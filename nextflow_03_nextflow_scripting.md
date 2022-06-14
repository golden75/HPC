# Nextflow Scripting   
nexflow documents: [https://www.nextflow.io/docs/latest/script.html](https://www.nextflow.io/docs/latest/script.html)   

Contents   
1.  [Language basics](#language-basics)  
2.  [Implicit variables](#implicit-variables)   
3.  [Closures]()
4.  [Regular expressions](#regular-expressions)  
5.  [Files and IO](#files-and-io)  


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


# Closures  

Briefly, a closure is a block of code that can be passed as an argument to a function. Thus, you can define a chunk of code and then pass it around as if it were a string or an integer.  

More formally, you can create functions that are defined as first class objects.   

```  
square = { it * it }  
```   
The curly brackets around the expression t tells the script interpreter to treat this expression as code.    
```  
println square(9)  
```    

This is not very interesting until we find that we can pass the function square as an argument to other functions or methods  
```
[ 1, 2, 3, 4 ].collect(square)  

[ 1, 4, 9, 16 ]  
```  


# Regular expressions  
Regular expressions are available via the `~/pattern/` syntax and the `=~` and `==~` operators.   

Use =~ to check whether a given pattern occurs anywhere in a string: 
```
assert 'foo' =~ /foo/       // return TRUE
assert 'foobar' =~ /foo/    // return TRUE
```   

Use ==~ to check whether a string matches a given regular expression pattern exactly.  
```
assert 'foo' ==~ /foo/       // return TRUE
assert 'foobar' ==~ /foo/    // return FALSE
```  

*  The ~ operator creates a Java Pattern object from the given string.   
``` 
x = ~/abc/
println x.class
// prints java.util.regex.Pattern
```  
*  The =~ operator creates a Java Matcher object.

``` 
y = 'some string' =~ /abc/
println y.class
// prints java.util.regex.Matcher 
```  

Java’s regular expression language : [Pattern Java Doc](https://docs.oracle.com/javase/7/docs/api/java/util/regex/Pattern.html).  

## String replacement  
```
x = "colour".replaceFirst(/ou/, "o")
println x
// prints: color

y = "cheesecheese".replaceAll(/cheese/, "nice")
println y
// prints: nicenice
```  

## Capturing groups  
First create a matcher object with the =~ operator. Then, you can index the matcher object to find the matches:  
```
programVersion = '2.7.3-beta'
m = programVersion =~ /(\d+)\.(\d+)\.(\d+)-?(.+)/

assert m[0] ==  ['2.7.3-beta', '2', '7', '3', 'beta']
assert m[0][1] == '2'
assert m[0][2] == '7'
assert m[0][3] == '3'
assert m[0][4] == 'beta'
```  

## Removing part of a string  
You can remove part of a String value using a regular expression pattern. The first match found is replaced with an empty String:  

``` 
// define the regexp pattern
wordStartsWithGr = ~/(?i)\s+Gr\w+/

// apply and verify the result
('Hello Groovy world!' - wordStartsWithGr) == 'Hello world!'
('Hi Grails users' - wordStartsWithGr) == 'Hi users'
```  

Remove the first 5-character word from a string:  
```  
assert ('Remove first match of 5 letter word' - ~/\b\w{5}\b/) == 'Remove  match of 5 letter word'
```  

Remove the first number with its trailing whitespace from a string:  
```
assert ('Line contains 20 characters' - ~/\d+\s+/) == 'Line contains characters'
```  


