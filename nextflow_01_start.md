## Installation  

#### Download using terminal  
```
wget -qO- https://get.nextflow.io | bash 
or
curl -s https://get.nextflow.io | bash
```  

Other module files which be needed in running the nextflow:  
```
java/12.0.1  
singularity/3.5.2 
```  


## Getting Started  

### simple script  
[tutorial.nf](documentation/tutorial.nf)

Execute the script by entering the following command  
```
nextflow run tutorial.nf
```  

It will output something like:
```
N E X T F L O W  ~  version 21.10.6
Launching `tutorial.nf` [festering_mayer] - revision: dfa326a346
executor >  local (3)
[91/a790dd] process > splitLetters       [100%] 1 of 1 ✔
[d2/d9a4bc] process > convertToUpper (1) [100%] 2 of 2 ✔
WORLD!
HELLO
```  

in the above, process `convertToUpper` is executed parallel. No way to figure out which chunk (HELLO or WORLD) will be executed first.  

You can change one process and ru-run it by;
```
nextflow run tutorial.nf -resume
```  

```
N E X T F L O W  ~  version 21.10.6
Launching `tutorial.nf` [furious_davinci] - revision: b2372bc3c1
executor >  local (2)
[2f/14f303] process > splitLetters       [100%] 1 of 1, cached: 1 ✔
[a5/1bdbdd] process > convertToUpper (2) [100%] 2 of 2 ✔
olleH
!dlrow
``` 

where the first process is skiped and procss 2 will run its course.  

 

## Pipeline Parameters  
It is simply declared by prepending to a variable name the prefix `params`, separted by dot character.  
```
params.str = 'Hello world!'
```

Its value can be specified on the command line by prefixing the parameter name with a `--paramName`  
```
nextflow run tutorial.nf --str 'Aubowan saha Suba Aluth Awruddak'  
```

which will give:  

```
N E X T F L O W  ~  version 21.10.6
Launching `tutorial.nf` [focused_mahavira] - revision: b2372bc3c1
executor >  local (7)
[8b/2d87ec] process > splitLetters       [100%] 1 of 1 ✔
[5d/28f456] process > convertToUpper (6) [100%] 6 of 6 ✔
ahas n
awobuA
abuS
htulA
ddurwA
ka
``` 


