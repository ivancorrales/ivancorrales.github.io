---
layout: post
permalink: /blog/python-falcon
title:  Build REST API with Falcon
tags: 
    - Falcon
    - Python
accent_image: 
  background: url('/assets/img/blog/jj-ying.jpg') center/cover
  overlay: false
accent_color: '#ccc'
theme_color: '#ccc'
hide_last_modified: true
comments: true
---

# Build a REST API with Falcon (Python)

* toc
{:toc .large-only}

## Synopsis

I hope this guide help guys who gonna start coding in Python and implementing REST API

## Assumptions

I understand you have a clear idea about REST is and python installed onto your environment and you have a real os (not windows)

## Motivation

*   Introducing into a new programming language to me: **Python**

*   Learning basic concepts and best practices in Python coding

## Setting up the environment

In order to get and environment ready to start coding we'll follow the below steps:

*   **1\. Creating a project directory for this development**

        mkdir -p /Users/Ivan/Sandbox/WesoviLabs/falcon-rest-api

        cd /Users/Ivan/Sandbox/WesoviLabs/falcon-rest-api

*   **2\. Setting up a Virtual environment**  

    We can skip next step in case of virtualenv is already installed.

        pip install virtualenv

    We could use the next virtualenvironment for ent developments

        virtualenv my-restapi-virtualenv

    We activate the virtualenv

        source my-restapi-virtualenv/bin/activate

*   **2\. Installing required libraries**  

    The falcon librrary

        pip install falcon

    Gunicorn is a python WSGI server, more info [here](http://gunicorn.org/)

        pip install gunicorn

    Cython will make our application fly!, PyPy would be even better but unfortunately Falcon is currently only compatible with PyPy 2.7

        pip install --upgrade cython falcon

## Creating a very basic application

We will create the **app.py**. In order to run the application we can do it just by the below command

    gunicorn app:api

![](/assets/images/posts/falcon/gunicorn%20running.png) Now we can open another temrinal and test the service by running

    curl http://127.0.0.1:8000/books/ -i

![](/assets/images/posts/falcon/response.png)

## Code

Code can be found on Github, **[click here to download it](https://github.com/wesovilabs/falcon-rest-api/tree/basic_application)**

In a few days I will be publishing a more detailed project developed with Falcon


