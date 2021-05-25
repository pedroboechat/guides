# How to configure SSH

## On the server:

You just need to install it with:

```bash
sudo apt install openssh-server
```

Then, optionally, if you want to allow to SSH as the root user, edit `/etc/ssh/sshd_config` and then add or uncomment the following line:

```bash
PermitRootLogin yes
```

Remember to check, in case you have a firewall enabled, if the port 22 is allowed.



## On the client (for *passwordless* login):

Create your client's private and public keys:

```bash
ssh-keygen -t rsa -b 4096 -C "your_email@domain.com"
```

Then transfer your public key to the server:

```bash
cat ~/.ssh/id_rsa.pub | ssh remote_username@server_ip_address "mkdir -p ~/.ssh && chmod 700 ~/.ssh && cat >> ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys"
```

