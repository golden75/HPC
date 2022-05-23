## Basic concepts  
Nextflow is a reactive workflow framework and a programming DSL (Domain Speific Language), that enables writing intensive computational pipelines.  
It is based on dataflow programming model.  
: which means it communicate through channels.   

Contents  
*   [Processes and Channels](#processes-and-channels)  
*   [Execution abstraction]()


## Processes and Channels  
Nextflow pipline-script is composed of different processes.  
Each process can be written in any language.  

**Processes** are executed independently and isolated from eachother.  
They can communicate via asynchronous FIFO queues, called **Channels**  

Process can have 1 or more channels as input and output. 
Interaction between these are defined by the pipeline execution flow itself.  

### Channels [link](https://www.nextflow.io/docs/latest/channel.html#channel-page)
2 types of channels: queue channel & value channel  

### Queue channel  
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




### Value channel  
created using value factory methods or by operators returning single value.  
eg: `first`, `last`, `collect`, `count`, `min`, `max`, `reduce`, `sum`   

``` 
process foo {
  input:
  val x from 1
  output:
  file 'x.txt' into result

  """
  echo $x > x.txt
  """
}
```

single input created inplicitly is a value channel.  
Also the `result` output is a value channel, and can be read by more than one process.   



### Channel factory  
channels may be created implicitly by process output(s) declaration or explicitly by following channel factory methods.   

*  `create`
*  `empty`  
*  `from`  
*  `fromPath`  
*  `fromFilePairs`  
*  `fromSRA`  
*  `of`  
*  `value`  
*  `watchPath`  


## Execution abstraction  

Process defines what command/script to be executed.  
Executor determines how that the script is actually run on the target system.  
My defining the targe execution platform in the configuration file, the pipeline can be run on local computer, grid platform or the cloud.  

## Scripting language  
Nextflow scripting is an extenstion of the Groovy. (Groovy is a superset of Java).  To lean [nexflow scripting](https://www.nextflow.io/docs/latest/script.html#script-page) :  



## Configuration options  
Pipeline configuration options are defined in the `nexflow.config` file, which is placed in the executiion directory.  

This file contains:  
*  which executor to use 
*  processes enviornment varialbes
*  pipeline parameters   

``` 
process {
  executor='sge'
  queue = 'cn-el6'
}
```  

