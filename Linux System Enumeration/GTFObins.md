let’s once again examine the privileges of user `john`, focusing specifically on his sudo privileges. To do this, we can run the command `sudo -l`, which will list all the commands that can be executed using sudo.

Linux Privilege Escalation

```shell-session
john@ubuntu:~$ sudo -l

Matching Defaults entries for john on ubuntu:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\\:/usr/local/bin\\:/usr/sbin\\:/usr/bin\\:/sbin\\:/bin\\:/snap/bin, use_pty

User john may run the following commands on ubuntu:
    (root) NOPASSWD: /usr/bin/nano
    (ALL : ALL) ALL
```

The first part, "Matching Defaults", indicates that sudo commands adhere to specific security rules. These rules include resetting risky settings (`env_reset`), notifying via email about incorrect passwords (`mail_badpass`), ensuring that only safe program locations are used (`secure_path`), and running commands securely (use_pty). Regarding what john can do, there are two significant capabilites:

1. He can run `/usr/bin/nano` as root without typing a password (`NOPASSWD`), making it extremely convenient to edit system files
    
2. He can run any command as any user or group (`(ALL : ALL) ALL`), effectively granting full administrative privileges, though he must enter his password for all commands except for `nano`.
    

The “[Get The F*** Out Bins](https://gtfobins.github.io/)” (`GTFObins`) project documents several techniques to help security professionals understand and mitigate privilege escalation risks. It provides a list of binaries that can be exploited to bypass certain restrictions within specific scenarios/cases. In this case, we are using `nano` with sudo privileges to break out to a root shell. Basically, we are opening the `nano` text editor as the user `root` and need to find a way how to spawn a shell. This can be done in many different ways. One of the ways is shown [here](https://gtfobins.github.io/gtfobins/nano/#sudo) that we will try now.

The sequence "^R^X" (`[CTRL+R]` `[CTRL+X]`) in `nano` opens a command execution prompt, which is then used to `reset` the terminal and spawn a `/bin/bash` shell, in this case, with the privileges of the user used for this `nano` session (`root`). Let’s try it out.