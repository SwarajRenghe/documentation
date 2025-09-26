# Funda Assignment - CLI Project

Hello!

Thanks for taking the time to review my assignment. I hope you like it as much as I had fun writing it!

I decided to approach this as not just as a quick script, but as a small but structured .NET application with an interesting architecture.

## Table of Contents

- [Funda Assignment - CLI Project](#funda-assignment---cli-project)
  - [Table of Contents](#table-of-contents)
    - [A couple of notes](#a-couple-of-notes)
  - [A Rough Outline of the project](#a-rough-outline-of-the-project)
  - [Project Structure](#project-structure)
    - [1. **Funda.Domain**](#1-fundadomain)
    - [2. **Funda.ApiClient** – talking to the Funda API (HttpClient config, URL builder, rate limiting, retries).](#2-fundaapiclient--talking-to-the-funda-api-httpclient-config-url-builder-rate-limiting-retries)
    - [3. **Funda.Services** – business logic: calculating top makelaars, aggregating listings, etc.](#3-fundaservices--business-logic-calculating-top-makelaars-aggregating-listings-etc)
    - [4. **Funda.CLI** – entry point: parses command line, wires everything up, renders tables.](#4-fundacli--entry-point-parses-command-line-wires-everything-up-renders-tables)
  - [Feedback for Funda API](#feedback-for-funda-api)
  - [Testing Strategy](#testing-strategy)
    - [Unit tests](#unit-tests)
    - [Integration tests](#integration-tests)
    - [Some learnings for myself](#some-learnings-for-myself)
      - [1. Using a Sliding Window retry mechanism](#1-using-a-sliding-window-retry-mechanism)
      - [2. Cancellation \& cooperative async](#2-cancellation--cooperative-async)
      - [3. Configuration merging and layering](#3-configuration-merging-and-layering)
      - [4. Live Progress Reporting](#4-live-progress-reporting)
      - [5. Defensive validation on flags](#5-defensive-validation-on-flags)
  - [Things I would add to make this production ready](#things-i-would-add-to-make-this-production-ready)
  - [AI Usage](#ai-usage)
  - [Running Instructions](#running-instructions)
    - [0. --help flag to see all available options](#0---help-flag-to-see-all-available-options)
    - [1. --dry-run to see system defaults / default settings](#1---dry-run-to-see-system-defaults--default-settings)
    - [2. Top 10 makelaar's in Amsterdam that have the most objects listed for sale](#2-top-10-makelaars-in-amsterdam-that-have-the-most-objects-listed-for-sale)
    - [3. Top 10 makelaar's in Amsterdam that have the most objects listed for sale that have a tuin](#3-top-10-makelaars-in-amsterdam-that-have-the-most-objects-listed-for-sale-that-have-a-tuin)
    - [4. Top 3 makelaar's across all cities that have the most objects listed for sale](#4-top-3-makelaars-across-all-cities-that-have-the-most-objects-listed-for-sale)
    - [5. All possible options](#5-all-possible-options)
  - [Running with Docker](#running-with-docker)
    - [Build the image](#build-the-image)
    - [Run the CLI](#run-the-cli)
  - [Thought Process Steps](#thought-process-steps)

---

### A couple of notes

There are some small TODOs left in the code (e.g., making the CLI parser use attributes instead of a mapping dictionary). I left these in to show my thought process and where I’d take it next.

As I worked through the project, I also logged and documented my thinking and decisions / tradeoffs made, and divided this into steps of work. You can find this at the bottom of this file, collapsed, if interested.

## A Rough Outline of the project

1. A clean domain-driven structure (separate projects for Domain, APIClient, Services and CLI)
2. A fully robust API Client that respects rate limits, has a retry-mechanism, a queue to store pending requests, retries on transient failures, and handles pagination.
3. A fully featured CLI app that lets you configure virtually all features via flags (e.g., city, top N, API settings), and shows live progress + a clean table at the end of processing.
4. Configurable behavior via appsettings.json or command-line flags.
5. Some small extras like a dry-run mode to see options before execution, logging for errors, validations on user input.
6. A codebase that is testable and easy to evolve (interfaces everywhere, simple abstractions).

## Project Structure

I split the solution into four projects to keep concerns separate:

### 1. **Funda.Domain**

This is a space purely for shared models and definitions. This is also where business rules live, and all consumers of the Domain must follow it's spec.

I also chose a "edge-mapping" approach, where I expect each consumer to map it's own models to be compatible with the Domain.

### 2. **Funda.ApiClient** – talking to the Funda API (HttpClient config, URL builder, rate limiting, retries).

This is where the API Client lives, with all of it's configuration. This is what talks to the Funda API. It is an independent implementation that is injected into the project, and can be swapped out for a file system client, a cache client, a message queue client etc.

This client is built with a lean HTTP Client to be lightweight and quick, and supports sliding-window rate limiting, automatic retrying with exponential backoff, queuing failed requests, etc.

### 3. **Funda.Services** – business logic: calculating top makelaars, aggregating listings, etc.

This is where all business logic, or "use cases" live. This is what defines our task of top X makelaars, and contains utilities within itself for aggregating listings, generating mappings, etc. It also defines interfaces for other consumers to interact with each other, like tracking progress and defining a search criteria.

### 4. **Funda.CLI** – entry point: parses command line, wires everything up, renders tables.

This is the main entry point of the applicatoin. It is both the entry and the presentation layer. It contains tooling for parsing the command line, wiring everything up and presentation. It also defines configuration.

This too can be swapped out with a Web Application, a pipeline application, or a system orchestrator.

This separation makes it easier to test, easier to swap implementations, and just keeps things cleaner overall.

## Feedback for Funda API

If interested, I would like to point out 2 aspects of the API that could be improved

1. 401 UNAUTHORISED error when quota exceeded
   - A small point, but the API returns a 401 when too many requests are made instead of a `429 Too Many Requests` code. This may not be a default retry in many retry libraries, and can cause some extra work for your team.
2. The paginations accepts both 0 and 1 as valid starting points - it would be better to stick to either one.

## Testing Strategy

### Unit tests

There are some unit tests that focus on individual classes in isolation. This is great for testing correctness of logic, but also so that unintended changes to services in the future are caught.

There are also validator tests.

### Integration tests

There's a couple of example integration tests, where multiple layers are wired together and tested. I did this using a small DI container within the test, and real implementations of the classes.

### Some learnings for myself

#### 1. Using a Sliding Window retry mechanism

This is not something I have used before, so it was nice to learn. This is intended to prevent >N requests per minute. I found this very useful - it helps to not measure just number of requests per second but a sliding window approach. This way, I only allow 100 total requests over any 60 seconds, not per second.
I also added a retry-mechanism that automatically 429/408/5xx errors, that was built it. This could be configured based on requirements. It also comes built in with Exponential backoff, and jitter, and honors any (retry-after) headers from the API.

#### 2. Cancellation & cooperative async

I wired everything around a `CancellationToken`. This makes the app more robust if it needs to stop mid-way, and makes it possible then to roll back atomic actions if they were ever dispatched.

What was new to me was learning how to automatically test the behavior of a cancellation token. I was able to mock an artificial wait, and then assert that the cancellation token did in fact work.

#### 3. Configuration merging and layering

This is not a typical requirement in applications, but I did want to set the project up so that there were baked in defaults, but everything could also be overwritten by flags so that there isn't a need to recompile on changes. Given more time, I would have done this process more smartly - built some kind of annotation wrappers around `CommandLineOverrides` so that both descriptions, aliases and validations could be written very well and cleanly in one place. The nice thing is `ApiOptions` and `CliOptions` are different and can be independent.

#### 4. Live Progress Reporting

Also my first time using the `IProgress` class, for passing a progress tracker to a service, and having it treat the caller as a delegate and publishing progress. It's a neat feature built into C#, and makes use of passing a value by reference and multi threaded nature.

#### 5. Defensive validation on flags

Includes designing fast feedback on invalid flags, and also uses built in validations for checking that base url is indeed a HTTP Url, for example.

## Things I would add to make this production ready

1. Parallelisation

- For this assignment, my requests finish well within (100req/60s = 1.67req/s approx. = ). 625ms. If the latency per request jumped over this, I’d want to figure out a way to fire requests in parallel but still respect the sliding window limiter, to maximise performance throughput.

2. Opt-In Caching

- By an optional flag, we could add opt-in caching. This could be saving raw page responsesin a --cache folder. It is easy to add to the HTTP Client as an option, and a simple ICacheHandler.
- This can be very useful while development.

3. More test coverage,

- Especially around the ApiClientExtensions, since this is the critical piece where rate limiting, retries, and HttpClient lifetimes are wired up.

4. Policy-based timeouts

- Currently, HttpClient.Timeout covers both queueing and request execution. That means if a request is waiting in the queue, it uses up it's execution time. Handling them separately would be better.

5. Payload Compression

- A small, quick win. Because the Funda API responses are quite repetitive JSON. Enabling Accept-Encoding: gzip could cut payload size significantly and improve latency.

6. HTTP/2 Multiplexing

- Depending on the Funda API if it supports HTTP/2, we could take advantage of multiplexing multiple concurrent streams over a single connection. This is something I still have to learn about.

7. Metrics, Observability and Tracing

- It would have been nice to log simple metrics: total API calls, retries attempted, average latency, etc.

8. XML Support

- For this assignment, I only built JSON support. It will be easy to plug-in a XML formatter instead.

## AI Usage

I made judicious use of AI for the assignment - I find it a really nice tool to save some time doing repetitive tasks, and for understanding concepts and ideas clearly.

Specifically, I used AI for

- Brainstorming

In a design and planning phase, I laid out a outline for what I wanted the project to look like, and got feedback from AI. I also used it to clear my ideas around domain driven design. I also use it as a search engine.

```
Interesting note: I also noticed firsthand the limitations of AI, and dangers of over reliance. The [`System.Commandline`](https://learn.microsoft.com/en-us/dotnet/standard/commandline/) library is being fully rewritten, and does not even have a public build available (I have used the Release Candidate). AI was not aware of this library and it's usage at all.
```

- Github CoPilot

I find it to be a nice productivity boost, helps me type faster and focus my attention on more important aspects.

- Test Case Design and Sanity Checks

I find AI particularly good at helping come up with coverage criteria. It also helped me write the integration testing.

- Documentation

Writing this README

## Running Instructions

Populate `appsettings.json` with a valid URL and API Key, or supply via command line flags

Build: `dotnet build`

Compile to build an executable (macOS): `./compile.sh && cd dist`

#### 0. --help flag to see all available options

```
./dist/Funda.Cli --help
```

```
dotnet run --project Funda.Cli Funda.Cli --help
```

#### 1. --dry-run to see system defaults / default settings

```
./dist/Funda.Cli --dry-run
```

#### 2. Top 10 makelaar's in Amsterdam that have the most objects listed for sale

```
./dist/Funda.Cli --city amsterdam --top 10
```

#### 3. Top 10 makelaar's in Amsterdam that have the most objects listed for sale that have a tuin

```
./dist/Funda.Cli --city amsterdam --top 10 --with-tuin
```

#### 4. Top 3 makelaar's across all cities that have the most objects listed for sale

```
./dist/Funda.Cli --top 3
```

#### 5. All possible options

```
./Funda \
  --base-url "https://api.funda.nl/v1" \
  --api-key "your-api-key-here" \
  --rpm 60 \
  --retry-delay "00:00:02" \
  --max-connections-per-server 10 \
  --connection-lifetime "00:10:00" \
  --http-timeout "00:01:30" \
  --max-retries 3 \
  --city "amsterdam" \
  --listing-type "koop" \
  --with-tuin \
  --pagesize 25 \
  --top 15 \
  --outdir "./exports" \
  --dry-run
```

## Running with Docker

I included a tiny Dockerfile so you can even run the CLI inside a Docker container. This also makes the CLI feel like a native tool, and can be used for other things.

### Build the image

```
docker build -t funda-cli .
```

### Run the CLI

Examples:

```bash
# show help
docker run --rm funda-cli --help

# top 10 makelaars in Amsterdam
docker run --rm funda-cli --city amsterdam --top 10

# top 5 makelaars with tuin filter
docker run --rm funda-cli --city amsterdam --with-tuin --top 5
```

---

## Thought Process Steps

<details>
<summary>
Step 1: Build Domains - Used across project
</summary>

My final task is

- To find which makelaar's have the most objects listed for sale.
- To find which makelaar's have the most objects with a tuin listed for sale.

This implies I should start with the domains - Makelaar, Object, Listing (may be for sale, may be under option, may be sold).

`ASSUMPTION: I define a domain called Property - This is a better name than object, and does not specifically imply house or anything else`

`QUESTION: What is an object? a apartment/house? office? plot of land? or does it not matter?`
`QUESTION: Can only houses and offices have a tuin?`

Tuin, Amsterdam may be properties of the Object.

I will start with Makelaar, Property base class. I can include a PropertyType enum which can inform future operations. I will make assumptions on what properties may be mandatory.

If I think it necessary I will add inherited classes, (maybe a House class that inherits from Office (maybe Tuin is a property of House and Office))

</details>

<details>
<summary>
Step 2: Start with the API - DTOs, Mapping services
</summary>

Next step, I will define objects to consume the data from the API, as a first step towards building the API client. I'm not going to map the entire object, only the properties from the response that I think I will need. I will follow this up with mapping methods to map this to my domain.

`QUESTION: I'm not sure yet where to store the mapping - is that a API client concern or a Service layer concern. Or even in the Domain?`

`QUESTION: It's an interesting question on how to detect if a property is within Amsterdam? We have the Woonplats which looks like the city but it may be inacurrate, and we can double check it wit the geolocation info, postalcode, etc. This is the responsibility of the Service or Domain`

`ASSUMPTION: In the response, There's a globalID and a ID. I'll go with ID.`

`QUESTION: How should my main program consume the client? It will be a class library, so not standalone. Should it be injected? The HTTP client should be injected in my opinion.`

This API section is taking much longer than I initially thought. I'm having trouble cleanly building the URL. I did want to use the URL builder pattern, but I'm also passing a SearchCriteria option to it, and I'm not sure builder is supposed to work this way.

I'm going to stop doing this now. I'll just use a simple string builder, and come back later to write it with a Builder pattern.

`TODO: The above`

I'm also now realising I'm not sure where to build the URL - should it be contained within the APIClient? Or be injected?

</details>

<details>
<summary>
Step 3: Implementing a Program.cs - Dependency injection, creating the HttpContext and calling the API client
</summary>
I realise now that the FundaResponseMapper is misplaced. It should be moved to the service layer. I think this is clearer separation of concerns, and that the API can be made more generic.

I wondered for a second if I should build an interface for the new mapper that I will build, because it will probably handle lots of data, so to extend it in the future interfaces might be useful. But I also want my mapper class to be stateless and static, so an interface does not make sense here.

I have an interesting dilemma now - I was hesitant to reference the API project in the Services projcet. (this tightly couples Services to API). But the old option was to couple the API to the Service/Domain, which sounds worse. The right answer is probably to make a third project that actually handles mapping.

Does this mean SearchCriteria must stay in Domain too? That's incorrect, but it also does not sound like an API concern, if the API is intended to be generic and independent. What if I make it so?

I've done some cleanup over in the domains, and changed what references Domain.

The CLI project is looking well

</details>

<details>
<summary>
Step 4: Actually implementing the main Program
</summary>
I want to now start tying these lose ends together. The CLI needs to 
- Create the HttpContext (using values from appsettings)
- Needs to inject services to other services, so it should reference both the Funda.Services and Funda.ApiClient. Also Funda.Domains

I should register everything I'm doing as a service, and tie everything into Program.cs. I'd like to keep Program.cs tiny, and leave the logic of running the application itself outside it. I should also start the main CLI part of this, also in a separate class.

I wonder if I even need an appsettings file, if my intention is to run everything from the CLI. I definitely don't want to rely on Env variables. But I guess I don't want the user to have to pass every value from the CLI as a flag, so I still need appsettings for defaults.
So I will put some basic config into appsettings, I can change this later. Should I make a options type within CLI to parse it, or use the ApiClient's option type to parse it? I'm not sure, but I'll go with ApiClient's option type. I also need some better validation steps. Maybe it will handle automatically? When parsing JSON.

`QUESTION: How to handle validation here when parsing appsettings`
`QUESTION: Should I make different environments already?`

I will use Polly for retrying/reject handling. Added as dependency.

Should I also add some kind of authorisation service? So that the CLI can be run via API key? Or pick from appsettings? This would be a good `TODO`

I'm adding a very simple App.cs, it just calls the API and logs the results found. Pagesize = 25,for now I have a hardcoded SearchCriteria - but this should come from CLI, and maybe appsettings for defaults.

Nice! My API Call is working well, parsing is working well, and pleasantly surprisingly Polly is working well.

Hmm, maybe Polly is not working correctly just yet, it rate limited me and I seem to not have a handler for this just yet. For now, I can proceed.

I'll hold off on adding unit tests just yet on this - it's small already and it will change quickly

</details>

<details>
<summary>
Step 5: Revisitng Funda.ApiClient with a clear head
</summary>
Reading the code again is helpful. I can definitely see some things I want to change. I'd like to make the IApiClient more generic.

Bigger change - I'm thinking of moving back the responsibility of mapping to domain from Api models back to the API. I've just read something about "edge mapping", and if my types are strong AND I have decided that the Domain is the core of the design, then the API must adapt itself to return items in my domain language.
I think this makes sense, because All consumers of the Domain must behave and respond in the same way. So that's good.

I'm noticing a coupling dependency on my Services unfortunately. If my service must parse the stream sent back by the API, then it must either know the API, or the API must implement an interface provided by the Service. This is looking odd.

Is this leading me to a CQRS? Maybe "GetTop10Makelaars" is a command, and "QueryAllApiObjects" is a query? This might be nice but I don't want to implement CQRS for this assignment, it is overkill.

Okay, I'm going to keep the IPropertyListingsQuery in Funda.Services.Interfaces so that

1. The Funda.Domain stays as it it, only concerned about the domain objects
2. The Funda.Services defines use cases, and also owns the contracts required by the user cases

Any other consumer (non API, so could be a cache or a file system) must adapt to the use case

While I am udating the API client, I'm also fixing up the URLBuilder.

Refactored it, now I'm feeling a lot happier with that class.

I've also added a bunch of tests for the APIClient, and the URLBuilder. The main class within Funda.Api is now implementing the interface ApiListingsQuery.cs (this can be named a bit better). I will test this too.

Nice, I'm now very happy with the state of Funda.Api.
`TODO: I might want to come back and expand test suite of ApiListingsQuery.cs`

</details>

<details>
<summary>
Step 5: Implementing Funda.Services
</summary>
Now, I'm ready to write the core business logic of the assignment. I implemented a service ITopMakelaarService - this is for now quite simple code, I just parse the incoming data and create a map between <(Makelar) MakelarObject, (int) ListingCount>

I need to add a Equals and GetHashCode property to makelar, to be able to build a hashmap.

`QUESTION: Should I worry about loading into memory limits here?`

Final steps - wrapping up how Funda.Services is consumed by the CLI. Now, I would like to register a new use case, and call on it. The use case TopMakelaarsService will be given a stream or feed of data from the ApiClient, wired up using the CLI. This seems correct to me.

Wiring everything up to my dependency injection class

It works! Api results are being fetched, and my business logic is filtering them correctly.
I can revisit the algorithm but it is definitely performant.

`TODO: I have added everything in as singletons because I am building a command line tool, not a command line app if that makes sense. At this moment, there is only one action that will run per command, it is not an interactive command line app`

Ready to commit now, tests pass, functionality works.

I still have some code to cleaup, and to make the actual Command line parser. But we are in good shape now.

Oh BTW - Polly still does not work correctly. For now, I throttled to only 2 API calls and break. Need to fix this.

</details>

<details>
<summary>
Step 6: A Fully Working Project
</summary>
Success! It appears that everything is working correctly now, Polly Rate Limiting, Dependency Injection, tests green, and also the answer spit out by my program is correct! (I validated it also with a quick python script)

I'm off to a 4 day holiday now, but when I'm back I need to

- Build a simple but nice Command line utility with flags
- Write a solid README
- Configure Polly options via appsettings
- Generally fix appsettings
- Maybe a dockerfile?
- Review unit test coverage, maybe some E2E tests?
- Make some kind of architecture diagram
- Anything else I can think of.
</details>

<details>
<summary>
Enhancement 1: Tracking progress of the query
</summary>

The way I have built the feature - it must first pull all data and only then can it parse and reorder data. Because of this, it appears that the program is taking a while to run. I added a small, thin `IProgress<PaginationProgress>` wrapper around the main service, so it can communicate how many pages have been progressed so far. For now, it only records ListingsSoFar and Pages/TotalPages.

I need to figure out a nice way to render this on the CLI output, and if there is any more data to be shown (top makelaar so far, for example).

Also added some expanded tests for this.

</details>

<details>
<summary>
Enhancement 2: Code cleanup, API Options from appsettings + validation
</summary>
Some code cleanup, renamed some files. I need to start making it possible to add options via CLI and override the ones in appsettings.

I added one more field in appsettings - `MaxConnectionsPerServer`. I'm really not sure which of these proeprties should be able to be overridden by the CLI and which should not be, but let's figure that out too.

</details>

<details>
<summary>
Enhancement 3: Abstracted Validator logic to APIClient, and improved ApiClientExtensions readability
</summary>

Wrote some clean code in ApiClientExtensions file, so that it is very readable now. I also moved it all to the ApiClientExtensions file - so now all the ApiClient related config loading, setting up retry/timeout logic etc. is happening in one place. This also simplifies my main Program.cs

</details>
