# Linux-Server-Configuration

## Udacity Fullstack Nanodegree Capstone Project
The goal of this Capstone project is to deploy a web application built from a previous project to a publicly accessible Ubuntu linux server. I use AWS Lightsail instance as the Virtual linux server and Ubuntu as its operating system for this project.

## IP & Hostname
> Public IP: 3.95.159.148\
> Hostname: ec2-3-95-159-148.compute-1.amazonaws.com\
> Ubuntu Server Username: grader\
> SSH port: 2200

### Software:
* Openssh Server
* Apache2
* PostgreSQL
* GIT
* mod_wsgi
* Python
* virtualenv
* Flask
* requests
* httplib2
* sqlalchemy
* psycopg2
* oauth2client
* render_template
* sqlalchemy_utils
* redirect

## Project Detail
### Amazon Lightsail Set Up

1. Go to the Amazon Lightsail website
2. Click get started for free
3. Create your first instance
4. Select OS Only and Ubuntu
5. Scroll down and name your instance whatever you'd like and click create.
6. The instance needs 5~10 mins to set up. After it is set up, you will see 'running' in the left corner of the status card. Write down the public IP address on a paper as you will use it a lot in the following steps 
7. Once running click on it and click "Account Page" at the bottom so you can download your private SSH key.
Click the 'Download' to download your private key, it should go to your Download folder by default. The file type is .pem and will be used to SSH into the server. 
8. The last thing we will need to do is configure the ports Amazon Lightsail will allow. By default the firewall is set to only allow connects from port 22 and port 80. We need to set up port 2200 and 123.
9. Click the 'Networking' tab and find the 'Add another' at the bottom. 
10. From this tab click add another under "Firewall" and choose **Custom** for application, **TCP** for protocol, and the port number under **Port Range**. Then click save.

## Server Configuration
1. On Mac you will want to store all of your SSH Keys in the `.ssh` folder which is located in a folder called .ssh at the root of your user directory. For example `Macintosh HD/Users/[Your username]/.ssh/`
2. If you cannot see that folder in the finder open the terminal and type: `$ killall Finder` then type `$ defaults write com.apple.finder AppleShowAllFiles TRUE`
3. Move your downloaded `.pem` public key file into the .ssh folder that is at the root of Finder
4. To make our public key usable and secure type `$ chmod 600 ~/.ssh/YourAWSKey.pem` into the terminal.
5. We now use this key to log into our Amazon Lightsail Server as the user ubuntu: `$ ssh -i ~/.ssh/YourAWSKey.pem ubuntu@3.95.159.148`

### Create new account grader
6. Amazon Lightsail does not allow you to log in as a root user, but we can switch to become a root user. Type `$ sudo su -` and boom, we become a root user! 
7. As Udacity requires we need to create a user called `grader`. Type  `$ sudo adduser grader` to create another user 'grader'. It will ask for 2 passwords and then a few other fields which you can leave blank.
8. We must create a file to give the user grader superuser privileges. To do this type `$ sudo nano /etc/sudoers.d/grader`. This will create a new file that will be the superuser configuration for grader. When nano opens type `grader ALL=(ALL:ALL)`, to save the file hit Ctrl-X on your keyboard, type 'Y' to save, and return to save the filename.
9. In order to prevent the $ sudo: unable to resolve host error, edit the hosts by `$ sudo nano /etc/hosts`, and then add `127.0.1.1 ip-10-20-37-65` under `127.0.1.1:localhost`

### Install updates and finger package
10. It is a good practice to update & upgrade all softwares in order to keep the system secure.
    * `sudo apt-get update`
    * `sudo apt-get upgrade`
    * `sudo apt-get dist-upgrade` (if some packages are kept back)
11. We will also install a useful tool called **Finger** with the command `$ sudo apt-get install finger`. This tool will allow us to see the users on this server.

