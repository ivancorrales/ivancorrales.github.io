---
layout: post
permalink: /blog/openldap-passthrough
title:  Pass through authentication in Openldap
description: Pass through authentication in Openldap.
tags:
  - OpenLDAP
  - Passthrough
  - Ops
accent_image: 
  background: url('/assets/img/blog/jj-ying.jpg') center/cover
  overlay: false
accent_color: '#ccc'
theme_color: '#ccc'
hide_last_modified: true
comments: true
---

# Pass-through authentication in Openldap

* toc
{:toc .large-only}

## About 

Pass-through authentication is a  mechanism that allows **delegating authentication** from one system to other one. Basically
this makes sense when we already got a system that manages credentials but we do not have writing privileges in it. The
 way we can extend the security system could be by taking advantage of pass-through authentication. Below a diagram that 
 represents this system.

![Pass through diagram](http://www.wesovilabs.com/assets/images/posts/openldap/pass-through-diagram.png)


## The problem

Sometimes in a corporate environment there are many teams that do a similar job but each of them is in charge of some part
 of the global system. Moreover after appearing some new positions such as *DevOps*, *SysOps*, *SysAdmin*, *SecOps* (companies are driving crazy but this is 
another question....)

Working as a *DevOps* I recently needed to face a problem in my current company. The corporate security team handles 
users credentials (and also the access to corporate tools) by making use of and **Active Directory**. 

As a member of the DevOps team I was required to make a proposal to manage the roles for the **ALM (Application Lifecycle Management) tools** .
The main requirement was that users should  be able to authenticate into the tools by theirs corporate credentials.  

This had been easy enough to resolve if my team had writing permissions into the Active Directory but it wasn't the case. 

## The proposal

Understanding we **can't** and mustn't **replicate** the credentials information into multiple systems (nor the security rules) I though abut
  pass-through authentication. 
  
As we saw in the above diagram this process could be understood as a way to **inherit a security system**. 
  
My team would be handle the authentication into the ALM Tools by an Openldap that would be delegating the authentication
 to the corporate one. This new piece would only care about managing roles and permissions for the ALM Tools but not for the
 credentials.
 
![Proposal](http://www.wesovilabs.com/assets/images/posts/openldap/proposal.png)

## The technologies

Since I am still working in a PoC for the company I make use of **Docker containers** to do this job. Why Docker? It makes me the life easy 
for multiple reasons. 

- **Active Directory**
  This will be running in a remote system. As we said earlier this manage the corporate user credentials.

- **Openldap**
  I bet for Openldap as a system to manage the roles since I've got some great experience working with it and there are plenty of **how to's** on
  the Internet.

  As I said earlier we will run the Openldap into a Docker container. We will need to connect to SASL.

- **SASL**
  by Wikipedia: *"Simple Authentication and Security Layer (SASL) is a framework for authentication and data security in Internet protocols. It decouples authentication mechanisms from application protocols, in theory allowing any authentication mechanism supported by SASL to be used in any application protocol that uses SASL."* 

  We will enable SASL into the same container Openldap will be running.  SASL will be delegating credentials authentication to the Active Directory.

- **Jenkins**
  Jenkins is a Continuous Integration tool. The Jenkins authentication will take place by invoking the Openldap system.


So to sum up we could draw a new diagram:

![Real scenario](http://www.wesovilabs.com/assets/images/posts/openldap/scenario.png)


## The solution

**Dockerfile**

  Basically we do some installation into the container and copy resources that will see next. 

  {% gist 77896dc54cfa4c6afa6c555b0e06b3fc %}
  
  
**slapd.conf**

  This file is read by Openldap and here we define the password authentication protocol. As we can see, we define as **saslauthd**
  but also we are defined the directory where teh sasl socket will be running on.

  {% gist a8b6a7dbc418710821bfe9b105097e9e %}
  
**saslatuhd.conf**
  
  This file contains the connection details to the remote active directory. It's not rock science at all. 
  
  {% gist 18914b9a342cb6d1b38eea9b708ba7d1 %}
  
**saslatuhd**

  The script used to run sasl.  More details could be found on [here](http://www.linuxcommand.org/man_pages/saslauthd8.html9)
  
  {% gist 961aea61a8b7442feaf77879f65d21d9 %}

**slapd.sh**

  This script  will set SASL and Openldap up and running. In the Dockerfile we defined an Entrypoint to invoke this script. Once the system is ready the docker will launch the Openldap server.

  {% gist e7eeee191c85199a733a3caae4b013fe %}

**ldif/structure.ldif**

  Once the Openldap is running we could populate the system with a very basic structure like this one. The **most important**
  thing is the **userPassword** attribute. We won't define a real credentials, we just put **{SASL}user** or **{SASL}user@realm**
  This provide SASL with the user detail to authenticate into the remote system.
  
  {% gist c37470618636cda45aea0aeb5353bc1b %}
  
  We could import the data by running this:
    
    ldapadd -x -D "cn=admin,dc=wesovilabs,dc=com" -w s3cr3t -h localhost -p 389 -f structure.ldif
  
  