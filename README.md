# Ecto

[![Build Status](https://travis-ci.org/elixir-ecto/ecto.svg?branch=master)](https://travis-ci.org/elixir-ecto/ecto)
[![Inline docs](http://inch-ci.org/github/elixir-ecto/ecto.svg?branch=master&style=flat)](http://inch-ci.org/github/elixir-ecto/ecto)

Ecto is a domain specific language for writing queries and interacting with databases in Elixir. It provides a standardised API for talking to different databases. It is a Language Integrated Query or [LINQ](https://en.wikipedia.org/wiki/Language_Integrated_Query). 

An example an Ecto schema definition:

```elixir
defmodule Sample.Weather do
  use Ecto.Schema

  schema "weather" do
    field :city     # Defaults to type :string
    field :temp_lo, :integer
    field :temp_hi, :integer
    field :prcp,    :float, default: 0.0
  end
end
```

Some examples of different query styles for the above schema:

```elixir
query = from w in Weather,
        where: w.prcp > 0 or is_nil(w.prcp),
        select: w
Repo.all(query)
```

```elixir
Weather
|> where(city: "Kraków")
|> order_by(:temp_lo)
|> limit(10)
|> Repo.all
```

## Documentation

* See the [online documentation](http://hexdocs.pm/ecto)
* [Run the sample application](https://github.com/elixir-ecto/ecto/tree/master/examples/simple)
* [Getting Started with Ecto Basics Guide](https://github.com/elixir-ecto/ecto/wiki/Ecto-Basics)

## Installation

This example assumes you're using PostgreSQL. If you want to use another database, just choose the proper dependency from the table in [Usage](#usage).

Add the following deps to your `mix.exs` file:

```elixir
defp deps do
  [{:postgrex, ">= 0.0.0"},
   {:ecto, "~> 2.0.1"}]
end
```

and update your applications list to include both projects:

```elixir
def application do
  [applications: [:postgrex, :ecto]]
end
```

Fetch the dependencies:

    mix deps.get

Configure the Ecto Repos for your application in `config/config.exs`:

```elixir
config :my_app, ecto_repos: [MyApp.Repo]
```

Then you can generate your Repo module like so:
  
    mix ecto.gen.repo

This will generate a Repo module and add appropriate configuration to `config/config.exs`. Adjust the `username` and `password` to match your PostgreSQL instance.

Now add a file `lib/my_app/app.ex`:

```elixir
# Taken from https://github.com/elixir-ecto/ecto/blob/master/examples/simple/lib/simple.ex#L1-L10
# Suspect this could be simpler...
#
# The `mix ecto.gen.repo` task suggests:
#
# Don't forget to add your new repo to your supervision tree
# (typically in lib/ecto2test.ex):
#
#    supervisor(Ecto2Test.Repo, [])
#
defmodule MyApp.App do
  use Application

  def start(_type, _args) do
    import Supervisor.Spec
    tree = [supervisor(MyApp.Repo, [])]
    opts = [name: MyApp.Sup, strategy: :one_for_one]
    Supervisor.start_link(tree, opts)
  end
end
```

Add a Schema

```elixir
defmodule MyApp.User do
  use Ecto.Schema

  schema "user" do
    field :name
    field :email
  end
end
```

* insert more steps from https://gist.github.com/joshprice/c9e46ecd31cbe2d1a180160b1fe0128b#model-related

## Usage

You need to add both Ecto and the database adapter as a dependency to your `mix.exs` file. The supported databases and their adapters are:

Database   | Ecto Adapter           | Dependency                   | Ecto 2.0 compatible?
:----------| :--------------------- | :----------------------------| :-------------------
PostgreSQL | Ecto.Adapters.Postgres | [postgrex][postgrex]         | Yes
MySQL      | Ecto.Adapters.MySQL    | [mariaex][mariaex]           | Yes
MSSQL      | Tds.Ecto               | [tds_ecto][tds_ecto]         | No
SQLite3    | Sqlite.Ecto            | [sqlite_ecto][sqlite_ecto]   | No
MongoDB    | Mongo.Ecto             | [mongodb_ecto][mongodb_ecto] | No

[postgrex]: http://github.com/ericmj/postgrex
[mariaex]: http://github.com/xerions/mariaex
[tds_ecto]: https://github.com/livehelpnow/tds_ecto
[sqlite_ecto]: https://github.com/jazzyb/sqlite_ecto
[mongodb_ecto]: https://github.com/michalmuskala/mongodb_ecto

We are currently looking for contributions to add support for other SQL databases and folks interested in exploring non-relational databases too.

## Important links

  * [Documentation](http://hexdocs.pm/ecto)
  * [Mailing list](https://groups.google.com/forum/#!forum/elixir-ecto)
  * [Examples](https://github.com/elixir-ecto/ecto/tree/master/examples)

## Contributing

Contributions are welcome! In particular, remember to:

* Do not use the issues tracker for help or support requests (try Stack Overflow, IRC or mailing lists, etc).
* For proposing a new feature, please start a discussion on [elixir-ecto](https://groups.google.com/forum/#!forum/elixir-ecto).
* For bugs, do a quick search in the issues tracker and make sure the bug has not yet been reported.
* Finally, be nice and have fun! Remember all interactions in this project follow the same [Code of Conduct as Elixir](https://github.com/elixir-lang/elixir/blob/master/CODE_OF_CONDUCT.md).

### Running tests

Clone the repo and fetch its dependencies:

```
$ git clone https://github.com/elixir-ecto/ecto.git
$ cd ecto
$ mix deps.get
$ mix test
```

Besides the unit tests above, it is recommended to run the adapter integration tests too:

```
# Run only PostgreSQL tests (version of PostgreSQL must be >= 9.4 to support jsonb)
MIX_ENV=pg mix test

# Run all tests (unit and all adapters)
mix test.all
```

### Building docs

```
$ MIX_ENV=docs mix docs
```

## Copyright and License

Copyright (c) 2012, Plataformatec.

Ecto source code is licensed under the [Apache 2 License](LICENSE.md).
