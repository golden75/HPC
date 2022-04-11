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

### Channels [link](https://www.nextflow.io/docs/latest/channel.html#channel-page)
2 types of channels: queue channel & value channel  

#### Queue channel  
*  unidirectional FIFO queue, which connects two process or operators.  
*  created using factory methods such as : `from`, `fromPath`, `etc` .. or chaining it with channel operator such as `map`, `flatMap` etc..  

**from method**  
allows to create a channel emitting any sequence of values specified in method argument.  
```
ch = Channel.from(1, 3, 5, 7)
ch.subscribe { prinln "value: $it" }
```

**fromList method**
```
Channel 
	.fromList( ['a', 'b', 'c', 'd'] )
	.view { "value: $it" }  
```

**fromPath method**  
```
myFileChannel = Channel.fromPath( '/data/some/bigfile.txt' ) 
``` 

`**` works as recuresivly  and `*` all matchers  
```
files = Channel.fromPath( '/data/**/**.fa' ) 
```  
returns all files ending in `*.fa` and data folder and recursively in subfolders.   

```
moreFiles = Channel.fromPath( '/data/**/*.fa 
```
returns values in only data subfolder  

```
pairFiles = Channel.fromPath( 'data/file_{1,2}.fa' ) 
```
will get `file_1.fa` and `file_2.fa`   

  



*  also created by process output declarations using `into` clause.  

** into operator**  
is used to create **two** or more copies of the same channel and use each of them to connect a separate process.   

eg:  
```
Channel 
	.from( 'a', 'b', 'c')
	.into{ foo; bar }  

foo.view{ "Foo emit:" +it }
bar.view{ "Bar emit:" +it }  
```  





