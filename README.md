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
* Next we would start an interact an interactive script that would allow us to remove any insecure default settings 
  and futher lockdown access to our database system using the command:



   sudo mysql_secure_installation



  ![sudo mysql_secure_installation](https://github.com/user-attachments/assets/b8d523dc-ead6-4638-9dd2-73b5fad8b622)


+ We would be asked to validate password plugin. We will have to choose a level for password validation with different 
 categories. 0 = Low, 1 = Medium and 2 = Strong. Recall that the password earlier created 
  for the root user was "password.1" and this matches the level 0. We would stick with it and choose 0.

  

  ![password validation](https://github.com/user-attachments/assets/09a40a19-1caa-41ec-a1a1-4fddec2e9a74)

  
   
  We would be asked to choose yes or no for some other questions. We can select yes to all.



  ![yes to all](https://github.com/user-attachments/assets/dcd55af7-f019-40f4-84d9-a373b2fd3fab)




+ Next we would try to enter the MySQL console using the root user password we created earlier:
      sudo mysql -p

    ![image](https://github.com/user-attachments/assets/113d2ab4-dd43-42c5-ad46-fc36444a7c4a)


  No error means we have successfully accessed the console using the password. We then exit the console by entering 
  the exit command.



**Step 3: Install PHP**



  PHP (Hypertext Preprocessor) is a popular open-source scripting language used primarily for web development, 
 enabling the creation of dynamic and interactive 
 websites. It runs on the server side and is embedded in HTML to handle tasks like form data processing, database 
 interactions, and content management. Nginx 
 requires external program to handle PHP processing and act as bridge between the PHP interpreter and the webserver. 
 This will ensure a better overall performance in PHP websites.
 
  You have to install PHP and two php packages: 1. **php-fpm** module which stands for PHP Fast CGI Process manager. 
  We would tell Nginx to pass PHP request to this software for processing. 2. We install PHP-mysql to allow 
 communication between PHP and MySQL databases. Other packages are installed automatically upon installlation of PHP.
  - Install both modules using the command:
    

    sudo apt install php-fpm php-mysql


    ![image](https://github.com/user-attachments/assets/90058ca8-67b6-4391-8e0e-6c85d461ea70)





  - Verify PHP running using the command

    php -v





     ![image](https://github.com/user-attachments/assets/90e91999-b007-429a-a2cf-467772e49fdf)


   **Step 4: Configure Nginx to Use PHP Processor**

 Next we would create server blocks to encapsulate configuration details and host more than one domain on a single 
 server.

 
 Nginx has a default server block from which it serves documents from the /var/www/html directory.
 We would set up a domain called Project LEMP and this would be located in a new directory /var/www/projectlemp/ which 
 would host our project's files and be 
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

   

 I had a bit of a hassle here. There were some discrepancies in the code. It kept bringing up error message.  I had to 
 make sure my domain name matched the server 
 detailes in the server block code and I made sure every curly bracket was accounted for. 
 
 + Next we will disable the default nginx host currently configured to listen to port 80 using:

               sudo unlink /etc/nginx/sites-enabled/default
+ Reload Nginx:

              sudo systemctl reload nginx

+ Reloading the website shows the Nginx page with a 403 forbidden message

  


![image](https://github.com/user-attachments/assets/e0a6a09b-1619-400c-b1e8-2c4558affb85)





This is because the webroot in /var/www/projectlemp is currently empty. We will have to create amn index.html file 
 with s simple code in our webroot directory to test that the new server block is working as expected.

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


 
      
 
 

   

  

 + Reloading our web browser displays a more meaningful webpage which is accessed with the public IP adress or the 
 public DNS name.


    



   ![image](https://github.com/user-attachments/assets/452faa76-e495-41d5-8e83-d2ff11619003)






**Step 5: Testing PHP with Nginx**
We have to see if we can Nginx can hand .php files to the processor. We do this by creating an info.php file in the web root directory.
         sudo nano /var/www/projectlemp/info.php

We enter the following script into the info.php file

        <?php
        phpinfo();




![image](https://github.com/user-attachments/assets/7953ec07-9586-4538-a1d0-47787035f140)



+ On your browser visit http://<ipaddress>/info.php and you will see the php homepage.




![image](https://github.com/user-attachments/assets/66312c77-2a77-419b-88d9-abad25f5d5a1)


            
+ We will have to remove the info.php file as it is a sensitive file that reveals details about server:


                sudo rm /var/www/projectlemp/info.php

Reloading our browser takes us back to the 403 forbidden page.
  
            
            

![dir conf before](https://github.com/user-attachments/assets/6c0298f0-8848-43dc-a6b3-40146746a128)



**Step 6: Retrieving Data From MySQL Database with PHP**
We will create and configure a test database so that nginx can query and display data from it.
We will name the database lemp_database and the user lemp_user
- We will connect to the console with password by typing:

          sudo mysql -p

* Next we create the database by typing:

          CREATE DATABASE lemp_database;

+ We create the new user and assign a new password:

          CREATE USER 'lemp_user'@'%' IDENTIFIED WITH mysql_native_password BY 'password.1';
+ Next we give lemp_user permission over the lemp_database:

  GRANT ALL ON lemp_database.* TO 'lemp_user'@'%';

+ Exit the console and try to log in again to see if the user has all permissions assigned.
      >exit
+ Type in this code to sign into the console as the user. Enter the password created when creating the database user:

    sudo mysql -u lemp_user -p

+ To be able to confirm access and see the database present, enter:
    SHOW DATABASES;


  ![show databases](https://github.com/user-attachments/assets/2c99f462-635a-43df-8507-3a23fd47637e)


We can see our created database lemp_database sitting pretty alongside other default entities

+ We create a table in our database called todo_list:
  
    CREATE TABLE lemp_database.todo_list (
	item_id INT AUTO_INCREMENT,
	content VARCHAR(255),
	PRIMARY KEY(item_id)
    ); 
+ Next we populate our newly created table with contents:

   INSERT INTO lemp_database.todo_list (content) VALUES ("My first important item");
   INSERT INTO lemp_database.todo_list (content) VALUES ("My second important item");
   INSERT INTO lemp_database.todo_list (content) VALUES ("My third important item");
   INSERT INTO lemp_database.todo_list (content) VALUES ("and this one more thing");
  
+ We confirm the presence of the newly created rows of content in our table using:
  
  SELECT * FROM example_database.todo_list;


  The todo_list table looks thus:
  

  ![table content showing](https://github.com/user-attachments/assets/84ab3bcc-592c-4cbe-bc5d-30ece00da2ca)

  + Exit the console
  + Next we would create a php script that would connect to MYSQL and query for content. This script will be located in the webroot folder of our domain:
 
     sudo nano /var/www/projectlemp/todo_list.php

  + Copy the following code into it:

                
                <?php
                $user = "lemp_user";
                $password = "password.1";
                $database = "lemp_database";
                $table = "todo_list";
                
                try {
                  $db = new PDO("mysql:host=localhost;dbname=$database", $user, $password);
                  echo "<h2>TODO</h2><ol>";
                  foreach($db->query("SELECT content FROM $table") as $row) {
                    echo "<li>" . $row['content'] . "</li>";
                  }
                  echo "</ol>";
                } catch (PDOException $e) {
                    print "Error!: " . $e->getMessage() . "<br/>";
                    die();

The output:

![php script for quering db](https://github.com/user-attachments/assets/95b68e0a-90a7-4e18-a172-4421d080a571)

+ Enter your browser and enter your ip address in this format http://<your domain>/todo_list.php. Using our IP address, http://18.171.235.246/todo_list.php


![image](https://github.com/user-attachments/assets/df126ddb-646a-4381-aa17-bb0120cbde89)

We can see all items entered into our table is showing. This confirms that PHP is communicating with MySQL to serve contents from the database.

**THANK YOU**




       


