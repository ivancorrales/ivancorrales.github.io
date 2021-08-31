---
layout: post
permalink: /blog/introduction-go
title:  Go for dummies
description: >
 This is a very brief introduction to the Go programming language
tags:
  - Go
  - API
  - Demo
accent_image: 
  background: url('/assets/img/blog/jj-ying.jpg') center/cover
  overlay: false
accent_color: '#ccc'
theme_color: '#ccc'
hide_last_modified: true
comments: true
---

# Go for dummies

* toc
{:toc .large-only}

## The purpose

The purpose of this post is making use of some of the Go concepts and get some help to get started in working with Go

## Reason

Why Go? I have some experience coding in Scala and recently I did a brief poc with Erlang... They both are nice (especially Erlang) but I wanted to try Go since awesome products are developed in it such as Docker or Consul. Beside Go syntax is lovely!

## About the PoC

I just decided to develop a game as I thought It could be a good way to know some Go statements and data structures.

## Rules

- The pieces will be displayed in a 6 x 6 board.

- Board will contain three type of letters: A or B or C and they all will be distributed randomly in the board

- Once a position is discovered by the player this cannot be chosen again.

- When the player finds a pair his scores is increased in 10 points, when the player doesn't find a pair his scores is decreased in 2 points.

- Game is over when there's no more available board boxes.

## Snapshots

Below a couple of pictures of the game

![Game](https://raw.githubusercontent.com/wesovi/findThePairs/master/resources/img1.png)

![Game](https://raw.githubusercontent.com/wesovi/findThePairs/master/resources/img2.png)

## To sum up

- Syntax is lovely, similar to C

- Method can return more than one value, this is great.

- Import other  'classes' (sorry but I have too much experience working with OOP programming languages) can be done using a single word import

- Unused imports and variables will throw fails in the project compilation.

- Below a sample of creating struct instances, the one will return a pointer and the second one an object. Do not forget we use pointers in Go.

- The logo is so great! XD


## Code

Code can be found on Github, **[click here to download it](https://github.com/wesovi/findThePairs)**
