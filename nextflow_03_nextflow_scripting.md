# Nextflow Scripting   
nexflow documents: [https://www.nextflow.io/docs/latest/script.html](https://www.nextflow.io/docs/latest/script.html)   

Contents   
1.  [Language basics](#language-basics)  
2.  [Implicit variables](#implicit-variables)  
3.  [Regular expressions](#regular-expressions)  
4.  [Files and IO](#files-and-io)  


Nextflow scripting is an extension of Groovy programing language.  
You can install groovy binary and include the path to the bin.  
``` 
module load java/12.0.1  
export PATH=$PATH:"path-to-groovy-4.0.2/bin" 
```  

Once groovy is installed properly, and interactive [groovy shell](https://groovy-lang.org/groovysh.html)  can be produced, to type commands interactivly using:  
```
groovysh   
```  


# Language basics   
```
println "Hello, World!"  
```  

### Variables  
To define variables, simply assign a value  
```
x = 1

x = new java.util.Date()  

x = -3.14  

x = false  

x = "Hi"  
```   

### Lists  
List object can be defined by placing items in square brackets.  
```
myList = [1776, -1, 33]
```  

Access a list value:  
``` 
println myList[0]
println myList.size()
```   


### Maps  
Maps are used to store associative arrays or dictionaries.  

```
scores = [ "Brett":100, "Pete":"Did not finish", "Andrew":86.87934 ]
```  
access values:  
```
println scores["Pete"]
```  

To add data or modify:  
```
scores["Pete"] = 3
scores["Cedric"] = 120
```  

### Multiple assignment  
An array or a list object can used to assign to multiple variables at once:  

```
a, b, c) = [10, 20, 'foo']
```

### Conditional Execution   
``` 
x = Math.random()
if( x < 0.5 ) {
    println "You lost."
}
else {
    println "You won!"
}
```  


### Strings  
Strings can be defined by ' or "" characters.
```
a = "wold" 
print "hello " + a + "\n" 
```

Double-quoated strings support variable interpolations.  ${expression}  
```
oxtype = 'quick'
foxcolor = ['b', 'r', 'o', 'w', 'n']
println "The $foxtype ${foxcolor.join()} fox"

The quick brown fox
```  

### Multi-line strings  
```
text = """
    hello there James
    how are you today?
    """
```  

As in Bash/shell scripts, terminating a line in a multi-line string with a `\` character prevents a new line character from separation.  

```
myLongCmdline = """
    blastp \
    -in $input_query \
    -out $output_file \
    -db $blast_database \
    -html
    """

result = myLongCmdline.execute().text
```  

# Implicit variables   

## Script implicit variables 
following variables are implicitly defined in the script global execulation scope.   

| **Name**   |  **Description**  |   
| :-----  | :-----  |  
|  baseDir  |  The directory where the main workflow script is located  |  lanuchDir  |  The directory where the workflow is run   |  
|  moduleDir  |  Directory where a module script is located   |  
|  nextflow   |  Dictionary like object representing nextflow runtime information   |  
|  params     |  Dictionary like object holding workflow parameters specifing in the config file or as command line options.   |   
|  projectDir  |  The directory where the main script is located   |   
|  workDir     |  directory where tasks temporary files are created  |   
|  workflow    |  Dictionary like object representing workflow runtime information   |   


##  Configuration implicit variables  
The following variables are implicitly defined in the Nextflow configuration file:  

| **Name**   |  **Description**   |   
| :-----  | :-----   |   
|  baseDir  |  The directory where the main workflow script is located  |  
|  lauchDir  |  The directory where the workflow is run  |   
|  projectDir  |  The directory where the main script is located   |   


##  Process implicit variables    
The following variables are implicitly defined in the `task` object of each process.   

| **Name**   |  **Description**    |   
| :-----  | :-----   |  
|  attempt  |  The current task attempt   |  
|  hash  |  The task unique hash Id  |   
|  index  |  The task index (corresponds to task_id in the execution trace)  |   
|  process  |  The current process name   |   
|  workDir  |  The task unique directory. NOTE: This is only available for processes that run native code via the exec: statement.   |   


The `task` object also contains the values of all process directives for a given taks, which allows you to access these settings at runtime.   
e.g:  
```   
process foo {
  script:
  """
  some_tool --cpus $task.cpus --mem $task.memory
  """
}
```  

In the above snippet the task.cpus report the value for the cpus directive and the task.memory the current value for memory directive depending on the actual setting given in the workflow configuration file.   

(more information on **Process directives** topic)   


