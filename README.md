#Install LAMP stack on ubuntu
##Task 1 :
nano /etc/apt/source.lst
>> replace update link by the mirror switch for ubuntu
##Task 2 :
sudo su
apt update && apt upgrade
more /var/log/apt/history.log
