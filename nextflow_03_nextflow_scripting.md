# Nextflow Scripting   
Nextflow scripting is an extension of Groovy programing language.  
You can install groovy binary and include the path to the bin.  
``` 
module load java/12.0.1  
export PATH=$PATH:"path-to-groovy-4.0.2/bin" 
```  

Once groovy is installed properly, and interactive groovy shell can be produced, to type commands interactivly using:  
```
groovysh   
```  


## Language basics  
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

## Conditional Execution   
``` 
x = Math.random()
if( x < 0.5 ) {
    println "You lost."
}
else {
    println "You won!"
}
```  


