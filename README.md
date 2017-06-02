

# Install LAMP stack on ubuntu
# Preamble
- Installing vagrant environnement
- Getting the last ubuntu trusty release.
-- For this, go to the vagrant repository, for the last Vagrantfile, or add that line to it ´config.vm.box = "ubuntu/trusty64"´
- then ´vagrant up && vagrant ssh´
-done
___
## Task 1 : Select the best repository
`nano /etc/apt/source.lst`
> replace update link by the mirror switch for ubuntu

___
## Task 2 : Update all installed package
sudo su
apt update && apt upgrade -y ´if update succed, upgrade and say yes to all´
more /var/log/apt/history.log ´The last command is the lassat block. The firs block is installed pakage, the second is updated package. Taking the first upgraded packet : python3-problem-report:amd64´
´We see the problem report on https://launchpad.net/ubuntu/+source/apport/2.14.1-0ubuntu3.23.
There is some Security fixes as we see here on the changelogs :
"  [ Marc Deslauriers ] 
  * SECURITY UPDATE: code execution via malicious crash files 
    - Use ast.literal_eval in apport/ui.py, added test to test/test_ui.py. 
    - No CVE number 
    - LP: #1648806 "
___
## Task 3 : Install a LAMP stack (Apache, MySQL, PHP)
- Gain root priviledge : ´sudo su´
- Instal Appache 2 : ´apt-get install apache2 -y´
-- To test the server is up, we need to make a port forwrding to the host. Or attribute a local ip address to the vagrant, by writing on the Vagrantfile ´config.vm.network :forwarded_port, guest: 80, host: 8080´ for the port forwarding or ´config.vm.network "private_network", ip: "192.168.50.4"´ or ´config.vm.network "private_network", type: "dhcp"´ for private networking. Using the gui of virtualbox is also possible, but is one time.
- Installing MySQL + the module for php. ´apt install mysql-server php5-mysql´
- Install the mysql auth module : ´apt install libapache2-mod-auth-mysql´
- Launch : ´mysql_install_db´ to set up the DB.
- Launch : ´mysql_secure_installation´ to remove some dangerous settings.
- Install PHP : ´sudo apt-get install php5 libapache2-mod-php5 php5-mcrypt´
- echo `"<?php \n phpinfo(); \n ?>" > info.php` to test the php connection. The info of environnement and configuration will appear.
___
## Task 4 : Configure file permission
**The least privilege principle in the Apache folder :**
-- Remove ownership and give it to user
`chown -R vagrant /var/www/`  : replace vagrant by the username
-- Give group ownership on folder to Apache
`chown -R :www-data /var/www/` Do not forget the ":" saying that it's the group
-- Apache shoud only execute folder
`find . -type d -exec chmod 610 {} +` : recursively find folder and set execute only for folder
-- Apache shoud only read files :
`find . -type f -exec chmod 640 {} +` : recursively find files and set readonly for groups
### Questions :
- What are the UID and GID of www-data ?
> `id www-data` : the uid and the gid are 33.

- What was the owner, group owner of files `./index.html` and directory `html/`?

> - The owner and group owner was `www-data`.
 - The permission : 755 for folder, 644 for files
 - In the initial configuration, through which permissions (Owner, Group or Others) does the Apache process get read access?
 - The three of them have read access, but as Apache is the owner he check the user section.
 
- Imaginating a more flexible way to share this folder amongs users:
> - Create a group web-share Add root as the only owner Doing critical
changes as modifying content are restricted. Deleting and adding files,
are allowed. We just add a new group to the user and www-data-
 - `addgroup web-share` : creating the group
 - `sudo chown -R :web-share `change file/folder to web-share group
 - `usermod -a -G web-share vagrant` : append the group to user vagrant
 -  `usermod -a -G web-share www-data` : append the group to user www-data 
 - `find . -type d -exec chmod 070 {} +` : allow the group to list execute and write folder. (delete/create files) 
 - `find . -type f -exec chmod 040 {} +` : allow the group to read the file only.
 - !!!!!!! `sevice apache2 restart` : for group change to take effect imediately

## Package metadata
- how much space is used by the metadata in ´/var/lib/apt/lists´?
> `du -h` : output : 126M of metadata

- what is the file that contains the metadata for the packages in the trusty distribution (that is Ubuntu 14.04 LTS), in the main component, format binary, architecture amd64?
> `ls |grep trusty\.main\.binary\.amd64`
- In this file find the lines that describe the package apache2. What other packages whose name contains apache2 does it depend on (look at the line Depends:)?
> `nano $(ls |grep trusty\.main\.binary\.amd64)` : the CTRL+W to find apache2. `CTRL+C` : to know the current line : 977.
> The section "depend" tell us that the package need `apache2-bin` and `apache2-data` to work properly.
- Give a command that list all section names : 
> `cat $(ls |grep trusty\.main\.binary\.amd64)|grep ^Section|cut -f 2 -d ' '|sort|uniq`
- In what section is the xubuntu-desktop package?
> it is on the section  : `universe/metapackages` and found in the main binary amd64 universe file.
- Give a command that simulate the installation : 
> `apt-get --simulate install xubuntu-desktop`
- How much space does this will take : 
> aproximatively 1.4Gb and 929 package will be installed.
- To get the total size of packet installed do : **Warning!!! take care**
> `sudo apt-get --assume-no autoremove "."` : that wil remove all packet of the system. We see that there is currently 1.446Mb -> 1.5Gb of disk space that could be freed.







