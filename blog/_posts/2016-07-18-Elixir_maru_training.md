---
layout: post
permalink: /blog/elixir-maru
title:  Getting started with Elixir - Maru
description: Introduction to Maru
tags: 
  - Elixir
  - Maru
accent_image: 
  background: url('/assets/img/blog/jj-ying.jpg') center/cover
  overlay: false
accent_color: '#ccc'
theme_color: '#ccc'
hide_last_modified: true
comments: true
---

# Getting started with Maru (Elixir)

* toc
{:toc .large-only}

## Purpose

  In this post we learn how to implement a REST API with Elixir. For this, we will make use of Maru is a micro framework for Elixir
  insspired by grape.
  
  In the previous post  **[Getting started with Elixir - Ecto](http://www.wesovilabs.com/exto/elixir/mix/2016/07/16/Working_with_Ecto/)** we just defined
  some basic concepts about Elixir so from now on we will skip some basic details.
  
## Setting up project

  Since we just created a project in a  previous post we will add to this project as a dependency
  
  **mix.exs**
  ```elixir
    ...
    def application do
        [applications: [:logger, :maru, :elixir_ecto_training]]
     end
    ...
    defp deps do
      [
          {:maru, "~> 0.10"} ,
          {:elixir_ecto_training, git: "https://github.com/wesovilabs/elixir_ecto_training.git", tag: "0.1.0"}
      ]
    end
  ```
  
## Maru configuration

  We will make the below configurations:
  
  + We can configure the port where the application will run on.
  + We add versioning capability to our Rest API.   
 

**/config/config.ex**

```elixir
use Mix.Config

config :maru, ElixirMaruTraining.API,

    versioning: [
        using: :path
    ],
    http: [port: 5001]
    
```


## Rest API version

Maru provides us with three different ways to implement Rest API
 
 + By path
 + By param
 + By Accept version header
 
The **[Maru documentation](https://maru.readme.io/docs/versioning)** is really clear and they provide with so I invite you to have a look at it.

## The API
  
  First of all we define an API module. This module is the one that we stated in the **config.ex**, and we define some global properties for our endpoints
  
  ```elixir
    before do
        plug Plug.Logger
        plug Plug.Parsers,
            pass: ["*/*"],
            json_decoder: Poison,
            parsers: [:json]
      end
  ```
  
  Maru allow us to intercept exceptions and allow us to transform the response message by making use of **rescue_from**, see below:
  
   ```elixir
    rescue_from Unauthorized, as: e do
      IO.inspect e
      conn
      |> put_status(401)
      |> json(%{message: "This place is not for you,  you are unauthorized"})
    end
      
    rescue_from Maru.Exceptions.NotFound, as: e do
      Logger.debug "404: URL Not Found at path /#{e.path_info}"
      conn
        |> put_status(404)
        |> json(%{message: "Hey budy you have no idea where you want to go"})
    end
      
    rescue_from :all, as: e do
      IO.inspect e
      conn
        |> put_status(500)
        |> json(%{message: "Something went bad! And I do not know what..."})
    end
    ```
  
  
  Since we are making use of versions we will implement a couple of routers (v1 and v2) and this routers are referenced from API
  
  ```elixir
    mount ElixirMaruTraining.TrackRouterV1
    mount ElixirMaruTraining.TrackRouterV2
  ```
    
 Then the ElixirMaruTraining.API module look  like this
  
  ```elixir
  defmodule ElixirMaruTraining.API do
    use Maru.Router
    require Logger
  
     before do
        plug Plug.Logger
        plug Plug.Parsers,
            pass: ["*/*"],
            json_decoder: Poison,
            parsers: [:json]
      end
  
      mount ElixirMaruTraining.TrackRouterV1
      mount ElixirMaruTraining.TrackRouterV2
  
      rescue_from Unauthorized, as: e do
          IO.inspect e
          conn
          |> put_status(401)
          |> json(%{message: "This place is not for you, you are unauthorized"})
      end
  
      rescue_from Maru.Exceptions.NotFound, as: e do
            Logger.debug "404: URL Not Found at path /#{e.path_info}"
            conn
            |> put_status(404)
            |> json(%{message: "Hey budy you have no idea where you want to go"})
      end
  
      rescue_from :all, as: e do
          IO.inspect e
          conn
          |> put_status(500)
          |> json(%{message: "Something went bad! And I do not know what..."})
      end 
  end
  ```
    

## Resources

For this example we define a new structure in our code. This will be the response structure that our services will return.
  
  **/lib/elixir_maru_training/resources.ex**
  
  ```elixir
  defmodule ElixirMaruTraining.TrackResource do
  
      defstruct [:title, :singer]
  
  end
  ```
  
  
  
## Routers
  
In other programming languages are known as controllers or even services. Basically a router is the place where we define the endpoints for our applications
  
In the API we defined pointed to the Routers: 
  
+ **TrackRouterV1**: This version will available a couple of services which will return mocked data. 
    
    + GET /v1/tracks - Return the list of mocked tracks
    + POST /v1/tracks - Add a new track and return a 204 http status code
 
    The router is implemented in **/lib/elixir_maru_training/router/track_router_v1.ex**
    
    ```elixir
    defmodule ElixirMaruTraining.TrackRouterV1 do
      use Maru.Router
      require Logger
      alias ElixirEctoTraining.Track, warn: true
      alias ElixirMaruTraining.TrackResource
      def allTracks do
        [
            %TrackResource{title: "Todos los dias sale el sol", singer: "Bongo Botrako"},
            %TrackResource{title: "Mi jefe", singer: "Mojinos Escozios"}
        ]
      end
    
      namespace :tracks do
    
        version "v1" do
    
           @desc "Return the list of mocked tracks"
          get do
            tracks = allTracks()
            Logger.debug "Tracks #{inspect tracks}"
            conn
            |> put_status(200)
            |> json(tracks)
          end
          
          
          @desc "Add a new track and return a 204 http status code"
          params do
            requires :title, type: String
            requires :singer, type: String
            optional :score, type: Atom, values: [:bad, :normal, :good, :awesome], default: :normal
          end
          post do
              track = %Track{title: params[:title], singer: params[:singer]}
              conn
              |> put_status(204)
              |> json(%{})
          end
        end
      end
    
    end
  ```
  
  In the above example we make use of the Maru Pipelines
  ```elixir
   conn
    |> put_status(200)
    |> json(tracks)
  ```
  
  as all of us can imagine what we are doing is return a 200 http status code and the body will be a list of track resources.
  
  For the **POST** endpoint we need to know the request body, and we do it by defining a parameter as see in the example
  
  ```elixir
  params do
    requires :title, type: String
    requires :singer, type: String
    optional :score, type: Atom, values: [:bad, :normal, :good, :awesome], default: :normal
  end
 ```
 this is awesome since we can even define enum values, set the attribute nature, set if the pattribute is optional or required...
 
 
 We can check the service are working by running the below commands:
 
    curl -XGET http://127.0.0.1:5001/v1/tracks  -H "accept: application/json" -i
    
    curl -XPOST http://127.0.0.1:5001/v1/tracks -i -H "accept: application/json" -H "Content-Type:application/json" --data '{"title":"La Macarena", "singer":"Los del rio"}'
 
+ **TrackRouterV2**: This version will again implement the get tracks and create new track services but this time we will make use of
**[elixir_ecto_training](https://github.com/wesovilabs/elixir_ecto_training)** project. To sum up this time we will work with real data.
  
  The code is below.
  
  ```elixir
  
  defmodule ElixirMaruTraining.TrackRouterV2 do
    use Maru.Router
    import Ecto.Query
    require Logger
    alias ElixirEctoTraining.Track, warn: true
    alias ElixirEctoTraining.Repo, warn: true
  
  
    def allTracks do
      query = from track in ElixirEctoTraining.Track,
          select: (
              %{ trackId: track.id, title: track.title, singer: track.singer}
          )
      query |> ElixirEctoTraining.Repo.all
    end
  
    def inserTrack(track) do
      ElixirEctoTraining.Repo.insert(track)
    end
  
    namespace :tracks do
  
      version "v2" do
  
          @desc "Return the list of tracks in the database"
          get do
              tracks_as_json = allTracks()
              |> List.wrap
              |> Poison.encode!
              Logger.debug "Tracks #{inspect tracks_as_json}"
              conn
              |> put_status(200)
              |> json(tracks_as_json)
          end
  
  
  
           @desc "Add a new track and return a 201 http status code"
           params do
              requires :title, type: String
              requires :singer, type: String
              optional :score, type: Atom, values: [:bad, :normal, :good, :awesome], default: :normal
           end
           post do
              track = %Track{title: params[:title], singer: params[:singer]}
              inserTrack(track)
              conn
              |> put_status(204)
              |> json(%{})
           end
       end
  
    end
  
  end

 ```
 
 As we can observe the  difference between v1 and v2 is that this time we are really taking the data from de database instead of using mocked data.
  
  We can check the service are working by running the below commands:
   
      curl -XGET http://127.0.0.1:5001/v2/tracks  -H "accept: application/json" -i
      
      curl -XPOST http://127.0.0.1:5001/v2/tracks -i -H "accept: application/json" -H "Content-Type:application/json" --data '{"title":"La Macarena", "singer":"Los del rio"}'
      
 
 Maru provides us with a task that allowas identify the routes in our application
 
    mix maru.routes
    
## The code

As always the code can be found on **[wesovilabs repository](https://github.com/wesovilabs/elixir_maru_training)**

## Links of interest

+ [Elixir documentation](http://elixir-lang.org/getting-started/introduction.html)
+ [Maru Documentation](https://maru.readme.io/docs/getting-started)
