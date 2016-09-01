## Intro

- Wombat by Erland Solutions

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

