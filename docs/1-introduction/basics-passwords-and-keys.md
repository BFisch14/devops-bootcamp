# Passwords and Keys

As you use more tools, services, networks and hosts you will need ways to securely access them. It is important to understand how different authentication schemes work, what their pros and cons are and adopt best practices for managing them.

![](img1/fingerprint.svg ':size=150x150 :class=icon')

## Passwords

Passwords are the simplest and most common type of authentication. Passwords can be convenient when they are easy to remember but they are also less secure if they are too simple. There are a plethora of techniques for breaking weak passwords so it is always important to follow best practices for keeping your passwords secure.

Best practices:
- Use longer passwords to make them more secure: usually a minimum of 8 characters.
- Use a combination of numbers, upper and lower case letter and symbols to make passwords more complicated.
- Do not use dictionary words or character substitutions.
- Do not use the same passwords for different accounts.
- Change your passwords periodically.

Keeping passwords secure also makes them more complicated and less convenient. Luckily there are password managers which can help create and manage passwords.

### KeePass
KeePass lets you manage your passwords in a local encrypted database so you only have to worry about remembering one password.

https://keepass.info/

`brew cask install keepassxc`

### Lastpass
Lastpass is an online password management system which makes it easier to manage passwords across multiple devices or to share them with groups of people (premium feature). It also has browser plugins and mobile apps to make it more convenient.

https://www.lastpass.com

## Public Key authentication
Public key authentication involves generating a pair of cryptographic keys: one private and one public. The public key is distributed to hosts that provide the authentication and the client signs the authentication request with the private key in a way that can be verified by the public key without revealing what the private key is.

![](img1/SSH.svg ':size=100x100 :class=icon')

### SSH
SSH makes use of public key authentication to allow passwordless and non-interactive access to remote hosts. To use public key authentication with SSH you must generate a pair of SSH keys, copy the public key to a SSH server and add it to an `authorized_keys` file.

?> **Note:** `.ssh/` needs a permission level of 0700, and `.ssh/authorized_keys` needs a permission level of 0600 to authenticate properly. Permissions can be changed using the `chmod` command.

```bash
# Generate SSH keys. Use the default file and empty passphrase for the keys.
ssh-keygen

# Copy public key to SSH server
scp ~/.ssh/id_rsa.pub <HOST_NAME>:

# SSH to host
ssh <HOST_NAME>

# Append public key to authorized_keys
mkdir ~/.ssh
cat id_rsa.pub >> ~/.ssh/authorized_keys
rm id_rsa.pub
```

SSH will automatically try to authenticate with the .ssh/id_rsa key so you should be able to log into the host without a password now.

> **Note:** The security of public key authentication depends on the private key remaining secret. It should never be shared and the file permission should only readable by the owner.

#### Tips and Tricks
You may need to log into different hosts with different user names and keys. You can specify these as SSH arguments `ssh <USER>@<HOST_NAME> -i PRIVATE_KEY_FILE` but it can be this inconvenient. You can use the SSH config file to set defaults for different hosts so you don't need to type out the same arguments over and over.


```
# .ssh/config

Host my_host
    Hostname ec2-42-42-42-42.us-west-2.compute.amazonaws.com
    User ec2-user
    IdentityFile ~/.ssh/host_public_key.pub
```

Now you can simply connect with `ssh my_host` and it will use the full hostname, user and identity file automatically.

# Deliverable
Create a pair of SSH keys, add the public key to an SSH server and verify you can log in without your password.

What are some advantages / disadvantages of public key and password authentication?
