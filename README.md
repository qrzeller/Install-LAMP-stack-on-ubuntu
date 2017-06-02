

# Install LAMP stack on ubuntu
# Preamble
- Installing vagrant environnement
- Getting the last ubuntu trusty release.
-- For this, go to the vagrant repository, for the last Vagrantfile, or add that line to it ´config.vm.box = "ubuntu/trusty64"´
- then ´vagrant up && vagrant ssh´
-done

## Task 1 : Select the best repository
nano /etc/apt/source.lst
>> replace update link by the mirror switch for ubuntu
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
´
## Task 3 : Install a LAMP stack (Apache, MySQL, PHP)
- Gain root priviledge : ´sudo su´
- Instal Appache 2 : ´apt-get install apache2 -y´
-- To test the server is up, we need to make a port forwrding to the host. Or attribute a local ip address to the vagrant, by writing on the Vagrantfile ´config.vm.network :forwarded_port, guest: 80, host: 8080´ for the port forwarding or ´config.vm.network "private_network", ip: "192.168.50.4"´ or ´config.vm.network "private_network", type: "dhcp"´ for private networking. Using the gui of virtualbox is also possible, but is one time.
- Installing MySQL + the module for php. ´apt install mysql-server php5-mysql´
- Install the mysql auth module : ´apt install libapache2-mod-auth-mysql´
- Launch : ´mysql_install_db´ to set up the DB.
- Launch : ´mysql_secure_installation´ to remove some dangerous settings.
- Install PHP : ´sudo apt-get install php5 libapache2-mod-php5 php5-mcrypt´
- echo ´"<?php \n phpinfo(); \n ?>" > info.php´ to test the php connection. The info of environnement and configuration will appear.
## Task 4 : Configure file permission
- The least privilege principle in the Apache folder :
-- Remove Writting permission to Apache for files
-- Remove ownership and give it to user
> chown -R vagrant /var/www/ ´replace vagrant by the username´
-- Give group ownership on folder to Apache
> chown -R :www-data /var/www/ ´Do not forget the ":" saying that it's the group
-- Apache shoud not have write permission fo folder


