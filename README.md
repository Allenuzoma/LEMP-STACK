# LEMP-STACK

## IMPLEMENTATION OF THE LEMP STACK ON AWS


This projects explains the processes involved in deploying a LAMP Stack on AWS with the aim of building a static and dynamic web app.


Linux: The operating system that provides the foundation for the stack.

NGINX: The web server software that handles requests and serves web pages on the instruction of the scripting language PHP.

MySQL: The database management system that stores and manages our website's data in a relational database

PHP: The server-side scripting language used to build dynamic web content.

**Step 0: Launch an EC2 instance**
* On the AWS Console, Create an Ubuntu EC2 instance
- Name: Instance name. e.g Allen-LEMP-Server
* AMI: Select Ubuntu 24.04 LTS HVM (64-bit architecture).
+ Instance type: Select the t2.micro machine type (eligible for free tier). ![instance selection](https://github.com/user-attachments/assets/bf795167-e62a-451b-9830-1d2153cd071e)


+ Key Pair: Create a new key pair and download and save the .ppk file as we would be using Putty to SSH into our instance. ![instance keypair](https://github.com/user-attachments/assets/ee06569f-ccab-4abb-8718-572e80a478d1)

    
+ Launch the instance and ensure the status checks is passed indicating readiness of the instance. ![instance ready](https://github.com/user-attachments/assets/fade13c1-ecc8-49bb-a063-fc5f6d821aba)



+ Check the security tab section of the instance to verify the presence of the security group allowing incoming SSH traffic on port 22 to the newly launched instance. ![ssh rule present](https://github.com/user-attachments/assets/225fc294-9120-4a5c-8e26-fcf1adf9d9e9)

+ Establish an SSH connection to the instance using Putty using the public DNS name and the private key downloaded.

    ![putty front page](https://github.com/user-attachments/assets/f7bb6a64-7135-42f7-81b8-7f06672cfe99)


 
    ![auth credential](https://github.com/user-attachments/assets/ce7905e2-f9a4-495d-abca-1d5e845be08a)
+  Select Ubuntu when asked to login as:





    ![putty login as](https://github.com/user-attachments/assets/b1420cf3-1ab6-4467-be31-3f65496d042c)



   
+  We can now start typing commands:






    ![putty after signing in](https://github.com/user-attachments/assets/87e9ba9f-7d63-4632-aa41-6a2a2bf92202)




**Step 1. Install Nginx on the instance**
   
   
   Nginx is a widely-used open-source web server software that allows websites to be hosted on the internet. It serves web content (HTML, PHP, etc.) to users' 
   browsers when they request a website. Nginx 
   is highly configurable, reliable, and can run on various operating systems like Linux, Windows, and macOS.
- Install apache2 using the command:
       sudo apt update
       sudo apt install nginx
       ![sudo install nginx](https://github.com/user-attachments/assets/d0d7b074-818e-4233-afc3-26330b39f5aa)




* Verify nginx is up and running using the command:
       sudo systemctl status nginx
       ![systemctl status nginx](https://github.com/user-attachments/assets/324f3a34-8a10-485a-96d1-374b19e6172c)


+ In order for communication with apache2 we have to visit the security group and create a new inbound rule for http on Port 80 to traffic from any IPV4 address 0.0.0.0/0
       ![image](https://github.com/user-attachments/assets/c38982e2-2374-405a-b902-5d9e0e362bdd)



+ Using curl http://localhost:80 or curl http://127.0.0.1:80 on the terminal or http://<public ip address of the instance> http://18.171.235.246 on a browser to confirm our nginx web server is functioning appropriately
  
  
  ![image](https://github.com/user-attachments/assets/660c8774-37aa-41db-a90a-fb91cc060f14)


  ![image](https://github.com/user-attachments/assets/c97d875a-e106-42d6-8aba-889038f9f4cc)
)









**Step 2: Install MySQL**


  We need to install a database management system to be able to store and manage relational data for our website. 
- To install MySQL we use the command: 
sudo apt install mysql-server
 ![install mysql-server](https://github.com/user-attachments/assets/bcd7ce9a-164b-472d-ba2f-6717fe7c4cd9)


* We then login to the MYSQL console by typing: sudo mysql

  
    ![after install mysql](https://github.com/user-attachments/assets/7d21a283-8d8d-4f9a-92a7-67f64d457d6a)


 We are now connected to the MySQL server as the administrative database root user. We have to set as the root user to prevent unauthorized access into the 
 database. We will do this by setting a password for 
 access control.
  
+ Set MYSQL root user password by entering the command :
 ALTER USER'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password.1';

    ![image](https://github.com/user-attachments/assets/452e95b4-08f1-4678-b6ba-e1a7f58234e2)



- Exit the MySQL shell by typing: >exit
* Next we would start an interact an interactive script that would allow us to remove any insecure default settings and futher lockdown access to our database system using the command:
   sudo mysql_secure_installation ![sudo mysql_secure_installation](https://github.com/user-attachments/assets/b8d523dc-ead6-4638-9dd2-73b5fad8b622)


+ We would be asked to validate password plugin. We will have to choose a level for password validation with different categories. 0 = Low, 1 = Medium and 2 
 =Strong. Recall that the password earlier created 
  for the root user was "password.1" and this matches the level 0. We would stick with it and choose 0.

  

  ![password validation](https://github.com/user-attachments/assets/09a40a19-1caa-41ec-a1a1-4fddec2e9a74)

  
   
  We would be asked to choose yes or no for some other questions. We can select yes to all.

     ![yes to all](https://github.com/user-attachments/assets/dcd55af7-f019-40f4-84d9-a373b2fd3fab)




+ Next we would try to enter the MySQL console using the root user password we created earlier:
      sudo mysql -p

    ![image](https://github.com/user-attachments/assets/113d2ab4-dd43-42c5-ad46-fc36444a7c4a)


  No error means we have successfully accessed the console using the password. We then exit the console by entering the exit command.















**Step 3: Install PHP**



  PHP (Hypertext Preprocessor) is a popular open-source scripting language used primarily for web development, enabling the creation of dynamic and interactive 
 websites. It runs on the server side and is embedded in HTML to handle tasks like form data processing, database interactions, and content management. Nginx 
 requires external program to handle PHP processing and act as bridge between the PHP interpreter and the webserver. This will ensure a better overall 
 performance in PHP websites.
 
  You have to install PHP and two php packages: 1. **php-fpm** module which stands for PHP Fast CGI Process manager. We would tell Nginx to pass PHP request to 
 this software for processing. 2. We install PHP-mysql to allow communication between PHP and MySQL databases. Other packages are installed automatically upon installlation of PHP.
  - Install both modules using the command:
    

    sudo apt install php-fpm php-mysql


    ![image](https://github.com/user-attachments/assets/90058ca8-67b6-4391-8e0e-6c85d461ea70)





  - Verify PHP running using the command

    php -v



    ![image](https://github.com/user-attachments/assets/90e91999-b007-429a-a2cf-467772e49fdf)


   **Step 4: Configure Nginx to Use PHP Processor**

 Next we would create server blocks to encapsulate configuration details and host more than one domain on a single server.

 
 Nginx has a default server block from which it serves documents from the /var/www/html directory.
 We would set up a domain called Project LEMP and this would be located in a new directory /var/www/projectlemp/ which would host our project's files and be 
 the server block from which files are served:


- Create a new directory using the command:
    
       sudo mkdir /var/www/projectlemp/
  

+ Next, we would assign ownership of the directory to the current user using the command:
       sudo chown -R $USER:$USER /var/www/projectlemp


+ We then create a configuration file for our project named projectlemp.conf. This will contain the barebone configuration for the server block created and 
 will be stored in the Nginx site-available directory /etc/nginx/sites-available/.
We would use a command line editor like Nano to create the projectlamp.conf:

     sudo nano /etc/nginx/sites-available/projectlemp.conf

#/etc/nginx/sites-available/projectlemp

server {
    listen 80;
    server_name projectlemp www.projectlemp;
    root /var/www/projectlemp;

    index index.html index.htm index.php;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php8.3-fpm.sock;
     }
    location ~ /\.ht {
        deny all;
     }
        }


![projectlemp conf](https://github.com/user-attachments/assets/c68edfa4-0dea-4c0f-a3a7-0d8fd1ffab67)

 Next we will activate the configuration by linking to the config file from nginx sites-enabled directory:

 
         sudo ln -s /etc/nginx/sites-available/projectlemp/ /etc/nginx/sites-enabled/


 This will tell Nginx to use the newly created barebones server block configuration next time it is reloaded. 
 
 + You can test your configuration for syntax errors in the projectlemp.conf file :
 by typing:

               sudo nginx -t


    ![image](https://github.com/user-attachments/assets/4b019435-9c2f-4d8d-9c57-d48543e4d5ca)

 I had a hassle here. There were some discrepancies in the code. It kept bringing up error message.  I had to make sure my domain name matched the server 
 detailes in the server block code and I made sure every curly bracket was accounted for. 
 
 + Next we will disable the default nginx host currently configured to listen to port 80 using:

               sudo unlink /etc/nginx/sites-enabled/default
+ Reload Nginx:

              sudo systemctl reload nginx

+ Reloading the website shows the Nginx page with a 403 forbidden message

  
![image](https://github.com/user-attachments/assets/e0a6a09b-1619-400c-b1e8-2c4558affb85)





This is because the webroot in /var/www/projectlemp is currently empty. We will have to create amn index.html file with s simple code in our webroot directory to test that the new server block is working as expected.

+ Create the index.html file:

       sudo nano /var/www/projectlemp/index.html



+ Copy the code into the index.html

        <!DOCTYPE html>
                <html lang="en">
                  <body>
                    <h1>Welcome to Project LEMP website</h1>
                    <h1>Created by Allens Uzoma Okwudei!</h1>
                  </body>
                </html>



       


 ![image](https://github.com/user-attachments/assets/ef379739-5345-456a-b9ce-4439a151bbe9)


 
      
 
 

   

    ![apache2 website](https://github.com/user-attachments/assets/7c6fd9cb-9d38-46cd-ad22-ece2622731ae)

 + We would need to disable apache default site so as to have projectlamp website running:
       sudo a2dissite 000-default

 + Next reload Apache using:
       sudo systemctl reload apache2    








   ![refresh website after a2dissite ](https://github.com/user-attachments/assets/055486d6-a840-4422-9bc8-a96a360345a0)





    Now we can see our website is not displaying the apache default page anymore. However projectlamp page is running but it is virtually empty and not displaying our customized information. This is because 
    the webroot is empty. We would correct this by going to the web root /var/www/projectlamp and creating an index.html file in the directory. We would copy the following code into it so that we can test 
    that the virtual host works as expected.
   + Create the index.html file using nano:

        sudo nano /var/www/projectlamp.index.html

     + copy a simple html code for our website:
    
     
                <!DOCTYPE html>
                <html lang="en">
                  <body>
                    <h1>Welcome to Project Lamp's website</h1>
                    <h1>Created by Allens Uzoma Okwudei!</h1>
                  </body>
                </html>


          




 + Reloading our web browser displays a more meaningful webpage which is accessed with the public IP adress or the public DNS name.


    



   ![reloading after webroot index](https://github.com/user-attachments/assets/1208624a-792d-4f48-83de-7db16dbd52d7)


 



**Step 5: Enable PHP on the website**
Index.html files are useful when our website is under maintenance and be used to convey such information to visiting users. However, we need to create a more dynamic webpage, hence the need to create a 
webpage using the index.php component.
But before we create the php component, we would need to correct a default apache feature which makes index.html take precedence over index.php. This will be corrected in the dir.conf file located in the mods-enable directory of apache2:
         sudo nano /etc/apache2/mods-enabled/dir.conf 

            
            
            
            
![dir conf before](https://github.com/user-attachments/assets/6c0298f0-8848-43dc-a6b3-40146746a128)



- Rearrange the code so that index.php comes before index.html:




  ![dir conf before](https://github.com/user-attachments/assets/8d9a3c5c-6741-49fb-83e2-dd3ce89d6693)



- Reload Apache:

  
          sudo systemctl reload apache2


 + We then create the index.php file to confirm that apache is able to handle and process request for php files. To do this, edit the index.php file

                nano /var/www/projectlamp/index.php
   
 
 + And copy the following code into it:

   
                <?php 
                phpinfo();







    ![index php](https://github.com/user-attachments/assets/5d254b5f-c9d5-46e7-a5db-088f0f655777)



 + On refreshing our website, we see the index.php webpage:







 ![index php webpage](https://github.com/user-attachments/assets/733aa40a-d7bb-4524-91f5-d02e27c1e073)








 + Due to the fact that the index.php file carries sensitive information about the PHP environment and the ubuntu server, we have to delete index.php file from the project lamp folder.
                 
                 
                 
                 
                 sudo rm /var/www/projectlamp/index.php



                 

  + Reloading the browser displayed the index.html website again.







 ![image](https://github.com/user-attachments/assets/ab0e807e-4829-4f51-8bc2-0a0fd4ed86ef)



