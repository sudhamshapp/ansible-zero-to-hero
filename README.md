# ansible-zero-to-hero

control node - the machine on you which installed the ansible is called control node


authentiacation to the servers - provide the ssh public key to the target servers and using private key we can talk to them

ssh-copy-id -f "-o IdentityFile <PATH TO PEM FILE>" ubuntu@<INSTANCE-PUBLIC-IP>

ssh-copy-id -f "-o IdentityFile /home/ubuntu/mars-sudhamsh.pem" ubuntu@3.21.190.66


- this is for passwordless authentication - should be done on control node
root@ip-172-31-24-64:~# history
    1  clear
    2  vi /etc/ssh/sshd_config.d/60-cloudimg-settings.conf  - make it yes and save the file
    3  systemctl restart ssh
    4  history
    5  passwd ubuntu


on the managed node(ansible installed instance)
ubuntu@ip-172-31-24-141:~$ ssh-keygen - this needs to be done first
Generating public/private ed25519 key pair.
Enter file in which to save the key (/home/ubuntu/.ssh/id_ed25519): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/ubuntu/.ssh/id_ed25519
Your public key has been saved in /home/ubuntu/.ssh/id_ed25519.pub
The key fingerprint is:
SHA256:9EZv7VbzZ5mGt8wjwxMdol/H5P+dRXZMUG7JJJoFWNs ubuntu@ip-172-31-24-141
The key's randomart image is:
+--[ED25519 256]--+
|          oo.o.o.|
|         .  * +o.|
|        . .+ E o+|
|       . o . o =.|
|        S o + +=B|
|         . o o.*X|
|            o.==B|
|             B=oB|
|              +==|
+----[SHA256]-----+
ubuntu@ip-172-31-24-141:~$ cd /home/ubuntu/.ssh/
ubuntu@ip-172-31-24-141:~/.ssh$ ls
authorized_keys  id_ed25519  id_ed25519.pub
ubuntu@ip-172-31-24-141:~/.ssh$ ssh-copy-id ubuntu@3.21.190.66 - ssh into the desired managed instance
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/ubuntu/.ssh/id_ed25519.pub"
The authenticity of host '3.21.190.66 (3.21.190.66)' can't be established.
ED25519 key fingerprint is SHA256:QBNCe00AbAJ+21j+raiJyfirI0XQjr/8YAYnipSWAOU.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
ubuntu@3.21.190.66's password: 

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'ubuntu@3.21.190.66'"
and check to make sure that only the key(s) you wanted were added.

ubuntu@ip-172-31-24-141:~/.ssh$ 
