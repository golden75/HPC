## Basic concepts  
Nextflow is a reactive workflow framework and a programming DSL (Domain Speific Language), that enables writing intensive computational pipelines.  
It is based on dataflow programming model.  
: which means it communicate through channels. 


## Processes and Channels  
Nextflow pipline-script is composed of different processes.  
Each process can be written in any language.  

**Processes** are executed independently and isolated from eachother.  
They can communicate via asynchronous FIFO queues, called **Channels**  

Process can have 1 or more channels as input and output. 
Interaction between these are defined by the pipeline execution flow itself.  

### Channels
2 types of channels: queue channel & value channel  

#### Queue channel  
*  unidirectional FIFO queue, which connects two process or operators.  
*  created using factory methods such as : `from`, `fromPath`, `etc` .. or chaining it with channel operator such as `map`, `flatMap` etc..  
*  also created by process output declarations using `into` clause.  




