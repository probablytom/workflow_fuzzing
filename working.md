---

title: "Introducing Dynamic Fuzzing"
journal: TBD
author:
- name: Tom Wallis
  footnote: 
  corresponding: w.wallis.1@research.gla.ac.uk
- name: Tim Storer
  footnote: 1
affiliation:
- number: 1
  name: Glasgow University, Computing Science
keyword:
  - dynamic fuzzing
  - python
  - TODO more keywords
abstract: Capturing and modelling unpredictable behaviour in certain classes of systems can be very difficult: as the behaviour of actors in the system becomes less predictable, and more contingent on external or intractible factors, a model of the system's behaviour becomes intractible to construct due to the scale of variance a model of the system must capture. This paper presents dynamic fuzzing, a method for simplifying a model of a system with intractibly contingent behaviour. After introducing the concepts of dynamic fuzzing, a suite of Python frameworks to construct dynamically fuzzed models are presented, and a sample model using these frameworks explored as a case study.
bibliography: references.bib
acknowledgements: |
  Obashi and co
contribution: |
  Must include all authors, identified by initials, for example:
  A.A. conceived the experiment(s),  A.A. and B.A. conducted the experiment(s), C.A. and D.A. analysed the results.  All authors reviewed the manuscript.
additionalinformation: |
  To include, in this order: \textbf{Accession codes} (where applicable); \textbf{Competing financial interests} (mandatory statement).
  The corresponding author is responsible for submitting a \href{http://www.nature.com/srep/policies/index.html#competing}{competing financial interests statement} on behalf of all authors of the paper.

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
        - progrmaming TODO: to be turned into a list of tuples of the form [(case, workflow), ...]
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
  
  
# writing TODO #
- How do we discuss the representation of ticks passed for a task?
- How do we talk about theatre as a precursor to this model for actor synchronisation?
- Are clocks actually actors?
  - Can clocks subscribe to other clocks?
- Talk more clearly about the philosophical distinction being made for prog in large vs prog in small
  - Do some more reading around this
  - Talk to John O'Donnell more about it

-->

---



# Introduction #
As the complexity of a system's behaviour increases, especially when this behaviour is contingent on intractible or external factors, future states of the system writ large becomes difficult to predict.

This poses problems for systems science. The strength of the scientific method is an ability to predict: a theory becomes more solidly founded, and proves its value to the scientific community, via its ability to predict unseen states of its subject. Therefore, a system science paradigm which is unable to predict these states is hampered from the very start.

Socio-technical systems represent a class of real-world systems which, relatively speaking, might be considered simple. A socio-technical system doesn't necessarily permit the more complex nature of, for example, a complex adaptive system; yet, socio-technical systems still present a difficult class of systems to build accurate models of. Attempts to build models of socio-technical systems usually fall into one of two categories: simplified models of the world, such as goal-oriented modelling and responsibility modelling, and more structured models of the world, such as workflow modelling or modelling via activity diagrams and petri nets.

There are benefits to both approaches, yet the two remain orthogonal to each other. A less structured model, for example, can ususally capture relatively high-level detail about the world. Responsibility modelling is an attempt to capture information about human systems which can determine which agents in a system are accountable for certain events, or the achievement of certain goals. This information is captured at a high level, and information about the system is inferred. Workflow modelling, on the other hand, captures low-level detail about the behaviours of agents within a system. The notion behind workflow modelling is that, by modelling fine-grained detail about a system, one is able to simulate the system and infer high-level details from the emergent properties percieved. Either philosophy exhibits flaws due to the inherant trade-off between the degree of detail captured and the ease of capturing said detail.

This can be seen in illustrative examples from either philosophy. Socio-technical systems naturally exhibit non-linear, intractible behaviour which can spiral in myriad directions due to the influence of small changes in one of thousands of variables. This note is important, because one typically constructs a model to learn something about its subject: a typical process is to construct a model, analyse it, and draw conclusions. A system model which relies on high-level detail, however, loses key details which makes quantitative analysis and simulation impossible. Typical high-level socio-technical modelling techniques eschew this analysis in favour of their only remaining option: a visual analysis of the system, and conclusions drawn simply from the analyst's rationality. Illustrative examples would be Soft Systems Methodology's diagrammatic approach, or UML's graphical nature.

Unfortuantely, modelling at this high level of detail is often the only way to draw nuanced conclusions about high-level and/or emergent properties of a system. A low-level model must describe the thousands of variables that high-level models cannot, and high-level conclusions from these models can only be drawn if those conclusions are easily accurately quantified and well-understood. Unfortunately, high-level properties can often only be determined by many disparate propeties of the modelled system, making them intractible to determine analytically.

It is worth mentioning that some modelling paradigms provide solutions to this problem. A notable example is NetLogo, a platform which permits expression of a model in low-level details, with high-level emergent phenomena appearing in visualisations of the system being modelled, and data collected in formats such as graphs. However, analysis of a NetLogo model is almost always visual; this requires any high-level phenomena observed to be easily visualised, as opposed to analysed or interpreted via other means. 

A more appropriate --- yet similar --- solution to the problem might be found considering the philosophy found in Programming In The Large vs. Programming In The Small. The paper discusses the benefit of a programming platform with two "levels": a "low-level", characterised by detail-oriented units. In modern technologies, this could be small programs hosted in containers. This is contrasted against a "high level", characterised by the composition of these fundamental elements. The equivalent for the container example could be container orchestration.

In this paper, we posit that identifying a similar division in modelling technologies helps to address the trade-off between high and low level details. Specifically, we propose agent-based workflow models which are broken into:

1. Individual actions which actors can perform, specified in a low-level language suitable for this task
2. Actions composed together into a workflow, specified in a high-level language which specifies new actions, by chaining together others

<!-- TODO: solved a merge conflict here. The last few paras of this and the beginning of the next section were written on different branches. Does this still flow properly? -->

# Re-Interpreting Large vs. Small #

A re-interpretation of some classic concepts in computing science literature might provide some improvements to the common paradigm.

Programming In The Large vs Programming In The Small was an influential paper which presented a philosophy on the design of our languages. Particularly, <!-- AUTHORS --> claim that software systems are usually composed of two parts: units of implementation detail, and separately, orchestration around those smaller units. This approach has some benefits:

1. Small implementation units are easier to test than potentially large ones. Having a separate language for the implementation of smaller units ought to keep their size reasonably bounded.
2. Focus on orchestration as a separate activity ought to make easier: the highlighting of security vulnerabilities from improperly connected units; the maintainance of a system; the work required in considering and architecting a system
3. The work involved in implementation, and in orchestrating implemented units, require different modes of thought. To implement _both_ in one language or framework may work to blur the line between the two, detracting from the thought process required of both.
<!-- TODO: what's the citation count of Large vs. Small? -->

In the context of building a _model_, this means developing low-level descriptions of the world in a "lower-level" implementation language, and tying these together into a model of the world using a "higher-level" description language.

This approach to modelling was originally conceived in the context of workflow modelling, which provides a convenient example to explore this modelling philosophy in more detail.

Canonical examples of workflow models might be UML activity diagrams, which _structure_ work in a clear way. This allows the high-level modelling discussed earlier. However, UML activity diagrams must be paired with some implementation of the actions discussed in the model for any _simulation_ to occur; without this, only inspection of the workflow itself can be achieved. To implement the individual actions detailed in an activity diagram means using a language other than a UML activity diagram, because specific implementation details cannot be expressed in these diagrams. 

Alternatively, focus can be on these low-level model details, and a model can describe only the implementation details of individual actions --- say, in a programming language. This permits a notation for a workflow which might seem convenient: workflows can be described as functions which simply execute these implementations, and flow control such as decisions in the workflow can be expressed using common control flow structures in most major programming languages. Indeed, a model can technically be implemented in any Turing-complete programming language --- yet this observation is not especially helpful, as any high-level understanding of the model created would be hard to achieve from reading programming code. For this, a better approach would be to represent the workflow in a more readable form than programming code, which would presumably be developed for the purpose of writing the workflow itself outside of implementation details. Unfortunately, we have arrived back at a UML activity diagram. 

Rather than choosing between the two, we might opt to write the workflow in one language, and the implementation details in another, combining them as appropriate. Here we get the best of both worlds: low-level detail can be described in an appropriate format, to enable the execution of a simulation, yet high-level detail can be described in a format appropriate for reasoning about the flow of actions, rather than the specifics of the actions. 

The benefit of the approach, ultimately, is that the modeller is able to use whatever tool is most appropriate for the task at hand. UML activity diagrams _exist_ for the purpose of easier reasoning and communication about workflows, whereas high-level programming languages exist for the purpose of easier reasoning and communication about programs.

## Are programs models? ##

This work began as an answer to the question, ``what is the difference between a programming language and a modelling language?''.

Programming languages can clearly _represent_ anything representable in a modelling language --- and, because not all modelling languages are Turing-complete, modelling languages cannot represent everything representable in a programming language. However, the nature of most modelling languages is different to the nature of most programming languages: they are frequently graphical in nature, and tend to be descriptive rather than defining procedures.

The difference, therefore, is perhaps the _perspective_ of the user of the language. A language designed graphically and simply, which is incapable of specifying low-level detail, indicates that the language is designed for a use case where low-level details detract from the work at hand, even if they might ultimately be useful to the user of the model. That such languages see popularity indicates that that use case is a legitimate one, which fulfils a genuine need "in the wild".

We propose that the difference between a modelling language and a programming language is the perspective of the user of the language: in just the same way that a user of a high-level programming language has different needs (and a different perspective) to a writer of assembly code, so too do the modeller and the programmer have different needs and perspectives. Unlike programming, however, modelling typically does not link these high and low level perspectives well. 

<!-- Some notes from John O'Donnell and general history of CS stuff here, claiming that the presented interpretation of Large vs. Small is a little new... -->

# Standard Models for Dynamic Fuzzing #

<!-- Actors -->

<!-- Time -->
Standard model of time is a clock which holds a set of entities subscribed to it. These entities are expected to be able to perform() as per the actor spec.

Clock ticks by running perform() for each actor subscribed. It assumes each perform() implementation will yield control and operate as a GreenThread.

<!-- Workflows -->


# Reference Implementation for Dynamic Fuzzing #

<!-- workflow_graphs & au -->


# Sample Model Implementation #


# Future Work #


# Conclusion #

