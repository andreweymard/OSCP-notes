```bash
PermitRootLogin no
PubkeyAuthentication yes
PasswordAuthentication no
X11Forwarding no
Port 4444
AllowUsers andrew
```
Then, we need to add the private key to the SSH agent and create a configuration file for the SSH agent in the `.ssh` directory.

Server Management

```shell-session
cry0l1t3@MyVPS:~$ ssh-add ~/.ssh/cry0l1t3
Enter passphrase for /home/cry0l1t3/.ssh/cry0l1t3:
Identity added: /home/cry0l1t3/.ssh/cry0l1t3 (cry0l1t3@MyVPS)

cry0l1t3@MyVPS:~$ vim ~/.ssh/config
```

In this configuration file we will setup the name we want to use for the server, its IP address or domain, the identity file, the port, the user, and tell SSH to only use the key specified. and not try other keys. This makes the connections a bit faster and substantially more secure.

Code: bash

```bash
Host MyVPS
    HostName <IP Address/domain name>
    IdentityFile ~/.ssh/cry0l1t3
    Port 4444
    User cry0l1t3
    IdentitiesOnly yes
```

With the next command we start the SSH agent in our terminal session. If you want to start the SSH agent automatically, you can add this command to your `~/.bashrc` or `~/.zshrc` file.

Server Management

```shell-session
cry0l1t3@htb:~$ eval $(ssh-agent)
```

Now, we can use SSH client just by typing the name (`MyVPS`) we have set for that server and connect to it.

Server Management

```shell-session
cry0l1t3@htb:~$ ssh MyVPS
```
#### Configure Firewall

Network Security

```shell-session
# Allow incoming connection only from the netbird interface wt0
# Reset ufw to default state
ufw --force reset

# Allow all incoming traffic on wt0 interface
ufw allow in on wt0

# Set default policies
ufw default deny incoming
ufw default allow outgoing

# Enable ufw
ufw --force enable

# Display ufw status
ufw status
```

Optionally, we can harden our SSH server even further by telling it to listen only on the `wt0` interface with the following script.

#### Alternative SSH Configuration

Code: bash

```bash
#!/bin/bash

# Get the IP address of wt0
WT0_IP=$(ip addr show wt0 | grep -oP 'inet \K[\d.]+')

# Backup current SSH configuration
SSHD_CONFIG="/etc/ssh/sshd_config"
BACKUP_CONFIG="/etc/ssh/sshd_config.bak_$(date +%F_%H-%M-%S)"
cp "$SSHD_CONFIG" "$BACKUP_CONFIG"

# Check if ListenAddress is already set
if grep -q "^ListenAddress" "$SSHD_CONFIG"; then
    # Replace existing ListenAddress
    sed -i "s/^ListenAddress.*/ListenAddress $WT0_IP/" "$SSHD_CONFIG"
else
    # Add ListenAddress
    echo "ListenAddress $WT0_IP" >> "$SSHD_CONFIG"
fi

# Ensure SSH is not listening on 0.0.0.0 or ::
sed -i '/^ListenAddress 0.0.0.0/d' "$SSHD_CONFIG"
sed -i '/^ListenAddress ::/d' "$SSHD_CONFIG"

# Test SSH configuration
if sshd -t; then
    echo "SSH configuration is valid."
else
    echo "Error: Invalid SSH configuration. Restoring backup."
    cp "$BACKUP_CONFIG" "$SSHD_CONFIG"
    exit 1
fi

# Restart SSH service
systemctl restart sshd
if [ $? -eq 0 ]; then
    echo "SSH service restarted successfully."
else
    echo "Error: Failed to restart SSH service."
    exit 1
fi
``` 