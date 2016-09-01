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