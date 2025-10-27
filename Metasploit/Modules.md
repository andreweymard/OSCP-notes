#### Syntax
```
<No.> <type>/<os>/<service>/<name>
```
#### Index No.
The `No.` tag will be displayed to select the exploit we want afterward during our searches. We will see how helpful the `No.` tag can be to select specific Metasploit modules later.
### Type
The `Type` tag is the first level of segregation between the Metasploit `modules`. Looking at this field, we can tell what the piece of code for this module will accomplish. Some of these `types` are not directly usable as an `exploit` module would be, for example. However, they are set to introduce the structure alongside the interactable ones for better modularization. To explain better, here are the possible types that could appear in this field:

|**Type**|**Description**|
|---|---|
|`Auxiliary`|Scanning, fuzzing, sniffing, and admin capabilities. Offer extra assistance and functionality.|
|`Encoders`|Ensure that payloads are intact to their destination.|
|`Exploits`|Defined as modules that exploit a vulnerability that will allow for the payload delivery.|
|`NOPs`|(No Operation code) Keep the payload sizes consistent across exploit attempts.|
|`Payloads`|Code runs remotely and calls back to the attacker machine to establish a connection (or shell).|
|`Plugins`|Additional scripts can be integrated within an assessment with `msfconsole` and coexist.|
|`Post`|Wide array of modules to gather information, pivot deeper, etc.|
#### OS
The `OS` tag specifies which operating system and architecture the module was created for. Naturally, different operating systems require different code to be run to get the desired results.
#### Service
The `Service` tag refers to the vulnerable service that is running on the target machine. For some modules, such as the `auxiliary` or `post` ones, this tag can refer to a more general activity such as `gather`, referring to the gathering of credentials, for example.
#### Name
Finally, the `Name` tag explains the actual action that can be performed using this module created for a specific purpose.

Note that the hosted file terminations that end in `.rb` are Ruby scripts that most likely have been crafted specifically for use within `msfconsole`. We can also filter only by `.rb` file terminations to avoid output from scripts that cannot run within `msfconsole`. Note that not all `.rb` files are automatically converted to `msfconsole` modules. Some exploits are written in Ruby without having any Metasploit module-compatible code in them. We will look at one of these examples in the following sub-section.
#### Writing and Importing Modules

```shell-session
astrocat@htb[/htb]$ searchsploit -t Nagios3 --exclude=".py"

---------------------------
 Exploit Title                                                                                                                               |  Path
-------------------------
Nagios3 - 'history.cgi' Host Command Execution (Metasploit)                                                                                  | linux/remote/24159.rb
Nagios3 - 'statuswml.cgi' 'Ping' Command Execution (Metasploit)                                                                              | cgi/webapps/16908.rb
Nagios3 - 'statuswml.cgi' Command Injection (Metasploit)                                                                                     |
```
We copy it into the appropriate directory after downloading the exploit. Note that our home folder .msf4 location might not have all the folder structure that the /usr/share/metasploit-framework/ one might have. So, we will just need to mkdir the appropriate folders so that the structure is the same as the original folder so that msfconsole can find the new modules. After that, we will be proceeding with copying the .rb script directly into the primary location.

Please note that there are certain naming conventions that, if not adequately respected, will generate errors when trying to get msfconsole to recognize the new module we installed. Always use snake-case, alphanumeric characters, and underscores instead of dashes.

For example:

    nagios3_command_injection.rb
    our_module_here.rb

#### MSF - Loading Additional Modules at Runtime
```shell-session
astrocat@htb[/htb]$ cp ~/Downloads/9861.rb /usr/share/metasploit-framework/modules/exploits/unix/webapp/nagios3_command_injection.rb
astrocat@htb[/htb]$ msfconsole -m /usr/share/metasploit-framework/modules/
```

#### MSF - Loading Additional Modules
```shell-session
msf6> loadpath /usr/share/metasploit-framework/modules/
```
