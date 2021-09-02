---
layout: post
permalink: /blog/pddl-introduction
title: PDDL in action 
tags:
  - Planning
  - Artificial Inteligence
accent_image: 
  background: url('/assets/img/blog/jj-ying.jpg') center/cover
  overlay: false
accent_color: '#ccc'
theme_color: '#ccc'
hide_last_modified: true
comments: true
---

> Artificial Inteligence (AI) is not only designing neural networks or/and processing images with computing vision. AI can be applied for resolving many problems such as the planning ones.

First of all I must say that this is not a tutorial to learn the full PDDL syntax but nevertheless you will be provided with explanations and links to the PDDL reference during this article.

## Concepts

To follow this tutorial we just need to understand two concepts: PDDL and Planner. The below definitions have been taken from the site [Planning.Wiki - The AI Planning & PDDL Wiki](https://planning.wiki/guide/whatis/planner)

- **PDDL**: Planning Domain Definition Language (PDDL) is a family of languages which allow us to define a planning problem. As planning has evolved, so to has the language used to describe it and as such there are now many versions of PDDL available with different levels of expressivity.

- **Planner**: An AI Planner allows us to attempt to solve a plannig problem. An AI planner reads in PDDL and uses it in order to decompose and solve the problem. There are however a really wide array of planners and some are objectively better or worse than others either unilaterally or in solving specific instances. 

It goes without saying that if you deep dive into the Planning field you will learn new concepts such as ADL, STRIPS among others. 

## Showcase

Let's image that we have a Vegan Restaurant and we want to improve our waiter service organization and make it more efficient. To achieve this, we need to identify what actions must be taken by our waiters at the time.

### Assumptions

After years of attending people we can state the following assumptions:

- The waiter spends `1 minute` accompanying the customers to the table.
- The spendt time ont deciding what to eat is always `5 minutes` (it doesn't depend on the number of people in the group).
- Each group will spend `30 seconds` per person on ordering the food. 
- The waiter spends `45 seconds` (0.75 minutes) on serving the food for each person in the group. 
- Each group will spend `20 minutes` eating and we assume that the number of people in the group won't affect the spent time.
- The spent time on paying the bill and leaving the table free will be always `3 minutes`. 

Apart from the above assumptions, we also need to consider that:

- The tables are not shared between different groups.
- The waiters cannot take two different actions at time. 

> Our solution won't be coupled to the number of waiters, groups nor to the  table distributions

### Let's code it

A PDDL problem consists of two files: 

- **domain**:  The domain file contains the object types, predicates and actions that can exist within the the model.  The domain is used to resolve problems that make use of this domain.

- **problem**: It defines which domain is used to resolve the problem and the definition of the problem: elements that participate in the problem, the initial state and the goal to be achieved.


#### Domain

##### Types

The identified types for our domain are `waiter`, `table` and `group`.

![Predicates definition](/assets/img/blog/domain_types.png){:.lead loading="lazy"}
Definition of the types
{:.figcaption}

##### Predicates
Predicates are true or false and they are used to identify which actions can be performed. The value of the predicates is modified along the problem resolution. By default, all the predicates are false. 

See below the list of identified predicates:

![Predicates definition](/assets/img/blog/domain_predicates.png){:.lead loading="lazy"}
Definition of the predicates
{:.figcaption}

#### Functions

The functions are referred to as numeric fluents.

![Predicates definition](/assets/img/blog/domain_functions.png){:.lead loading="lazy"}
Functions for the domain restaurant
{:.figcaption}


#### Actions

An action defines a transformation the state of the world. This transformation is typically an action which could be performed in the execution of the plan.

The standard action definition looks like this:

```
(:action BUILD-WALL
    :parameters (?s - site ?b - bricks)
    :precondition (and
        (on-site ?b ?s)
        (foundations-set ?s)
        (not (walls-built ?s))
        (not (material-used ?b))
    )
    :effect (and
        (walls-built ?s)
        (material-used ?b)
    )
)
```

On the other hand, we will make use of an speciall action `durative-action` because our actions impply temporal consequences. For example, a waiter will be busy while is serving the food.

The possible actions in our domain are:

**ASSIGN-TABLE-TO-GROUP**

![](/assets/img/blog/action_assign_table_to_group.png){:.lead loading="lazy"}
Action ASSIGN-TABLE-TO-GROUP
{:.figcaption}


**DECIDE-WHAT-TO-EAT**

![](/assets/img/blog/action_decide_what_to_eat.png){:.lead loading="lazy"}
Action DECIDE-WHAT-TO-EAT
{:.figcaption}

**ORDER-FOOD**

![](/assets/img/blog/action_order_food.png){:.lead loading="lazy"}
Action ORDER-FOOD
{:.figcaption}

**COOKING-FOOD**

![](/assets/img/blog/action_cooking_food.png){:.lead loading="lazy"}
Action COOKING-FOOD
{:.figcaption}


**SERVE-THE-FOOD**

![](/assets/img/blog/action_serve_the_food.png){:.lead loading="lazy"}
Action SERVE-FOOD
{:.figcaption}

**EAT**

![](/assets/img/blog/action_eat.png){:.lead loading="lazy"}
Action EAT
{:.figcaption}

**PAY-BILL-AND-SAY-BYE**

![](/assets/img/blog/action_pay_bill.png){:.lead loading="lazy"}
Action PAY-BILL-AND-SAY-BYE
{:.figcaption}

### Problem

The below diagram represents the problem to be resolved.

![](/assets/img/blog/scenario1.png){:.lead loading="lazy"}
Scenario 1
{:.figcaption}

First of all we need to transform the real problem into a PDDL script.

![](/assets/img/blog/problem_basic.png){:.lead loading="lazy"}
Definition of the problem
{:.figcaption}

To resolve our problem we use the planner `optic` that supports `durative-actinos`.  You could resolve the problem with the below command.

```bash
docker run -v `pwd`:/var/data adamfgreen/aip2020:latest optic \
    -E -c -S /var/data/domain.pddl /var/data/problem-basic.pddl
```

The planner will try to find the plan that would find the best solution for our problem. The best solution will guarantee that
the waiter organization is the best possible.

```
; Plan found with metric 50.006
; States evaluated so far: 833
; States pruned based on pre-heuristic cost lower bound: 431
; Time 0.30
0.000: (assign-table-to-group w1 t2 g2)  [1.000]
1.001: (decide-what-to-eat g2)  [5.000]
1.001: (assign-table-to-group w1 t1 g1)  [1.000]
2.002: (decide-what-to-eat g1)  [5.000]
6.002: (order-food g2 w2)  [2.000]
7.003: (order-food g1 w1)  [1.500]
8.003: (cooking-food g2)  [16.000]
8.504: (cooking-food g1)  [12.000]
20.505: (serve-the-food w1 g1)  [2.250]
22.756: (eat g1)  [20.000]
24.004: (serve-the-food w2 g2)  [3.000]
27.005: (eat g2)  [20.000]
42.757: (pay-bill-and-say-bye w1 g1 t1)  [3.000]
47.006: (pay-bill-and-say-bye w1 g2 t2)  [3.000]

 * All goal deadlines now no later than 50.006
```

Certainly to predict the best plan for the above problem could be easy, but **could you do the same for the following problem?**


![](/assets/img/blog/scenario2.png){:.lead loading="lazy"}
Scenario 2
{:.figcaption}


![](/assets/img/blog/problem2_def.png){:.lead loading="lazy"}
Definition of the problem
{:.figcaption}


```bash
docker run -v `pwd`:/var/data adamfgreen/aip2020:latest optic \
    -E -c -S /var/data/domain.pddl /var/data/problem-complex.pddl
```

> And we have a plan!

```
; Plan found with metric 351.797
; States evaluated so far: 22478
; States pruned based on pre-heuristic cost lower bound: 98
; Time 21.25
0.000: (assign-table-to-group w1 t3 g1)  [1.000]
1.001: (decide-what-to-eat g1)  [5.000]
6.002: (order-food g1 w1)  [1.500]
7.503: (cooking-food g1)  [12.000]
19.504: (serve-the-food w1 g1)  [2.250]
21.755: (eat g1)  [20.000]
41.756: (pay-bill-and-say-bye w1 g1 t3)  [3.000]
44.757: (assign-table-to-group w1 t5 g8)  [1.000]
45.758: (decide-what-to-eat g8)  [5.000]
50.759: (order-food g8 w1)  [4.000]
54.760: (cooking-food g8)  [32.000]
86.761: (serve-the-food w1 g8)  [6.000]
92.762: (eat g8)  [20.000]
112.763: (pay-bill-and-say-bye w1 g8 t5)  [3.000]
115.764: (assign-table-to-group w1 t1 g7)  [1.000]
116.765: (assign-table-to-group w1 t5 g6)  [1.000]
116.765: (decide-what-to-eat g7)  [5.000]
117.766: (decide-what-to-eat g6)  [5.000]
122.767: (order-food g6 w1)  [3.500]
126.268: (cooking-food g6)  [28.000]
154.269: (serve-the-food w1 g6)  [5.250]
159.520: (eat g6)  [20.000]
179.521: (pay-bill-and-say-bye w1 g6 t5)  [3.000]
182.522: (assign-table-to-group w1 t2 g4)  [1.000]
183.523: (decide-what-to-eat g4)  [5.000]
183.523: (assign-table-to-group w1 t3 g5)  [1.000]
184.524: (assign-table-to-group w1 t4 g3)  [1.000]
184.524: (decide-what-to-eat g5)  [5.000]
185.525: (decide-what-to-eat g3)  [5.000]
189.525: (order-food g5 w1)  [1.000]
190.526: (cooking-food g5)  [8.000]
198.527: (serve-the-food w1 g5)  [1.500]
200.028: (eat g5)  [20.000]
200.028: (order-food g3 w1)  [1.000]
201.029: (cooking-food g3)  [8.000]
209.030: (serve-the-food w1 g3)  [1.500]
210.531: (eat g3)  [20.000]
220.029: (pay-bill-and-say-bye w2 g5 t3)  [3.000]
230.532: (pay-bill-and-say-bye w1 g3 t4)  [3.000]
233.533: (assign-table-to-group w1 t5 g2)  [1.000]
234.534: (order-food g4 w1)  [2.500]
234.534: (decide-what-to-eat g2)  [5.000]
237.035: (cooking-food g4)  [20.000]
257.036: (serve-the-food w1 g4)  [3.750]
260.787: (eat g4)  [20.000]
280.788: (pay-bill-and-say-bye w1 g4 t2)  [3.000]
283.789: (order-food g7 w1)  [2.000]
285.790: (cooking-food g7)  [16.000]
301.791: (serve-the-food w1 g7)  [3.000]
304.792: (eat g7)  [20.000]
304.792: (order-food g2 w1)  [2.000]
306.793: (cooking-food g2)  [16.000]
322.794: (serve-the-food w1 g2)  [3.000]
325.795: (eat g2)  [20.000]
345.796: (pay-bill-and-say-bye w1 g2 t5)  [3.000]
348.797: (pay-bill-and-say-bye w1 g7 t1)  [3.000]

 * All goal deadlines now no later than 351.797
```

## Conclussions

It's fair to point out that making use of a planner could be tricky. Hence some planners only work with a single operating system or distribution and that could make developers to drag their feet on it
On the plus side, contributions to this world would be very appreciated.

Last but not least I must say that PDDL can be very interesting to get the children introduced to the computer science logic


## Code

The code can be found here:

- [domain.pddl](https://gist.github.com/ivancorrales/ed5cd0519fcadb362cddc3e04217bd92)
- [problem-basic.pddl](https://gist.github.com/ivancorrales/a4bb06390401d4dd8af11f61435d1df7)
- [problem-complex.pddl](https://gist.github.com/ivancorrales/a4bb06390401d4dd8af11f61435d1df7)

## Citations

- Wikipedia contributors. (2021, June 3). Planning Domain Definition Language. In Wikipedia, The Free Encyclopedia. Retrieved 06:00, September 2, 2021, from [shorturl.at/kuAHW](https://en.wikipedia.org/w/index.php?title=Planning_Domain_Definition_Language&oldid=1026662262)

- Planning.Wiki - The AI Planning & PDDL Wiki, from [https://planning.wiki](https://planning.wiki)