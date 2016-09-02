# ElixirConf 2016

## TO WATCH:

- String Theory
- Building umbrella project
- Debugging techniques in Elixir
- No REST for the wicked: Building a GraphQL API
- Building Available and Partition Tolerant Systems with Phoenix Tracker.
- Collaborative Music with Elm and Phoenix
- Nerves + Phoenix Saves a Father's Sanity!

## Intro

- 542 attendees
- [Wombat](https://www.erlang-solutions.com/products/wombat-oam.html) by Erland Solutions

## Keynote Chris McCord

#### Phoenix 1.3

- web/ and "model" to die
- web to move inside /lib/my_app/web
- models will live in normal /lib folder
- Add `Web` namespace, e.g. `MyApp.Web.PageController`
- Generators will move controller code to models. e.g. `Blog.list_comments` and `Blog.create_comment`
- Responder callbacks for controller errors and coding only the happy path
- Phoenix Umbrella Projects. All microservices under one roof/repo
- Web/static => root assets folder

#### Future

- Service discovery accidentally made with Presence

## A tale of keys and values | Abstractions by Ernie Miller

- A choose your own adventure talk proposal
- Walkthrough history of light, photons, light waves, Schrodinger, etc.
- Usual Miller self, nothing about programming. Should have known better haha

## The future of deploying Elixir by Paul Schoenfelder

- Talks about what good deployments should be
- Author of [`exrm`](https://github.com/bitwalker/exrm)
- Author of [`distillery`](https://github.com/bitwalker/distillery)
  - [documentation](https://hexdocs.pm/distillery/getting-started.html)
  - pure Elixir
  - support for umbrella projects
- Not worth watching. He's just comparing `exrm` with `distillery`.
- Talks about all the problems he had with `exrm` and how bad it is
- [`edeliver`](https://github.com/boldpoker/edeliver) "edeliver is based on deliver enables you to build and deploy Elixir and Erlang applications and perform hot-code upgrades"
- Did manage to get Openshift and elixir working nicely
- Should not be doing hot upgrades with Docker containers. Should instead deploy to a different containers.
- Because you have hot upgrades you could be tempted not to use docker containers but the rest of your infrastructure could benefit from containerized architecture and so you should see the pros/cons of both approaches. Hot upgrades might be more trouble than they're worth.

## The Ghost of Object-Oriented programming

- Hugo's feedback: WAY too beginner. Not worth watching.

## Selling Food With Elixir by Chris Bell

- Application design & stories from production
- In Rails we would have used an Order model with Sidekiq for job processing
  - All state lives in the database, including any background processes.
  - Could do the same thing in Phoenix with `verk` and an Order model
- Elixir design principles
  1. Phoenix is not your application. (see changes in 1.3) It's just a web interface, not your system.
  2. Embrace state outside of the database. You can store state in your processes too.
  3. If it's concurrent, extract it into an OTP application. What would have been a Sidekiq job can be a different process.
  4. (Don't just) Let it crash. Handle known/expected failures.

#### Design
- Order Scheduler
  - Queues orders to send to store delivering orders 15 minutes before the timeslot.
  - Using `send_after` to recursively send a message until it's ready to process.
  - [`gen_retry`](https://github.com/appcues/gen_retry)
- Task Supervisor
  - `GenServer#terminate` allows you to do something before the process goes away.
- Store Availability
  - Track the capacity for a given store
  - Problem: high demand for timeslots during the lunch rush
  - Using the database would require too many calls. It'd be expensive because too frequent.
  - Using [ETS](http://erlang.org/doc/man/ets.html) (redis for erlang) Erlang Term Storage
    - Keeps availability slots per timeslots in ETS
    - `:ets.tab2list` to retrieve data
    - `:ets.update_counter` to update data
  - To hold a timeslot temporarily while the client fills in his order:
    - Create a process
    - Monitor the process (giving a ref)
    - `Process.send_after` will kill the process
    - When the process terminates, we listen to the "down" event.
    - The callback tells the `StoreManager` to remove the timeslot.
  - On boot, since ets doesn't persist after the process dies, we read from the database to recreate the ets table.
  - [`immortal`](https://github.com/danielberkompas/immortal)

#### Lessons
- Name your processes if you only want one per node. Otherwise you could duplicate things.
- Understand the process design and the bottlenecks of the libraries you're using
  - [`poison`](https://github.com/devinus/poison)
  - [`HTTPotion`](https://github.com/myfreeweb/httpotion)
  - [`HTTPoison`](https://github.com/edgurgel/httpoison)
- Feed work, don't read work
- Start with an umbrella app

## Phoenix Beyond the Browser - Realtime Applications with Phoenix and Swift by David Stump

- Why Phoenix?
  - Fast response time
  - fault tolerant
  - concurrent
  - scalable
  - maintainable
  - Phoenix Channels (WebSockets)
- Author wrote the Swift Websocket Client library
- Goes through introduction to Swift, to Phoenix and how to join the 2 with websockets.
- Unfortunately not very useful for work but the other talks weren't either.

## Measuring your Elixir Application by Renan Ranelli

- First half is about the "why" you should measure. Dah.
- Explaination of histograms, 95th percentile, etc.
- Using InfluxDB & Grafana!!
- Also using collectd as a influxdb plugin
- [`exometer` & `elixometer`](https://github.com/pinterest/elixometer)
- [Step by step demo](http://milhouseonsoftware.com/2016/05/08/measuring-your-elixir-application/)
- Nice trick: `sliently() { "$@" | /dev/null &1>2 }` # or something similar
  - e.g. `silently spawn_process &`
- [InfluxDB Kapacitor](https://influxdata.com/time-series-platform/kapacitor/) for alerting anomalies
- [Another blog post on gathering metrics](http://tech.footballaddicts.com/blog/gathering-metrics-in-elixir-applications)

## Lightning Talks

- [elixirstatus.com](http://elixirstatus.com/)
- [elixirweekly.net](https://elixirweekly.net/)
- [authy](https://github.com/schrockwell/authy) equivalent to `pundit` in elixir
- [12 years of progress](https://twitter.com/sstephenson/status/730039913052176384)
- [elm](http://elm-lang.org/)
- [Chaos Monkey](https://github.com/Netflix/SimianArmy/wiki/Chaos-Monkey)
- [principlesofchaos.org](http://principlesofchaos.org/)

## Keynote Jose Valim

- [GenStage](http://github.com/elixir-lang/gen_stage) & Flow
- Elixir goal's = Eager -> lazy -> concurrent -> distributed
- Eager: Good for small collection, fails when the set is too big.
  - Took too long on a big file
- Lazy: Split the work, don't load in memory, etc. e.g. `File.stream!` instead of `File.read!`
  - Item by item (folds computation), less memory usage at the cost of computation
  - Took 60 seconds on 2Gb
- Concurrent: Using `Flow`
  - `Flow` uses all cores, spawning processes for each data partition
  - Give up ordering and process locality for concurrency
  - Took 36 seconds on double core
  - It's not magic, overhead when data flows through processes
  - Requires volume/cpu bound work to benefit
- Distributed: Papers show that you actually don't need distributed in 40% to 80% of cases with big data.
  - The gap between concurrent and distributed is really small in Elixir
- Next thing: durability concerns

#### GenStage | Producer, Consumer, Dispatcher

- [Announcing GenStage](http://elixir-lang.org/blog/2016/07/14/announcing-genstage/)
- New behavior
- Could replace `GenEvent`
- Introduce `DynamicSupervisor`
- Exchanges data between stages transparently with back-pressure
- Breaks into producers, consumers and producer/consumer
- P -> P/C -> P/C -> P/C -> C (each is a stage)
- Back-pressure means that a stage can tell if it's under too much load (demand-driven)
- Allows you to never overflow your system
- See video for an implementation example
- Consumer can ask halfway through processing for more items from the producer so nothing ever halts
- `GenStage` Dispatchers: per producer, gets demand and sends data, can dispatch to multiple consumers at once.
  - Default Dispatcher : Makes sense if you have multiple consumers for a single producer. Dispatcher will send data to the consumer with the highest demand.
  - BroadcastDispatcher : can reach all consumers regardless of demand.
  - PartitionDispatcher : splits evenly to each consumer.

#### Flow

- Flow is ~1200 LOC but really only ~80 LOC because it leverages `GenStage`
- Embraces map/reduce pattern. map -> partition -> reduce -> merge
- Windows & Triggers (when the data never finishes, e.g. RabbitMQ, Twitter feed)
  - See [Flow.Window documentation](https://hexdocs.pm/gen_stage/Experimental.Flow.Window.html)
- Configurable batch size (max & min demand)
- No distribution nor execution guarantees

## What is refactoring? by Gary Rennie

- Refactoring a TicTacToe game
- [Example online](https://github.com/Gazler/oxo)
- Elixir
  - Technique: if/cond to pattern matching
  - Technique: case to function
  - Technique: callback code to functions
  - Technique: case to pipeline
    - Each function in the pipeline now has to return the same structure for `|>` to work
  - Technique: pipeline to `with`
    - Matches on whatever you define, better than pipeline
  - Technique: Move code to struct modules (`defstruct`)
- Ecto
  - Use `Repo.aggregate` for counting
  - Use `Ecto.Multi`
- Phoenix
  - Same optimization as in Rails with `get_user` `before_action` callback instead using `plug`

## Leveling Up with Ecto by Darin Wilson

- [Code example](https://github.com/darinwilson/music_db)
- [Slides](https://dl.dropboxusercontent.com/u/14884175/leveling_up_with_ecto.pdf)
- [documentation](https://hexdocs.pm/ecto/Ecto.html)
- Ecto is a toolset (not an ORM) for interacting with external datasources
- The Big 4: Repo, Query, Schema, Changeset
- Repo in Phoenix is not Ecto.Repo but MyApp.Repo
  - Which means you can add your own custom functions in Repo
  - all, insert_all, update_all, delete_all are default
- Query
  - DSL that resembles SQL
  - Composable
  - Have to be explicit with `select`
  - Have to correctly cast the types (without schema)
- Schema
  - Defined in your 'models'
  - Not that good when building queries for reporting
  - Really useful for associated records ('has_many')
  - Ecto won't fetch associated records unless you ask it to.
    - you can use `preload: [<assoc>]` or
    - ```
      Artist
      |> Repo.get(1)
      |> Repo.preload(:albums)
      ```
- Changeset
  - `Repo.insert(changeset)`
  - Ships with validations
  - Constraints: same as validations, but verify at the DB level
    - e.g. `unique_constraint`
    - Useful to avoid rescuing errors from the DB and expecting certain behavios
    - Loads errors just like validations
  - Doesn't have to be bound with Schema. You can create a schemaless changeset.
- Phoenix Integration
  - `phoenix_ecto` provides the glue between the 2
  - `form_for` uses ChangeSet
  - Works great when you have a 1 to 1 to 1 between forms, database structure and UI
  - Using `Changeset.get_field` and `.put_change` you can decouple UI and DB
  - Schema don't need associatied database tables with `embedded_schema`. More [here](http://blog.plataformatec.com.br/2016/05/ectos-insert_all-and-schemaless-queries/)
- There is no one good way to do something with Ecto => Flexibility

## Concurrent Feature Testing with Wallaby by Chris Keathley

- Manages multiple browsers, concurrent, assumes async interfaces
- Sessions
  - Test -> Wallaby -> [Browsers] -> Phoenix -> Ecto
  - Wallaby gives a pool of browsers for you to requests
  - So your test talks to the browser directly after
  - Your session is setup with Phoenix to be isolated with ownership of transactions all the way to Ecto.
- Queries & Interactions are pretty much the same as Capybara