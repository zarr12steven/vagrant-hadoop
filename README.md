vagrant-hadoop
==============

some vagrant examples for different hadoop cluster deployment model

Requirement
===========

 * OS: tested with [Ubuntu](http://www.ubuntu.com) Desktop 12.04 LTS 64 bit -- [Download ISO](http://www.ubuntu.com/start-download?distro=desktop&bits=64&release=lts)
 * [git](http://git-scm.com/) : tested with 1.7.9.5-1
 * [VirtualBox](http://www.virtualbox.org) : tested with 4.3.10
 * [Vagrant](http://www.vagrantup.com) : tested with 1.4.3+
 * [vagrant-aws plugin](https://github.com/mitchellh/vagrant-aws) : tested with 0.4.1
 * Amazon EC2 API Tools - [ec2-api-tools](http://docs.aws.amazon.com/AWSEC2/latest/CommandLineReference/command-reference.html) : tested with 1.5.0.0-0ubuntu1  *(Optional)*

-----

These scripts are tested on Ubuntu Desktop 12.04 LTS **64 bit** GNU/Linux.
Why **64 bit**? Because [Apache BigTop](http://bigtop.apache.org) only provides 64 bit deb/rpm packages.
Besides, to run 64 bit VirtualBox images, you also need a 64 bit host OS.
```
### How to check your OS version
jazz@EA-dev:~$ uname -a
Linux EA-dev 3.8.0-29-generic #42~precise1-Ubuntu SMP Wed Aug 14 16:19:23 UTC 2013 x86_64 x86_64 x86_64 GNU/Linux
jazz@EA-dev:~$ lsb_release -a
No LSB modules are available.
Distributor ID:	Ubuntu
Description:  Ubuntu 12.04.4 LTS
Release:  12.04
Codename: precise
```
To checkout the github repository, you need to install `git`.
```
### Install git on Ubuntu 12.04
jazz@EA-dev:~$ sudo apt-get -y install git
```
Some scripts are tested on local virtualbox VMs, you need to install virtualbox before running these scripts. 
Due to PXE boot issue of some NICs, we suggest to run official virtualbox instead of ubuntu package (4.1.12_Ubuntur77245).
Please install official [virtualbox rpm/deb/exe](https://www.virtualbox.org/wiki/Downloads) for your environment.
```
### Install VirtualBox on Ubuntu 12.04
jazz@EA-dev:~$ echo "deb http://download.virtualbox.org/virtualbox/debian precise contrib" > virtualbox.list
jazz@EA-dev:~$ sudo mv virtualbox.list /etc/apt/sources.list.d/
jazz@EA-dev:~$ wget -q http://download.virtualbox.org/virtualbox/debian/oracle_vbox.asc -O- | sudo apt-key add -
jazz@EA-dev:~$ sudo apt-get update; sudo apt-get -y install virtualbox-4.3
### How to check your virtualbox version
jazz@EA-dev:~$ vboxmanage --version
4.3.10r93012
```
These scripts are based on [Vagrant 2 syntex](http://docs.vagrantup.com/v2/vagrantfile/version.html), so you can not use the default ubuntu package (1.0.1). 
Please install [official vagrant rpm/deb/exe](http://www.vagrantup.com/downloads.html) for your environment.
```
### Install official vagrant package on Ubuntu
jazz@EA-dev:~$ wget https://dl.bintray.com/mitchellh/vagrant/vagrant_1.5.2_x86_64.deb
jazz@EA-dev:~$ sudo dpkg -i vagrant_1.5.2_x86_64.deb 
### How to check your vagrant version
jazz@EA-dev:~$ vagrant --version
Vagrant 1.5.2
```
To run virtual machines on Amazon EC2, you need to [register AWS account](https://portal.aws.amazon.com/gp/aws/developer/registration/index.html). We will use [Amazon EC2 API Tools](http://aws.amazon.com/developertools/351) to simplify some web operations. Please reference Ubuntu community Wiki ["EC2 Starter's Guide" ](https://help.ubuntu.com/community/EC2StartersGuide) to register account, intall ec2-api-tools and configure environment variables.
```
### Install ec2-api-tools on Ubuntu
jazz@EA-dev:~$ sudo apt-get -y install ec2-api-tools
### How to check your ec2-api-tools
jazz@EA-dev:~$ dpkg -l ec2-api-tools 
Desired=Unknown/Install/Remove/Purge/Hold
| Status=Not/Inst/Conf-files/Unpacked/halF-conf/Half-inst/trig-aWait/Trig-pend
|/ Err?=(none)/Reinst-required (Status,Err: uppercase=bad)
||/ Name                           Version                        Description
+++-==============================-==============================-============================================================================
ii  ec2-api-tools                  1.5.0.0-0ubuntu1               Amazon EC2 API tools
```
set up your Amazon API credentials. Go to [Account -> Security Credentials](https://console.aws.amazon.com/iam/home?#security_credential)
- click "Access Keys (Access Key ID and Secret Access Key)" tab
- Create a new Certificate
- Download the root key and save it in ~/.ec2/rootkey.csv
- Make your credential files private: chmod go-rwx ~/.ec2/*.csv 
```
jazz@EA-dev:~$ mkdir -p ~/.ec2
### download Access Key Certificates and private key into ~/.ec2
jazz@EA-dev:~$ chmod go-rwx ~/.ec2/*.csv
### generate a shell script for furthur 
jazz@EA-dev:~$ source ~/.ec2/rootkey.csv
jazz@EA-dev:~$ cat > ~/.ec2/ec2_keys << EOF
export EC2_URL=https://ec2.ap-southeast-1.amazonaws.com
export AWS_ACCESS_KEY=$AWSAccessKeyId
export AWS_SECRET_KEY=$AWSSecretKey
export V_AWS_ACCESS_KEY_ID=$AWSAccessKeyId
export V_AWS_SECRET_ACCESS_KEY=$AWSSecretKey
export V_KEYPAIR_NAME=vagrant
export V_KEYPAIR_PATH=~/.ec2/vagrant.pem
export V_SEC_GROUPS=vagrant
EOF
jazz@EA-dev:~$ source ~/.ec2/ec2_keys
### generate keypair named by 'vagrant' and store the private key in ~/.ec2/vagrant.pem
jazz@EA-dev:~$ ec2-create-keypair vagrant > ~/.ec2/vagrant.pem
```
To run some example vagrant script for AWS EC2, please install vagrant-aws plugin.
```
### Install vagrant-aws plugin
jazz@EA-dev:~$ vagrant plugin install vagrant-aws
### How to check installed plugins
jazz@EA-dev:~$ vagrant plugin list
vagrant-aws (0.4.1)
vagrant-login (1.0.1, system)
vagrant-share (1.0.1, system)
```
-----
