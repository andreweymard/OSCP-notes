| **Special Variable** | **Description**                                                                                                                                                                                                                                       |
| -------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `$#`                 | This variable holds the number of arguments passed to the script.                                                                                                                                                                                     |
| `$@`                 | This variable can be used to retrieve the list of command-line arguments.                                                                                                                                                                             |
| `$n`                 | Each command-line argument can be selectively retrieved using its position. For example, the first argument is found at `$1`.                                                                                                                         |
| `$$`                 | The process ID of the currently executing process.                                                                                                                                                                                                    |
| `$?`                 | The exit status of the script. This variable is useful to determine a command's success. The value 0 represents successful execution, while 1 is a result of a failure.                                                                               |
| `$#`                 | In this case, we need just one variable that needs to be assigned to the `domain` variable. This variable is used to specify the target we want to work with. If we provide just an FQDN as the argument, the `$#` variable will have a value of `1`. |
| `$0`                 | This special variable is assigned the name of the executed script, which is then shown in the "`Usage:`" example.                                                                                                                                     |
| `$1`                 | Separated by a space, the first argument is assigned to that special variable.                                                                                                                                                                        |
## String Operators

If we compare strings, then we know what we would like to have in the corresponding value.

|**Operator**|**Description**|
|---|---|
|`==`|is equal to|
|`!=`|is not equal to|
|`<`|is less than in ASCII alphabetical order|
|`>`|is greater than in ASCII alphabetical order|
|`-z`|if the string is empty (null)|
|`-n`|if the string is not null|
## Integer Operators

Comparing integer numbers can be very useful for us if we know what values we want to compare. Accordingly, we define the next steps and commands how the script should handle the corresponding value.

|**Operator**|**Description**|
|---|---|
|`-eq`|is equal to|
|`-ne`|is not equal to|
|`-lt`|is less than|
|`-le`|is less than or equal to|
|`-gt`|is greater than|
|`-ge`|is greater than or equal to|
## File Operators

The file operators are useful if we want to find out specific permissions or if they exist.

|**Operator**|**Description**|
|---|---|
|`-e`|if the file exist|
|`-f`|tests if it is a file|
|`-d`|tests if it is a directory|
|`-L`|tests if it is if a symbolic link|
|`-N`|checks if the file was modified after it was last read|
|`-O`|if the current user owns the file|
|`-G`|if the file’s group id matches the current user’s|
|`-s`|tests if the file has a size greater than 0|
|`-r`|tests if the file has read permission|
|`-w`|tests if the file has write permission|
|`-x`|tests if the file has execute permission|
## Logical Operators

With logical operators, we can define several conditions within one. This means that all the conditions we define must match before the corresponding code can be executed.

|**Operator**|**Description**|
|---|---|
|`!`|logical negotation NOT|
|`&&`|logical AND|
|`\|`|logical OR|
#### Arithmetic Operators

|**Operator**|**Description**|
|---|---|
|`+`|Addition|
|`-`|Subtraction|
|`*`|Multiplication|
|`/`|Division|
|`%`|Modulus|
|`variable++`|Increase the value of the variable by 1|
|`variable--`|Decrease the value of the variable by 1|
## Return Values

When we start a new process, each `child process` (for example, a `function` in the executed script) returns a `return code` to the `parent process` (`bash shell` through which we executed the script) at its termination, informing it of the status of the execution. This information is used to determine whether the process ran successfully or whether specific errors occurred. Based on this information, the `parent process` can decide on further program flow.

|**Return Code**|**Description**|
|---|---|
|`1`|General errors|
|`2`|Misuse of shell builtins|
|`126`|Command invoked cannot execute|
|`127`|Command not found|
|`128`|Invalid argument to exit|
|`128+n`|Fatal error signal "`n`"|
|`130`|Script terminated by Control-C|
|`255\*`|Exit status out of range|