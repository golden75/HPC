# Nextflow Scripting   
nexflow documents: [https://www.nextflow.io/docs/latest/script.html](https://www.nextflow.io/docs/latest/script.html)   

Contents   
1.  [Language basics](#language-basics)  
2.  [Implicit variables](#implicit-variables)   
3.  [Closures](#closures)
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


# Files and I/O  
file method returns a file system object, given a file path string  
```
myFile = file('some/path/to/my_file.file')
```   

When you use wildcard characters, `*`, `?`, `[]`, `{}` it returns a list object holding paths of files or empty list.  
```  
listOfFiles = file('some/path/*.fa')
```  

Two asterisks `**` also search subdirectories.   

To find hidden files:
```
listWithHidden = file('some/path/*.fa', hidden: true)
```  

`file` options :  
| **Name**   |  **Description**   |  
| :-----  | :-----  |  
| glob  |  When true interprets characters *, ?, [] and {} as glob wildcards, otherwise handles them as normal characters (default: true)  |  
|  type  |  Type of paths returned, either file, dir or any (default: file)  |  
|  hidden  |  When true includes hidden files in the resulting paths (default: false)  |  
|  maxDepth  |  Maximum number of directory levels to visit (default: no limit)  |  
| followLinks  |   When true follows symbolic links during directory tree traversal, otherwise treats them as files (default: true)  |  
|  checkIfExists  |  When true throws an exception of the specified path do not exist in the file system (default: false)  |  

See also: [Channel.fromPath](https://www.nextflow.io/docs/latest/channel.html#channel-path).  


## Basic read/write  
*  reading a file is as easy as getting the value of the file’s text property, which returns the file content as a string value:  
```
print myFile.text
```  

*  save a string value to a file by simply assigning it to the file’s text property:  
```
myFile.text = 'Hello world!'
```   

*  to append a string value to a file without erasing existing content :  
```
myFile.append('Add this line\n') 
```  

*  Or use the left shift operator, a more idiomatic way to append text content to a file:
``` 
myFile << 'Add a line more\n'  
```  

##  Read a file line by line  
Dont use the below to read big files:  
```
myFile = file('some/my_file.txt')
allLines = myFile.readLines()
for( line : allLines ) {
    println line
}
``` 

For Big files, use method `eachLine`  
``` 
count = 0
myFile.eachLine { str ->
    println "line ${count++}: $str"
} 
```  

## Advanced file reading operations  
The classes `Reader` and `InputStream` provide fine control for reading text and binary files, respectively.  

The method `newReader` creates a `Reader` **object** for the given file that allows you to read the content as single characters, lines or arrays of characters 

```
myReader = myFile.newReader()
String line
while( line = myReader.readLine() ) {
    println line
}
myReader.close()
```  

The method `withReader` works similarly, but automatically calls the close method for you when you have finished processing the file. So, the previous example can be written more simply as  
```
myFile.withReader {
    String line
    while( line = it.readLine() ) {
        println line
    }
}
```  



The methods newInputStream and withInputStream work similarly. The main difference is that they create an InputStream object useful for writing binary data.  

## Advanced file writing operations  
The Writer and OutputStream classes provide fine control for writing text and binary files.  

For example, given two file objects sourceFile and targetFile, the following code copies the first file’s content into the second file, replacing all U characters with X:  
```
sourceFile.withReader { source ->
    targetFile.withWriter { target ->
        String line
        while( line=source.readLine() ) {
            target << line.replaceAll('U','X')
        }
    }
}
```  

Read the Java documentation for the Writer, PrintWriter and OutputStream classes to learn more about methods available for writing data to files.   


## List directory content  
```
myDir = file('any/path')
```  

The simplest way to get a directory list is by using the methods list or listFiles  
```
allFiles = myDir.list()
for( def file : allFiles ) {
    println file
}
```

## Create directories  
Given a file variable representing a nonexistent directory, like the following:  
```
myDir = file('any/path')
```

the method mkdir creates a directory at the given path, returning true if the directory is created successfully, and false otherwise:  

```
result = myDir.mkdir()
println result ? "OK" : "Cannot create directory: $myDir"
```

The method mkdirs creates the directory named by the file object, including any nonexistent parent directories:  
```
myDir.mkdirs()
```  

## Create links  
Given a file, the method mklink creates a file system link for that file using the path specified as a parameter:
```
myFile = file('/some/path/file.txt')
myFile.mklink('/user/name/link-to-file.txt')
```  

## Copy files  
The method copyTo copies a file into a new file or into a directory, or copies a directory to a new directory:  
```
myFile.copyTo('new_name.txt')
```  

When the source file is a directory, all its content is copied to the target directory:  
```
myDir = file('/some/path')
myDir.copyTo('/some/new/path')
```  

## Move files  
You can move a file by using the method moveTo:  
```
myFile = file('/some/path/file.txt')
myFile.moveTo('/another/path/new_file.txt')
```  

## Rename files  
```
myFile = file('my_file.txt')
myFile.renameTo('new_file_name.txt')
```  

## Delete files  
```
myFile = file('some/file.txt')
result = myFile.delete()
println result ? "OK" : "Cannot delete: $myFile"
```

Delete directory and subdirectory use method `deleteDir`  

## Check file attributes  
The following methods can be used on a file variable created by using the `file` method:  

| **Name**   |  **Description**    |  
| :-----  | :-----   |  
|  getName  |  Gets the file name e.g. /some/path/file.txt -> file.txt  |  
|  getBaseName  |  Gets the file name without its extension e.g. /some/path/file.tar.gz -> file.tar  |  
|  getSimpleName  |  Gets the file name without any extension e.g. /some/path/file.tar.gz -> file  |  
|  getExtension  |  Gets the file extension e.g. /some/path/file.txt -> txt  |  
|  getParent  |  Gets the file parent path e.g. /some/path/file.txt -> /some/path  |  
|  size  |  Gets the file size in bytes  |  


For example, the following line prints a file name and size:  
```
println "File ${myFile.getName() size: ${myFile.size()}"
```  

## Get and modify file permissions  
getPermissions returns a 9-character string representing the file’s permissions using the Linux symbolic notation e.g. rw-rw-r--:  
```
permissions = myFile.getPermissions()
```  

Similarly, the method setPermissions sets the file’s permissions using the same notation  
```
myFile.setPermissions('rwxr-xr-x')
```  

A second version of the setPermissions method sets a file’s permissions given three digits representing, respectively, the owner, group and other permissions:  
```
myFile.setPermissions(7,5,5)
```

## HTTP/FTP files  
Nextflow provides transparent integration of HTTP/S and FTP protocols for handling remote resources as local file system objects. Simply specify the resource URL as the argument of the file object:  
```
pdb = file('http://files.rcsb.org/header/5FID.pdb')
```  

## Counting records  
### countLines  
The countLines methods counts the lines in a text files.  
```
def sample = file('/data/sample.txt')
println sample.countLines()
```

### countFasta  
The countFasta method counts the number of records in FASTA formatted file.  
```
def sample = file('/data/sample.fasta')
println sample.countFasta()
```  

### countFastq  
```
def sample = file('/data/sample.fastq')
println sample.countFastq()
``` 