### Keygen
12. Now we must create an SSH Key for our new user **grader**. Open a new Terminal window (Command+N) and input `$ ssh-keygen -f ~/.ssh/udacity_key.rsa`
13. In the same terminal we need to read and copy the public key using the command: `$ cat ~/.ssh/udacity.rsa.pub`. Copy the key from the terminal.
14. Going back to the first terminal window where you are logged into Amazon Lightsail as the root user, move to grader's folder by `$ cd /home/grader`
15. Create a directory called .ssh with the command `$ mkdir .ssh`
16. Create a file to store the public key: `$ touch .ssh/authorized_keys` 
17. Edit the authorized_keys file `$ nano .ssh/authorized_keys`. Paste in the public key
18. We must change the permissions of the file and its folder by running
    * `$ sudo chmod 700 /home/grader/.ssh`
    * `$ sudo chmod 644 /home/grader/.ssh/authorized_keys` 
19. Change the owner of the .ssh directory from **root** to **grader** by using the command `$ sudo chown -R grader:grader /home/grader/.ssh`
20. Restart the ssh service: `$ sudo service ssh restart`
21. Type `$ ~.` to disconnect from Amazon Lightsail server
22. Log into the server as grader: `$ ssh -i ~/.ssh/udacity_key.rsa grader@3.95.159.148`

### Enforce key based authentication
23. We now need to enforce the key-based authentication: `$ sudo nano /etc/ssh/sshd_config`. Find the *PasswordAuthentication* line and change text after to `no`. After this, restart ssh again: `$ sudo service ssh restart`

### Change Port
24. We now need to change the ssh port from 22 to 2200, as required by Udacity: `$ sudo nano /etc/ssh/ssdh_config` Find the *Port* line and change **22** to **2200**. Restart ssh: `$ sudo service ssh restart`
25. Disconnect the server by `$ ~.` and then log back through port 2200: `$ ssh -i ~/.ssh/udacity_key.rsa -p 2200 grader@3.95.159.148`

### Disable root login
26. Disable ssh login for **root** user, as required by Udacity: `$ sudo nano /etc/ssh/sshd_config`. Find the *PermitRootLogin* line and edit to `no`. Restart ssh `$ sudo service ssh restart`

### Configure UFW
27. From here we need to configure the Firewall to support our applications and ports needed for this project:
    * `$ sudo ufw allow 2200/tcp` to allow the server listen on port 2200
    * `$ sudo ufw allow 80/tcp`
    * `$ sudo ufw allow 123/udp`
    * `$ sudo ufw enable`
28. Running `$ sudo ufw status` should show all of the allowed ports with the firewall configuration.

## Deploy Catalog Application
We will use a Python virtual machine, Apache2 with mod_wsgi, and PostgreSQL to host our application. Before completeing the following steps, ssh to the Amazon Terminal through your Terminal with grader.

### Install Apache and GIT
1. Install required packages:
   * `$ sudo apt-get install apache2`
   * `$ sudo apt-get install libapache2-mod-wsgi python-dev`
   * `$ sudo apt-get install git`
   
### Enable mod_wsgi
2. Enable mod_wsgi by `$ sudo a2enmod wsgi` and start the web server by `$ sudo service apache2` start or `$ sudo service apache2 restart`. You should input the public IP address and you should see a page like below. If you do not see the page, you have to check the error message and google a solution: ![Apache](/apache2.png)

### Setup Folder Structure
3. We now have to create a directory for our catalog application and make the user grader the owner: 
   * `$ cd /var/www`
   * `$ sudo mkdir catalog`
   * `$ sudo chown -R grader:grader catalog`
   * `$ cd catalog`
4. **Structure:** In this directory we will have our **catalog.wsgi** file `var/www/catalog/catalog.wsgi`, our virtual environment directory which we will create soon and call **venv** `/var/www/catalog/venv`, and also our application which will sit inside of another directory called **catalog** `/var/www/catalog/catalog`.
   
### Clone Catalog Project
5. Now we clone the project from Github: `$ git clone [your link] catalog`












