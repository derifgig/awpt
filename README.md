# AWPT
Tool for AWS EC2 connect, via SSM 
# Supported client OS
Linux: Ubuntu, ArchLinux/Manjaro

Windows: not tested
 
MacOS: not tested
# Supported EC2
BETA 1 - Amazon Linux 1,2: by system account ec2-user 
# Requirements
### AWS
Allow SSM connection 
https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager-getting-started-enable-ssh-connections.html#ssh-connections-enable

EC2 should have tag "Name".

# Installation
Clone repo
```
umask 022
sudo git clone https://github.com/derifgig/awpt.git /opt/awpt
```
Create link to you actual binary path
```
sudo ln -s /opt/awpt/bin/awpt /usr/local/bin
```
Add to ~/.ssh/config
```
Host *
    StrictHostKeyChecking False
  
Host i-* mi-*
    ProxyCommand sh -c "aws ssm start-session --target %h --document-name AWS-StartSSHSession --parameters 'portNumber=%p'"    
```
Install AWS tools
* https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2-linux.html
* https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager-working-with-install-plugin.html#install-plugin-linux

Install packages
* jq

## AWS profiles configuration
By defaults project use AWS profile with the same name as project.
```
$ cat ~/.aws/config 
[profile myproject1]
region = us-east-1

$ cat ~/.aws/credentials 
[myproject1]
aws_access_key_id = {id}
aws_secret_access_key = {key}
```
But you can set any AWS profile and credential in project config file:
```
$ cat ${HOME}/.config/awpt/project/myproject1/config
aws_profile=myproject1
```
# Examples of use
```
$ awpt create myproject1
Create new project: myproject1
Create directory: /home/dima/.config/awpt/project/myproject1
Create directory: /home/dima/.ssh/myproject1

##### Manually: add AWS config and credentials 

$ awpt sp myproject1
Set Project: myproject1

$ awpt sync
Reading AWS EC2 instances. Please wait...
EC2 count: 4 /home/dima/.config/awpt/project/myproject1/ec2.list

$ awpt ls
i-0aab60a8dce021bbf eu-myproject1-bastion-0 10.104.30.212 ssh_key 
i-025e6f600820f689d eu-myproject1-app-0 10.104.29.120 ssh_key
i-0117ab998345cd102 eu-myproject1-app-0 10.104.30.218 ssh_key
i-0cea0baa8652080a5 eu-myproject1-rabbitmq-0 10.104.34.123 ssh_key

$ awpt info
AWPT version: 0.1beta
Current Project: myproject1
Config file: /home/dima/.config/awpt/project/myproject1/config
---------
aws_profile=myproject1
-----------
EC2 count: 4

# connect by SSM
$ awpt eu-myproject1-bastion-0

# connect by SSH
$ awpt ssh eu-myproject1-bastion-0

```
## Local configuration
```
# general path
$HOME/.config/awpt/

# project config
$HOME/.config/awpt/{project}/config

# SSH keys for project
$HOME/.ssh/myproject1
```
## Uninstall
```
sudo unlink /usr/local/bin/awpt
rm -rf /opt/awpt 
rm -rf ~/.config/awpt
```