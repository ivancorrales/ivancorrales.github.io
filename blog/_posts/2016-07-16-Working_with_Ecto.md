---
layout: post
permalink: /blog/elixir-ecto
title:  Getting started with Elixir - Ecto
description: > 
  Working with Ecto.
tags:
  - Elixir
  - Ecto
  - mix
accent_image: 
  background: url('/assets/img/blog/jj-ying.jpg') center/cover
  overlay: false
accent_color: '#ccc'
theme_color: '#ccc'
hide_last_modified: true
comments: true
---

# Getting started with Ecto (Elixir)

* toc
{:toc .large-only}

## Purpose

 The purpose of this article is sharing with other geeks my experience developing an application with Elixir.  A few months ago I just did
 some researches in coding with Erlang and to be honest I really enjoyed it writing the article *[First steps with Erlang](http://www.wesovilabslabs.com/erlang/demo/tutorial/example/sample/2016/01/29/First-steps-with-erlang/)*, but this time  I will go for a more real application which could provide me with some better feedback
 about this functional programming language.
 
## What's Elixir?

Elixir is a dynamic, functional language designed for building scalable and maintainable applications and run over the Erlang Virtual Machine

## What's Ecto?

Ecto is a database wrapper that makes the life easier for Elixir developers who need to make use of database in their applications. Ecto works with **Postgresql**,
 **Mysql**, **MSSQL**, **SQLite3** and **MongoDB**.
  
In this tutotial we will be working with a Postgresql but we could do de same with any of the above databases.

## Creating the project

By this point I assume you all have elixir installed on your computer. If not, you only need to got to the [Elixir Site](http://elixir-lang.org/install.html])and follow the installation steps. 

**Create the project**

      mix new -app <APP_NAME>

![Result](../../../../../../../assets/images/posts/elixir/create_project.png)

This will create some directories:
  
  +    **_build**: This directory contains only compiled code and libraries.
        
  +    **config**: Configuration for our application.
        
  +    **deps**: Dependencies used by this application. 
        
  +    **lib**: The code of our project.
        
  +    **test**: Unit testing for our application
        
  +    **mix.exs**: In this file we define the dependencies and the application modules.
        
  +    **priv/repo**: SQL scripts and Elixir migration scripts.

**Adding dependencies**

Since we are working with Postgresql we need to add the driver beside of the Ecto dependency.
 
 So in the mix.exs file we add the below
 
  ```elixir
  ...
  def application do
    [applications: [:logger, :postgrex, :ecto]]
   end
    
  defp deps do
  [
    {:postgrex, ">= 0.0.0"},
    {:ecto, "~> 2.0.0"}
  ]
  end
  ...
  ```

and to downlaod dependcy we run the command

      mix deps.get
      
**Configuring our database connection**

First af all we create a Repo for our application. We create a new file in directory lib,
 
 **/lib/repo.exs**
 ```elixir
 defmodule ElixirEctoTraining.Repo do
   use Ecto.Repo,
   otp_app: :elixir_ecto_training
 end
 ```

and then we edit file **/config/config.exs** to define the database driver connection. For this tutorial we assume
a postgresql instance run on port 3001 (we can make use of **docker-compose.yml** in docker directory)

```elixir

  config :elixir_ecto_training, ecto_repos: [ElixirEctoTraining.Repo]
  
  config :elixir_ecto_training, ElixirEctoTraining.Repo,
    adapter: Ecto.Adapters.Postgres,
    database: "elixir_ecto_training_db",
    username: "wesovi",
    password: "wesoviPass",
    hostname: "localhost",
    port: "3001"
```

## Ecto tasks

Ecto provides with samo taks which we can find running

       mix ecto --help

![Result](../../../../../../../assets/images/posts/elixir/ecto.commands.png)

Creating the database by running **ecto.create**
  
![Result](../../../../../../../assets/images/posts/elixir/ecto.create.png)
  
Defining the tables structure.
  
We need to create a file called **/priv/lib/structure.sql**. So far we will only create a table to store tracks.
      
```sql
CREATE SEQUENCE tracks_id_seq;
      
CREATE TABLE tracks (
  id bigint NOT NULL DEFAULT nextval('tracks_id_seq'),
  title   VARCHAR,
  inserted_at timestamp without time zone
);
      
ALTER SEQUENCE tracks_id_seq OWNED BY tracks.id;
      
      
ALTER TABLE ONLY tracks
  ADD CONSTRAINT tracks PRIMARY KEY (id);
      
```

To create the tables we only need to run command **ecto.load**
    
![Result](../../../../../../../assets/images/posts/elixir/ecto.create.png)

We can check the table has been created by using any Postgresql Admin Tool,


Oooops... we forgot defining a column singer in table tracks... What can we do now?, We well generate a new migration for do this.

![Result](../../../../../../../assets/images/posts/elixir/ecto.gen.migration.png)

We need to edit new migration file in order to write our sql change.
```elixir
defmodule ElixirEctoTraining.Repo.Migrations.AddColumnSingerToTableTracks do
  use Ecto.Migration

  def change do
    alter table(:tracks) do
        add :singer, :string
    end
  end
end
```

We can have a look at all the migrations and theis statuses by running **mix ecto.migration**

![Result](../../../../../../../assets/images/posts/elixir/1ecto.migrations.png)

To make the change defined in the migration file we can do by **mix ecto.migrate -v 20160717214057**

![Result](../../../../../../../assets/images/posts/elixir/mix.ecto.migrate.png)

And the new column singer has been added to the table tracks.

## Mapping tables to elixir structures

We create a new file **/lib/model.exs** and define a new module as below:

```elixir
defmodule ElixirEctoTraining.Track do
  use Ecto.Schema
  import Ecto.Query


  schema "tracks" do
    field :title
    field :singer
    field :inserted_at
  end

end
```

## Testing the application
We modify mix.exs file and add the below content

```elixir
def application do
    [
      applications: [:logger, :postgrex, :ecto],
      mod: {ElixirEctoTraining,[]}
    ]
end
```

In order to run our tests agains a empty database we will create  an **aliases** in out mix.exs file

**What's an aliases** 
Aliases allow us to perform some tasks that contain sub-tasks

```elixir
  ...
  
  def project do
      [app: :elixir_ecto_training,
       version: "0.1.0",
       elixir: "~> 1.3",
       build_embedded: Mix.env == :prod,
       start_permanent: Mix.env == :prod,
       deps: deps(),
       aliases: aliases]
    end
    
    ...


  defp aliases do
      [
        "ecto.rebuild": [
            "ecto.drop", "ecto.create",  "ecto.load", "ecto.migrate", "test"
        ],
        "test": [
            "ecto.rebuild"
        ]
      ]
    end
```


We can write a very basic test. The purpose of this test is check tha following: We have an empty table tracks and add a new record then we have 1 record.

So we create a file **/test/elixir_ecto_training.ex** and with the below content

```elixir
defmodule ElixirEctoTrainingTest do
  alias ElixirEctoTraining.Track
  alias ElixirEctoTraining.Repo
  import Ecto.Query

  use ExUnit.Case, async: true

  setup do
    :ok = Ecto.Adapters.SQL.Sandbox.checkout(Repo)
    Ecto.Adapters.SQL.Sandbox.mode(Repo, {:shared, self()})
  end

  test "track insertion" do
    previousNumberOfTracks = List.first Repo.all(from u in Track, select: count(u.id))
    Repo.insert! %Track{ title: "Runaway", singer: "John Bon Jovi" }
    numberOfTracks = List.first Repo.all(from u in Track, select: count(u.id))
    assert numberOfTracks == previousNumberOfTracks + 1
  end

end


```

To run the tests we do
      
          mix test
    

![Result](../../../../../../../assets/images/posts/elixir/mix.test.png)

## The code

As always the code can be found on **[wesovilabs repository](https://github.com/wesovilabs/elixir_ecto_training)**

## Links of interest

+ [Elixir documentation](http://elixir-lang.org/getting-started/introduction.html)
+ [Ecto Documentation](https://hexdocs.pm/ecto/Ecto.html)


