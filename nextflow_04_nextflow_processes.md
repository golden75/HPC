# Nextflow Processes  

In Nextflow a **process** is the basic processing *primitive* to execute a user script.  

The process definition starts with the keyword `process`, followed by process name and finally the process body delimited by curly brackets.  

The process body must contain a string which represents the command or, more generally, a script that is executed by it. A basic process looks like the following example:  

``` 
process sayHello {
    """
    echo 'Hello world!' > file
    """
}
``` 

A *process* may contain 5 definition blocks:  
1.  directives  
2.  inputs  
3.  outputs  
4.  when clause  
5.  procss script   

```  
process < name > {

   [ directives ]

   input:
    < process inputs >

   output:
    < process outputs >

   when:
    < condition >

   [script|shell|exec]:
   < user script to be executed >

}
```  

Content:   
*  [Script](#script)  
   *  [Scripts à la carte](#scripts-à-la-carte)
   *  [Conditional scripts](#conditional-scripts)
   *  [Template](#template)
   *  [Shell](#shell)
   *  [Native execution](#native-execution)  
*  [Stub](#stub)

## Script  
`script` block is a string statement, that defines the command that is executed by the process to carry out its task.  

* A process contains **only one** script block. It must be the last statment, when the process contains *input* and *output* declarations.  
* The string statement is executed as *bash* in the host system. It can be *command*, *script* or combination of them (as you usually use in terminal shell or bash script).  
* String block can be simple string or multi-line string.  
eg:  
```  
process doMoreThings {
  """
  blastp -db $db -query query.fa -outfmt 6 > blast_result
  cat blast_result | head -n 10 | cut -f 2 > top_hits
  blastdbcmd -db $db -entry_batch top_hits > sequences
  """
}
```  

Like in bash, strings delimited by `"` character support variable substitutions, while strings delimited by `'` do not.  

In the above code `$db` variable is replaced by actual value defined somewhere in the pipline script.   

When you want to acess environment varialbe when using `"` , you need to use `\` character:  
```  
rocess doOtherThings {
  """
  blastp -db \$DB -query query.fa -outfmt 6 > blast_result
  cat blast_result | head -n $MAX | cut -f 2 > top_hits
  blastdbcmd -db \$DB -entry_batch top_hits > sequences
  """
}
```  

### Scripts à la carte  

Process script is interpreted as `bash` in *nextflow* in default. But you can use other languages as well. This can be done is the corresponding sheband declaration:   

  
```
process perlStuff {
    """
    #!/usr/bin/perl

    print 'Hi there!' . '\n';
    """
}

process pythonStuff {
    """
    #!/usr/bin/python

    x = 'Hello'
    y = 'world!'
    print "%s - %s" % (x,y)
    """
}
```   

**NOTE:**
> Since the actual location of the interpreter binary file can differ across platforms, it is wise to use the env command followed by the interpreter name, e.g. #!/usr/bin/env perl, instead of the absolute path, in order to make your script more portable.  


### Conditional scripts  
Complex scripts can use the control statements like: `if`, `switch` etc..  

Process scripts can contain conditional statements by simply prefixing the script block with the keyword `script:`. The interpreter will then evaluate all the following statements as a code block, which will return, what will be executed.   

```
seq_to_align = ...
mode = 'tcoffee'

process align {
    input:
    file seq_to_aln from sequences

    script:
    if( mode == 'tcoffee' )
        """
        t_coffee -in $seq_to_aln > out_file
        """

    else if( mode == 'mafft' )
        """
        mafft --anysymbol --parttree --quiet $seq_to_aln > out_file
        """

    else if( mode == 'clustalo' )
        """
        clustalo -i $seq_to_aln -o out_file
        """

    else
        error "Invalid alignment mode: ${mode}"
}
```   


### Template  
A template is simply a shell script file that Nextflow is able to execute by using the template function as shown below:  


```
process template_example {
    input:
    val STR from 'this', 'that'

    script:
    template 'my_script.sh'
}
```   

Nextflow looks for the `my_script.sh` template file in the directory **templates/** that must exist in the same folder where the **Nextflow script** file is located (any other location can be provided by using an absolute template path).  


### Shell  
The shell block is a string statement that defines the shell command executed by the process to carry out its task. It is an alternative to the Script definition with an important difference, it uses the exclamation mark ! character as the variable placeholder for Nextflow variables in place of the usual dollar character.

In this way it is possible to use both Nextflow and Bash variables in the same piece of code without having to escape the latter and making process scripts more readable and easy to maintain. For example:  

``` 
process myTask {
    input:
    val str from 'Hello', 'Hola', 'Bonjour'

    shell:
    '''
    echo User $USER says !{str}
    '''
}
```  

### Native execution  
Nextflow processes can execute native code other than system scripts as shown in the previous paragraphs.

This means that instead of specifying the process command to be executed as a string script, you can define it by providing one or more language statements, as you would do in the rest of the pipeline script. Simply starting the script definition block with the exec: keyword, for example:  

```
x = Channel.from( 'a', 'b', 'c')

process simpleSum {
    input:
    val x

    exec:
    println "Hello Mr. $x"
}

```  

will display:
```
Hello Mr. b
Hello Mr. a
Hello Mr. c
```


## Stub  
Its possible to define a command `stub`, that replaces the actual process command, when the `-stub-run` or `-stub` command line options are used.  

``` 
process INDEX {
  input:
    path transcriptome

  output:
    path 'index'

  script:
    """
    salmon index --threads $task.cpus -t $transcriptome -i index
    """

  stub:
    """
    mkdir index
    touch index/seq.bin
    touch index/info.json
    touch index/refseq.bin
    """
}
```  

This feature is meant to allow the fast prototyping and test of the workflow logic without using the real commands. The developer can use it to provide a dummy command which is expected to mimic the execution of the real one in a quicker manner. This can also be used as an alternative for the dry-run feature.

>**NOTE**  
>The stub block can be defined before or after the `script` block. When the pipeline is executed with the `-stub-run` option and a process’s stub is not defined, the *script block* is executed.  

