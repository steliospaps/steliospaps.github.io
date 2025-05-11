# About

My note s and takeways from [devoxUK2025](https://www.devoxx.co.uk/talks-by-tracks/?id=1251)

# Sessions

## Keynotes

### Communitaction

Communicating to business, should start from Why, then proceed to what.

Why has to be a business benefit.
I was aware (from my time in the bank) about

- Make us moeny
- Stop us from losing money
- Stop us from going out of business

I learned about:

- Beat the competition.

### Experimental sprints

- Define clear outcome predictions
- Timebox

## Concerto for Java and AI - Building Production-Ready LLM Applications - Thomas Vitale

https://www.devoxx.co.uk/talk/?id=3804

- used ollama
- spring-ai
  - chatclient can build complex prompts
  - RetrievalAugmentedAdvisor `chatClient.prompt().advisors()`
- Used Ai to extract tags for musical components
- Used Ai to propose components for the musical composition of a movie
- Used Ai to text to speech, then extract director notes for the musical pieces

Links:

- bluesky: @thomasvitale.com
- https://thomasvitale.com

- https://github.com/ThomasVitale/llm-apps-java-spring-ai
- https://github.com/ThomasVitale/concerto-for-java-and-ai

## Top REST API Design Pitfalls - Victor Rentea

Tools to detect implemtnation drift:

- https://pact.io
- https://spring.io/projects/spring-cloud-contract

Concepts:

- Scalability
  - perfromance
  - behaviour
- DTO: don't use use Request/Response
- MapStruct (or other mappers)
  - When mapping is painfull get rid of it and switch to manual
- CQRS
  - separate read/write models
  - Commands for write vs Queries for read
- no result for PUT, ids only for POST
- Action buttons with dedicated endpoints over edit screens
- All errors at the same response
  - https://www.rfc-editor.org/rfc/rfc9457.html

Links:

[slides](https://drive.google.com/file/d/1cKOp7QVBx3iC7OlKyr_QC2ol_9k_LoM-/view)

twitter: @vistorrentea

## Mini Lab- Create AI-Infused Apps with LangChain4j (and Quarkus) - Kevin Dubois

Used Quarkus to play around with an LLM

- https://quarkus.io/quarkus-workshop-langchain4j/
- (actual workshop) https://quarkus.io/quarkus-workshop-langchain4j/requirements/

Note:
For podman in linux you have to export a property in the environment: https://quarkus.io/guides/podman. Also remember to qualify images with docker.io/xxx

```
export DOCKER_HOST=unix://$(podman info --format '{{.Host.RemoteSocket.Path}}')
```

I could not make open telemetry work. (micrometer and quarkus were not playing nicely together)

- use llm as guardrail against prompt injection
- RAG
- MCP
- prompt class will instruct LLm to only answer in the expected format
- if return type is `Stream<String>` the LLm si streaming responses back

## What is multimodal RAG, and can we build a village with it? - Alexander Chatzizacharias

- https://github.com/glycin/rag-village
- RAG term was invented by Patrick Leweis in 2005
- LLama model
- CLIP multimodal embedding model - iamges
- CLAP multimodal embedding model - audio

## Functional Programming Code Smells - Victor Rentea

- https://github.com/victorrentea/clean-code-java/tree/devoxx-uk-2025
- https://github.com/uber/NullAway
- Jpa Repositories can return stream (but then processing happens inside the transaction)

## Patterns of Legacy Displacement - Rob Horn

- https://www.devoxx.co.uk/talk/?id=21369
- [Rob Horn](https://www.linkedin.com/in/rob-horn)

- incremental displacement
- transitional architecture
- event interception
- legacy mimic
  - service providing
  - service concuming
- Take business in a journey with you, show value
- DBUnit, transaction capturing

## Java 21: The revolution of virtual threads - Christian Worz

- https://www.devoxx.co.uk/talk/?id=8052
- https://twitter.com/@chwoerz
- Java 21
- (reminder for me to use Jmeter)
- `Thread.ofPlatform()... Thread.ofVirtual()`
- spring web: `spring.threads.virtual.enabled=true` (off by default to nnot break the 1% of projects that could be impacted)
- StructuredTaskScope
  - ThreadLocal does not work as expected there (https://www.youtube.com/watch?v=A-za5Rxxh5o, https://typed.rocks/st)
  - Extend to access handleComplete
  - synchronized correct after java 24
  - Native calls pins thread

https://github.com/typed-rocks/virtual-threads

## Write Once, Deploy Everywhere: From Containers to Serverless - James Ward, Matthew Meckes

https://www.devoxx.co.uk/talk/?id=22657

https://www.linkedin.com/in/jamesward, https://bsky.app/profile/jamesward.com, https://twitter.com/_JamesWard

https://www.linkedin.com/in/mmeckes, https://bsky.app/profile/mmeckes, https://twitter.com/mmeckes

- Jib for pacjaging (best for java)? vs BuildPacks, vs Docker
- EKS automode
- code for both standalone and lambda: https://github.com/aws/serverless-java-container
  - many endpoint to one lambda
  - possbile all GET in one lambda all modifications in another (so that they can run with different privileges)
- https://github.com/jamesward/springboot_bars/tree/lambda-web
- SnapStart https://docs.aws.amazon.com/lambda/latest/dg/snapstart.html
  - consider DB Priming
- https://catalog.workshops.aws/java-on-aws
- aws `q chat --non-interactive ...` https://aws.amazon.com/q/
- Test Containers
  - Use `@RestartScope` to limit the restarts/creations during the test (other wise OOM?)

## Spring Boot testing: Zero to Hero - Daniel Garnier-Moiroux

- https://www.linkedin.com/in/garniermoiroux, https://twitter.com/@kehrlann
- https://github.com/Kehrlann/spring-boot-testing
- use `maven sprinb-boot:test-run` -`MockMvcTester` autowired bean allows testing
- `@SpringBootTest`
- `@AutoconfigureWebMvc`
- `org.htmlunit` `WebClient` to test page and run it (not as good as selinium but good for simple stuff)
- Trick: greeakpoint for one thread only inside the unittest, and then you can observer running server and iterract with it and its state
- Test Context
  - activate debug logs for DefaultContextCache
- Test slices
- '@ExtendWith(CaptureOutput.class)` (or something) to capture the output (including logs) so you can verify that auditing was logged.
- assertJ `assertThat().extracting().filtering()`
- `@WewbMvcTest` creates only some beans (controllers etc) you have to mock the rest
- TestContainers
  - initializr will get the right test containers for your depednecies
  - `spring.docker.compose` allows you to configure paramters for local runs (TestContiners will pick thme up?)
  - `@ServiceConnection` when the testcontainer bean is created
  - `@TestCOnfiguration(processBean=false)` make the bean return a static object (to not create them many times per test)
  - `@DynamicPropertySource` can interogate the container object for parameters
- Security
  - `@WithMockUser` will inject the user access_token (username, roles fluent interface)
  - `spring-security-test`
  - `logging.level.org.springsecurity=TRACE` (???)
  - `MockMvcTester.post().with(SecurityMockMvcRequestPostProcessor.csrf())`
    - thymeleaf page can have form with \_csrf.xxxName \_csrf.Value
    - MockMvcTester.post()(?).oidcLogin()..idtoken().claim("email","aaa@bbb.com")

## Understanding RAG Techniques and MCP - PRATIK PATEL

- https://www.linkedin.com/in/prpatel, https://bsky.app/profile/prpatel.dev, https://twitter.com/prpatel
- https://www.devoxx.co.uk/talk/?id=40203
- similarity metrics
  - cosine
  - dot product
  - euclidian distance
- customisation
  - finetuning / Lora
  - Agents
  - prompt injection / RAG
- Naive RAG
- contextual retrieval
- ollama + Gemma3
- PRPatel free talks (his company pays for him to come)
- Chunking eithe say 100 bytes+20 bytes overlap, or smarter (say sentence by sentence)
- examples of various rag techgniques https://github.com/FareedKhan-dev/all-rag-techniques
- (lab) https://github.com/prpatel/ai-rag-minilab
- good ui `n8n` https://n8n.io/

## Hello, Quantum World! - Jules May

- https://www.linkedin.com/in/jules-may
- O(n)< O(n^k) < O(e^n) (non deterministacally polynomial) < O(e^n) proof< ??? (traveling slesman?)
- SuperPosition and colapse
- increase in bits still following morre's law (2x every 18 months) no theoretical limit yet
- Bosons, qubits
- Bloch sphere
- Lie field
- https://quantum.ibm.com

## 30 minutes to understand MCP (Model Context Protocol) - Sébastien Blanc

- https://twitter.com/sebi2706
- https://www.devoxx.co.uk/talk/?id=23332
- MCP is both tools and prompts, JSON RPC 2.0
  - tools/list
  - tools/call
  - prompts/list
  - prompts/get
- demo (ollama)
- https://github.com/sebi-and-horacio/mcp-quarkus-lab

## GraalVM in action: Building a Polyglot Rule Engine for Dynamic Business Logic - Rick Ossendrijver

- https://www.devoxx.co.uk/talk/?id=6502
- https://www.linkedin.com/in/rick-ossendruhver
- https://twitter.com/@rick_ossies
- alternatives
  - [drools](https://www.drools.org/)
- GraalVM polyglot can run pyuthon or js inside java with limited privileges
- Graalv Context, proxyobject
- event based system.
- all system is live.
  - dry run
  - versioning
  - code lives in DB
  - rate limiting
  - loops possible (inf recursion)

## Are Your Tests Slowing You Down? - Trisha Gee

- https://www.devoxx.co.uk/talk/?id=22054
- https://www.linkedin.com/in/trishagee
- https://bsky.app/profile/trishagee.bsky.social
- https://jvm.social/@trisha_gee
- https://twitter.com/trisha_gee
- Develocity (from gradle)
- @DispayName for tests
- intelij ai: type questions and then tab
  - can write tests but not so good, better for edge cases
- Flaky tests quarantine @ignore until?
- maven build cache
- develocity or apache
- develocity offers remote build cache (populated by CI)
- parallel testing
- ai predictive test selection
- to speedup start from the slowest tests
- refactor and delete

## Observability for All! - Carly Richmond

- https://www.devoxx.co.uk/talk/?id=13326
- [Carly Richmond](https://www.linkedin.com/in/carly-richmond)
- signals
  - logs
  - metrics
  - traces
- Core web vitals
  - LCP largest contentful point
  - INP interaction to next point
  - CLS cumulative layout shift
  - TTFB Time To First Byte(?)
- RUM Real User Monitoring
- opentelemtry.io web-vitals
- when local testing use a local nginx to add headers for CORS etc
- Syntheic monitoring
- PlayWright (like selenium?)
- SLOs Service Level Objectives

## Unlocking Generative AI in your Web App - Maximiliano Firtman (@FIRT)

- https://www.devoxx.co.uk/talk/?id=18784
- [linked in ](https://www.linkedin.com/in/firtman)
- learn pwa
- cloud ai
- on device ai
  - on browser
  - on platform
- Prompt engineering
  - require specific format
  - avoid prompt injection
- instructions
  - use delimiter for dynamic data
  - ask for specific foprmat
  - give examples
- Ai client side, web ai, build it
- agent
- mcp servers
- libraries+ frameworks
  - Transformers.js
  - WebLLM
  - WebAssembly
  - WebGPU
  - WebNN
- build-in ai
  - chrome (needs flags)
  - geminin ai nano (2GB)
  - js `window.ai.languageModel.capabilities`
    - create session
- Fellou
  -agentic browser

## Every Event, Everywhere, All at Once - Jacqui Read

- https://www.linkedin.com/in/jacquelineread

Event driven stuff

- Event has happened (past tense)

I did not know about EventObject in Java

- Event vs command
  - event has happened
    - result
    - immutable
    - in the past
    - does not care about consumers
  - command is a request for an action
    - intent

handler: listener+ callback

Event storming is a way to analyse/design systems (colors not important legent very important)

- orange: events
- green: opporutinities
- pink (45 deg rotated): Hotspot
- yellow actors
- purple system
- blue arrow voting (important to have the name/initial on arrow)

Pivotal events (public events), bounded context (between pivotal events)

3 flavours of event storming

- discovery oriented
- process modeling
  - design oriented
  - smaller group
- software design

Event sourcing: updates of each change. To get state at a point in time read all updates up to that time.

Domain events. (Domain Driven Design Orange book (free))

Integration events , dimain events

All events are domain events - itegration events (public) - private events (specific to each subsystem)

Event is in the continouum between notification (0 context/less data) and snapshot(100% context/more data)

- As small as possible
- as large as necessary

If you get only Id everyone has to read source to find out what changed.

Non event

# Stands

## DiffBlue

Automatic creation of tests for existing production code
[link](https://www.diffblue.com/)

## Unblocked

A.I. that provides context and answeres developers questions. Context: Documetntation, slack conversations, code.

- Slack integration (bot that responsds to questions in a thread)
- Data shield (verbally told: a way to isolate access to sensitive information to people allowed to see it. Answers can be shared with rest of team if deemed safe.)

## Vaadin

Browser components coded in java.

https://vaadin.com/

## webforj

like vaadin?

https://webforj.com/

## Webswing

like jpro one

# People

## JitterTed

https://ted.dev/
