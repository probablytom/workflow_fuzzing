---

title: Introducing Dynamic Fuzzing

---

<!-- 

# General outline #

Capturing and modelling unpredictable behaviour is very difficult.

However, for a certain class of systems, the nature of the unpredictable behaviour is known. This class would be the set of systems where their behaviour varies from the norm in ways which can be described as change of process. Change in process can be described as a transformation on a process — examples of this technique might be lisp's macro system.

However, the ideology of modelling is distinct from the ideology of programming, and relying on homoiconicity for the process change means attempting to capture model information in a program.

The issue here is that the aim of a programming language is orthogonal to the aim of a modelling language. Programming languages are geared toward the representation of computation, and engineering around this; modelling languages are oriented toward accurate representation of some concept, which may or may not be a process or computation.

Therefore, we introduce a modelling framework which can capture high-level model detail, but can take advantage of process transformation concepts similar to lisp's. Models in this framework represent socio-technical system behaviour, and take cues from successful paradigms with similar intents, such as BDD languages.

Processes in this framework can be changed using transformations on the process which operate with a similar philosophy to lisp's macro system, treating "code as data". Every time a process in a model is executed, a "blueprint" of the process is transformed through a pipeline of changes to the process. We call this "static process fuzzing".

We extend the notion of a lisp macro to a change in process which can happen *repeatedly*. The output of this series of transformations — which *could* be equivalent to the pipeline's input — is executed in lieu of the blueprint, every time it is enacted within the model. This enables the model to exhibit varying behaviour, where the impact of change to process can have non-linear effect as the model executes. We call the practice of generating new processes in our models every time they're run — as opposed to only once — "dynamic fuzzing". We call process transformers "fuzzers".

Processes in the framework are intended to act as a high-level representation of a workflow in a workflow model. Transformations are intended to represent the variance typically found in a socio-technical system, where sensors might be unreliable, or human actors have behaviours contingent on intractible external factors.

Importantly, the framework borrows its philosophy from the likes of Programming In The Large Vs Programming In The Small. Model detail is captured at a high-level, allowing the *architecture* of a model to be constructed. Low-level details are filled in at a level analogous to programming in the small. The interaction of the two allows high-level detail to be captured in a manner which fulfils the ideological requirements of the modeller, while low-level detail is filled in by those with the ideological requirements of a programmer.

Worth noting is that this separation of philosophies in capturing model detail and implementation detail requires separate fuzzers for each. Model detail is fuzzed by compiling the model to a finite state machine, and representing pertinent details from this machine in an array. Implemetation detail is fuzzed in much the same way lisp alters processes: a list representation of an AST is provided to a fuzzer, which can make necessary changes. 

Thus, fuzzers can be static or dynamic, depending on when the model uses them to generate new processes, and can be flow-level or process-level, depending on whether they operate on the level of a workflow or a process. We might therefore describe a static flow fuzzer, or a dynamic process fuzzer, depending on the nature of the variance applied to our model.


# Technical Content #

## spec for time ##
- clocks that emit ticks
- a tick runs a set of listeners as greenthreads

## spec for actors ##
- actors can perform()
- perform()ing will run a unit of work (which is a tick for the greenthread)
- actors subscribe to clocks, and work when the clock tells them to.
- actors employ the hewitt actor model
- generalise sets of actors with departments they work in. 
  - after checking a personal inbox of work, actors check any departmental inboxes they are associated with.
  - personal inbox resolution -> departmental inbox resolutions -> idle()
- actors can be passed messages which are either workflows or messages.
  - if passed a workflow in either inbox, execute it.
  - if passed a message, check an internal mapping of messages to workflows
    - in Python we implmenet pattern matching on the messages by rewriting the __eq__ magic method.

## spec for workflows ##
- workflows are kind of FSMs.
- We represent the FSM's structure as a graph. each node can have three types:
  - a list
    - represents a subworkflow
    - when one of these is reached, we run the subworkflow as if it was a regular workflow composed of one or more of any of the three types
  - a dictionary
    - represents a decision
    - dict has two keys:
      - "condition_function" 
        - A function which represents the decision process. 
        - Takes ctx, actor, env.
      - "cases"
        - a list where the first item is a condition and all other items are the rest of the workflow to be run if condition matches output of condition_function's return value
        - checked in order
          - important, because the return value could be equal to more than one thing!
        - TODO: to be turned into a list of tuples of the form [(case, workflow), ...]
    - a function
      - represents a task to be done
- this representation has an indexing system used to denote place on the automata.
  - a list!
  - the first element indexes into the workflow automata.
    - if the index points to a task (:: function), we are done.
    - if the index points to a list (i.e. a subworkflow), another element follows representing the index within this nested workflow automata.
    - if the index points to a dictionary, two elements follow
      - first represents the case matched which tells us which path of the workflow is being navigated down
      - second represents position within that nested workflow automata.
  - we repeat this indexing for all nested workflow automata.
  
  
# TODO #
- How do we discuss the representation of ticks passed for a task?
- How do we talk about theatre as a precursor to this model for actor synchronisation?
- Are clocks actually actors?
  - Can clocks subscribe to other clocks?
- Talk more clearly about the philosophical distinction being made for prog in large vs prog in small
  - Do some more reading around this
  - Talk to John O'Donnell more about it

-->


# Introduction #


# Motivation #


# Standard Models for Dynamic Fuzzing #


# Reference Implementation for Dynamic Fuzzing #

<!-- workflow_graphs & au -->


# Sample Model Implementation #


# Future Work #


# Conclusion #

