# ansible-zero-to-hero

control node - the machine on you which installed the ansible is called control node


authentiacation to the servers - provide the ssh public key to the target servers and using private key we can talk to them

ssh-copy-id -f "-o IdentityFile <PATH TO PEM FILE>" ubuntu@<INSTANCE-PUBLIC-IP>

ssh-copy-id -f "-o IdentityFile /home/ubuntu/mars-sudhamsh.pem" ubuntu@3.21.190.66 - below is the output, do it as a ubuntu user, no need of switching to root
ubuntu@ip-172-31-24-141:~$ ls
docker-play.yml  first-playbook.yml  inventory.ini  mars-sudhamsh.pem  test
ubuntu@ip-172-31-24-141:~$ pwd
/home/ubuntu
ubuntu@ip-172-31-24-141:~$ ssh-copy-id -f "-o IdentityFile /home/ubuntu/mars-sudhamsh.pem" ubuntu@3.138.202.146
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/ubuntu/.ssh/id_ed25519.pub"
The authenticity of host '3.138.202.146 (3.138.202.146)' can't be established.
ED25519 key fingerprint is SHA256:sAhvDgZSjd9LNn1I1/x2D93/ldCl/+9pCTQu9Zb8W+0.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh -o ' IdentityFile /home/ubuntu/mars-sudhamsh.pem' 'ubuntu@3.138.202.146'"
and check to make sure that only the key(s) you wanted were added.

ubuntu@ip-172-31-24-141:~$ ssh ubuntu@3.138.202.146
Welcome to Ubuntu 24.04 LTS (GNU/Linux 6.8.0-1012-aws x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

 System information as of Fri Sep 13 11:10:32 UTC 2024

  System load:  0.0               Processes:             106
  Usage of /:   22.8% of 6.71GB   Users logged in:       0
  Memory usage: 20%               IPv4 address for enX0: 172.31.28.97
  Swap usage:   0%


Expanded Security Maintenance for Applications is not enabled.

0 updates can be applied immediately.

Enable ESM Apps to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status


The list of available updates is more than a week old.
To check for new updates run: sudo apt update

To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

ubuntu@ip-172-31-28-97:~$ 



we need follow this approach
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

ubuntu@ip-172-31-24-141:~$ ssh ubuntu@3.21.190.66' - next time it won't ask for the password

default inventory location of ansible inventory
/etc/ansible/hosts, but we can create the inventory at any location as well
ansible  all -m ping - no need of mentioning the inventory file explicitly
ansible -i /home/ubuntu/inventory.ini all -m ping - we need to explicitly specify the inventory if we created a file for instance with -i and absolute path of the inventory


ansible -m shell -a "sudo ls /etc/" all

Ansible roles and folder structure - will take the same playbook and implement through ansible roles

ansible-galaxy role init test
ubuntu@ip-172-31-24-141:~/test$ tree . - this is the directory structure created
.
├── README.md
├── defaults
│   └── main.yml
├── files
├── handlers
│   └── main.yml
├── meta
│   └── main.yml
├── tasks
│   └── main.yml
├── templates
├── tests
│   ├── inventory
│   └── test.yml
└── vars
    └── main.yml

9 directories, 8 files


ansible is idempotent

ansible-galaxy role -h 

ubuntu@ip-172-31-24-141:~$ ansible-galaxy role install bsmeding.docker - basically we're pulling it from ansible-galaxy
Starting galaxy role install process
- downloading role 'docker', owned by bsmeding
- downloading role from https://github.com/bsmeding/ansible_role_docker/archive/1.0.11.tar.gz
- extracting bsmeding.docker to /home/ubuntu/.ansible/roles/bsmeding.docker
- bsmeding.docker (1.0.11) was installed successfully
ubuntu@ip-172-31-24-141:~$ ls ~/.ansible/roles - this is where the pulled role from ansible-galaxy is resided
bsmeding.docker
ubuntu@ip-172-31-24-141:~$ sudo vi docker-play.yml - we need to create and point the pulled role from ansible-galaxy, below is the reference
ubuntu@ip-172-31-24-141:~$ cat docker-play.yml 
---
- hosts: all
  become: true
  roles: 
    - bsmeding.docker
  

ubuntu@ip-172-31-24-141:~$ ansible-playbook docker-play.yml 


vault - helps secure passwords, ssh keys and api keys

boto3, aws secret keys and vault to store the keys securely

extra vars(that is passed in the comamnd line arguments -e type=t2.medium) has the highest predendence rather than the defaults and vars in the ansible role

loops and conditionals

ansible-playbook arbitary.yml --vault-password-file vault.pass