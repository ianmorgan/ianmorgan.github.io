# Introduction 

A number of small microservices to explore various aspects of 
their design and development. Fairly heavily influenced by event sourcing,
CQRS and Domain Driven Design

Some basic principles:

* Follow [Twelve-Factor App](https://12factor.net/) guidelines unless there is a good reason not to.
* Isolate from the underlying cloud platform where reasonable, for ease of local development if nothing else.
* Be consistent in API design & dev ops tooling where reasonable. Some divergence over time is to be expected, but 
aim to keep this clear, a "generation 1" service, a "generation 2" and so on. 


  
