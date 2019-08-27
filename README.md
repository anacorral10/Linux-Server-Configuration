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
6. Amazon Lightsail does not allow you to log in as a root user, but we can switch to become a root user. Type `$ sudo su -` and boom, we become a root user! 
7. As Udacity requires we need to create a user called `grader`. Type  `$ sudo adduser grader` to create another user 'grader'. It will ask for 2 passwords and then a few other fields which you can leave blank.









