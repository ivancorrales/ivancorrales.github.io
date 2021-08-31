---
layout: post
permalink: /blog/elixir-exkorpion
title:  Testing your Maru application with Exkorpion
description: Exkorpion.
tags: 
  - Elixir
  - Exkorpion
accent_image: 
  background: url('/assets/img/blog/jj-ying.jpg') center/cover
  overlay: false
accent_color: '#ccc'
theme_color: '#ccc'
hide_last_modified: true
comments: true
---

# Testing your Maru application with Exkorpion

* toc
{:toc .large-only}

## Purpose

Along this guide we'll learn how to implement our tests with Exkorpion.  

## The environment

To follow this guide I assume you already have your **Elixir** development environment ready, besides you should have installed **docker** on our machine since
we need to run a postgresql database and we will be taking advantage of docker.

This guide will go over  **Maru** and **Ecto** but being focused on understanding **Exkorpion**. 

### Ecto

Ecto is a database wrapper that makes the life easier for Elixir developers who need to make use of database in their applications. 

To get a better understanding of Ecto, have a look at **[Getting started with Ecto](http://www.wesovilabs.com/exto/elixir/mix/2016/07/16/Working_with_Ecto/)**

### Maru

It's a REST-like API micro-framework for elixir inspired by grape. 

I encourage you to have a look at my previous article **[Getting started with Maru](http://www.wesovilabs.com/exto/elixir/mix/maru/2016/07/16/Elixir_maru_training/)**  

### Exkorpion

Exkorpion is test library that wraps ExUnit and enhances their features providing our tests with a **BDD syntax**. 

#### Goals

- It helps us to write tests which are easy-to-read.
- It is completely compatible with ExUnit.
- It forces us to structure our tests in steps (given-when-then)
- It is not coupled to any other framework

#### Syntax

As was mentioned on the above Exkorpion is mainly oriented to a bdd syntax:

**scenario**:  A scenario groups multiple cases that test a functionality works as expected. By using scenario we achieve the below:

  - Better documentation for other developers.
  - Test are better organized and structured
  - Working under an agile methodology we can match scenarios to acceptance criteria

**it**: Exkorpion provide with a reserved word It to represent any of the cases inside a scenario.

**before_each**: Before each will be inside of a scenario and provices with a reusable set of data for our tests.

**with/given/when/then**: These word are the ones that provide us with s BDD syntax. Actually even when we write some unit tests we should thinkg about them.

  - *Given*: It defines the input data for performing the tests. (It's an optional step, it could be not neccessary sometimes)
  - *When*:  It performs the action to be tested.
  - *Then*:  It ensures the result in the preoviuos step are the expected.


we could make us of **with** step if we pretend to run the some tests for multiple input

## Let's do it

This step-by-step guide will make use of a existing project which is implemented with **Maru** and **Ecto**. This project
 implement a versioned API, which offers the below services:
   
   | Version  |  Verb  |  Path        | Description |
   | ---      | ---    |  ---         |  --- |
   |v1        |  GET   |  /v1/tracks  |   Return the list of mocked tracks (It always returns two tracks) |
   |v1        |  POST  |  /v1/tracks  |   Add a new track and return the list of existing tracks (It will return 3 tracks) |
   |v2        |  GET   |  /v2/tracks  |   Return the list of tracks in the database |
   |v2        |  POST  |  /v2/tracks  |   Add a new track and return the list of existing tracks |
   {:.mbtablestyle}
   
The above records represent the services.

**Go to the repository**: **[elixir_maru_training](https://github.com/wesovilabs/elixir_maru_training)** is a repository hosted on Github.

![elixir_maru_training](https://raw.githubusercontent.com/wesovilabs/wesovilabs.github.io/master/assets/images/posts/exkorpion/elixir_maru_training.png)

**Fork the repository**:

![Fork](https://raw.githubusercontent.com/wesovilabs/wesovilabs.github.io/master/assets/images/posts/exkorpion/project-forked.png)

**Clone your repository**: Once you've already forked the repository, lets clone it onto your computer 

{% highlight bash %}
git clone https://github.com/ivancorrales/elixir_maru_training.git
{% endhighlight %}

**Add exkorpion dep to your project**: Lets  open the **mix.exs**, add exkorpion dependency and download the dependencies.

Add the below dep to deps function:

{% highlight elixir %}
{:exkorpion, "~> 0.0.2", only: [:dev, :test]}
{% endhighlight %}

![mix.exs](https://raw.githubusercontent.com/wesovilabs/wesovilabs.github.io/master/assets/images/posts/exkorpion/exkorpion_dependency.png)

**Download your deps**: Check into the project directory and run 

{% highlight bash %}
mix deps.get
{% endhighlight %}

This command will download all required dependencies from this project.

**Create the exkorpion structure**: By running the below task we setup the directoies to work with exkorpion

{% highlight bash %}
mix exkorpion.init
{% endhighlight %}

After running the task a new directory called **scenarios** will be created in the project root. Inside this directory we can find the file
**scenario_helper.exs** that looks like this

![mix.exs](https://raw.githubusercontent.com/wesovilabs/wesovilabs.github.io/master/assets/images/posts/exkorpion/sceneario_helper-1.png)

**Writing a scenario for REST API V1**: First scenario will be focused on testing API V1 (**track_router_v1.ex**) . This version just works with mocked data so we won't be required
 to run any database.
 
 - Create a file **track_router_v1_scenario.exs** in **/scenarios/** directory.
 - We write the module that will contain the scenarios block
 
 ![mix.exs](https://raw.githubusercontent.com/wesovilabs/wesovilabs.github.io/master/assets/images/posts/exkorpion/sceneario1_1.png)
 
 - Write a test to check the get-tracks services in v1 work as expected. Don't forget this is a mocked service that will always return 2 elements.
 
 ![mix.exs](https://raw.githubusercontent.com/wesovilabs/wesovilabs.github.io/master/assets/images/posts/exkorpion/scenario1_2.png)
 
 - Run your first scenario to check everything works as expected. Tu run the exkorpion scenearios you onyly need to write the below command:
 
{% highlight bash %}
 MIX_ENV=test mix exkorpion
{% endhighlight %}

![mix.exs](https://raw.githubusercontent.com/wesovilabs/wesovilabs.github.io/master/assets/images/posts/exkorpion/scenario1_3.png)

As we can observe on the above execution the tests was successful. We can make a change in thest to observe how the execution will look when
the test fails. For example we will say the length of the list is 3 instead of 2. 

![mix.exs](https://raw.githubusercontent.com/wesovilabs/wesovilabs.github.io/master/assets/images/posts/exkorpion/scenario1_4.png)

After running the scenearios

{% highlight bash %}
 MIX_ENV=test mix exkorpion
{% endhighlight %}

we can observe the descriptive output when something fails

![mix.exs](https://raw.githubusercontent.com/wesovilabs/wesovilabs.github.io/master/assets/images/posts/exkorpion/scenario1_5.png)

- The second services provided by API V1 will insert a new track into the mocked list and return 3 elements to the client. To write
 this test will make use of two reserved words in Exkorpion: **with** and **given**. The key with, will allow us to run the same test for multiple cases. 

 ![mix.exs](https://raw.githubusercontent.com/wesovilabs/wesovilabs.github.io/master/assets/images/posts/exkorpion/scenario1_6.png)
 
 As we can observe in the second test will be checking that the size for the track list has increased but also that the new element has been
 included into the list.
 
 Now we when running the tests will check both tests in the scenario were successful.
 
 ![mix.exs](https://raw.githubusercontent.com/wesovilabs/wesovilabs.github.io/master/assets/images/posts/exkorpion/scenario1_7.png)
 
**Writing a scenario for REST API V2**: Bear in mind this version of the api will run on a real environment and the application will need to access to 
a postgrest database (See postgresql configuration in **/config/config.exs** 

- First step now, it will be run the docker compose on **/docker** directory, so open a new terminal and run the scripts **/script/setup-environment.sh** 
 
{% highlight bash %}
 sh scripts/setup-environment.sh
{% endhighlight %}

Now we have a postgresql running on our machine with same config details for the **Ecto** configuration

- Create a file **track_router_v3_scenario.exs** in **/scenarios/** directory.

- As we are writing acceptance tests we need to ensure the database status is always the same for all the executions, so we will take advantage of **Ecto** and
 we run the sceneario always with the same data. To achieve this we need to add the below commands into the **/scenearios/sceneario_helper.exs**
 
 ![mix.exs](https://raw.githubusercontent.com/wesovilabs/wesovilabs.github.io/master/assets/images/posts/exkorpion/sceneario_helper-2.png)
 
- To test V2 we will write only one case that will add a new track to the system (this time in a real database) and it will check the size of the tracks has been
increased after adding a new element. 

![mix.exs](https://raw.githubusercontent.com/wesovilabs/wesovilabs.github.io/master/assets/images/posts/exkorpion/scenario2_1.png)
 
 - Running both scenarios
 
{% highlight bash %}
 MIX_ENV=test mix exkorpion
{% endhighlight %}
 
 ![mix.exs](https://raw.githubusercontent.com/wesovilabs/wesovilabs.github.io/master/assets/images/posts/exkorpion/scenario2_log.png)
 
 Now we can ensure our **Maru Rest API work as expected**
 
## The code
 
 In case of you have any doubt please have a look at my forked project **[here](https://github.com/ivancorrales/elixir_maru_training)**
 
 
## Shared it!

You like this post,  please **[share it with other colleagues on Twitter](http://twitter.com/share?text=Great post about testing Maru applications with exkorpion!&url=http://www.wesovilabs.com/elixir/2016/09/13/Maru_application_with_exkorpion/&hashtags=elixirlang&via=wesovilabs)** 
