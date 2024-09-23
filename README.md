# LEMP-STACK

## IMPLEMENTATION OF THE LEMP STACK ON AWS


This projects explains the processes involved in deploying a LAMP Stack on AWS with the aim of building a static and dynamic web app.


Linux: The operating system that provides the foundation for the stack.

NGINX: The web server software that handles requests and serves web pages on the instruction of the scripting language PHP.

MySQL: The database management system that stores and manages our website's data in a relational database

PHP: The server-side scripting language used to build dynamic web content.

**Step 0: Launch an EC2 instance**
* On the AWS Console, Create an Ubuntu EC2 instance
- Name: Instance name. e.g Allens-LEMP-Server
* AMI: Select Ubuntu 24.04 LTS HVM (64-bit architecture).
+ Instance type: Select the t2.micro machine type (eligible for free tier). ![instance selection](https://github.com/user-attachments/assets/bf795167-e62a-451b-9830-1d2153cd071e)


+ Key Pair: Create a new key pair and download and save the .ppk file as we would be using Putty to SSH into our instance. ![instance keypair](https://github.com/user-attachments/assets/ee06569f-ccab-4abb-8718-572e80a478d1)

    
+ Launch the instance and ensure the status checks is passed indicating readiness of the instance. ![instance ready](https://github.com/user-attachments/assets/fade13c1-ecc8-49bb-a063-fc5f6d821aba)



+ Check the security tab section of the instance to verify the presence of the security group allowing incoming SSH traffic on port 22 to the newly launched instance. ![ssh rule present](https://github.com/user-attachments/assets/225fc294-9120-4a5c-8e26-fcf1adf9d9e9)

+ Establish an SSH connection to the instance using Putty using the public DNS name and the private key downloaded.

    ![putty front page](https://github.com/user-attachments/assets/ab61ed52-506b-4c44-b99c-33e480bfa265)

 
    ![auth credential](https://github.com/user-attachments/assets/ce7905e2-f9a4-495d-abca-1d5e845be08a)
+  Select Ubuntu when asked to login as:





    ![putty login as](https://github.com/user-attachments/assets/b1420cf3-1ab6-4467-be31-3f65496d042c)



   
+  We can now start typing commands:






    ![putty first page after login](https://github.com/user-attachments/assets/755c4002-f989-40a4-94d7-962515f3cfb4)



**Step 1. Install Apache2 on the instance**
   
   
   Apache2 is a widely-used open-source web server software that allows websites to be hosted on the internet. It serves web content (HTML, PHP, etc.) to users' browsers when they request a website. Apache 
   is highly configurable, reliable, and can run on various operating systems like Linux, Windows, and macOS.
- Install apache2 using the command:
       sudo apt update
       sudo apt install apache2 
       ![sudo apt install apache2](https://github.com/user-attachments/assets/644d22da-a040-454b-9f26-b79ce2c33f77)



* Verify apache2 is up and running using the command:
       sudo systemctl status apache2
       ![image](https://github.com/user-attachments/assets/5da4f99b-481e-44b7-b2d8-4650af88b121)


+ In order for communication with apache2 we have to visit the security group and create a new inbound rule for http on Port 80 to traffic from any IPV4 address 0.0.0.0/0
       ![image](https://github.com/user-attachments/assets/8599cf8a-6688-43a7-9b6c-60967967d8cd)

+ Using curl http://localhost:80 or curl http://127.0.0.1:80 on the terminal or http://<public ip address of the instance> http://18.170.52.8 on a browser to confirm our apache2 web server is functioning appropriately
  
  
  ![curl localhost to access apache](https://github.com/user-attachments/assets/45d7fe97-e121-4fd2-a37f-b66e6dbcbbd6)

  ![apache homepage](https://github.com/user-attachments/assets/4e1bede8-c123-46e8-8317-769f68f2dba4)









**Step 2: Install MySQL**


  We need to install a database management system to be able to store and manage relational data for our website. 
- To install MySQL we use the command: 
sudo apt install mysql-server
 ![sudo install my sql server](https://github.com/user-attachments/assets/afa8d926-12e5-4474-bdc5-c0b573f80b17)

* We then login to the MYSQL console by typing: sudo mysql

  
    ![sudo mysql output](https://github.com/user-attachments/assets/b63f4b73-9d28-4fb9-872d-b6c9e734d995)

 We are now connected to the MySQL server as the administrative database root user. We have to set as the root user to prevent unauthorized access into the database. We will do this by setting a password for 
 access control.
  
+ Set MYSQL root user password by entering the command :
 ALTERUSER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password.1';

    ![mysql root user password setting](https://github.com/user-attachments/assets/f19df68f-249d-402e-ba3a-86e620ea9bac)


- Exit the MySQL shell by typing: >exit
* Next we would start an interact an interactive script that would allow us to remove any insecure default settings and futher lockdown access to our database system using the command:
   sudo mysql_secure_installation ![sudo mysql secure installation](https://github.com/user-attachments/assets/a4652811-ee7b-481b-b676-7a3433e494d3)


+ We would be asked to validate password plugin. We will have to choose a level for password validation with different categories. 0 = Low, 1 = Medium and 2 =Strong. Recall that the password earlier created 
  for the root user was "Password.1" and this matches the level 1. We would stick with it and choose 1. 
  
     ![password validation](https://github.com/user-attachments/assets/ed6ad013-5993-45f2-8bca-6bd2755944d7)

  We would be asked to choose yes or no for some other questions. We can select yes to all.

     ![yes to all](https://github.com/user-attachments/assets/a113bdbe-d3c4-408c-a49d-1ad150c33036)



+ Next we would try to enter the MySQL console using the root user password we created earlier:
      sudo mysql -p

    ![enter mysql console using password created](https://github.com/user-attachments/assets/bc5f95bc-ea11-4810-b78b-9b587947b230)

  No error means we have successfully accessed the console using the password. We then exit the console by entering the exit command.















**Step 3: Install PHP**



  PHP (Hypertext Preprocessor) is a popular open-source scripting language used primarily for web development, enabling the creation of dynamic and interactive websites. It runs on the server side and is 
  embedded in HTML to handle tasks like form data processing, database interactions, and content management.
  You have to install PHP and two php packages: 1. **php-Mysql** module, to help PHP communicate with MySql based databases and also **libapache2-mod-php** to enable apache2 handle PHP files. Core PHP 
  packages are installed automatically upon installlation of PHP.
  - Install all three using the command:
    

    sudo apt install php libapache2-mod-php php-mysql


    ![image](https://github.com/user-attachments/assets/edd46283-c257-43da-9c5d-8d92357f31bc)




  - Verify PHP running using the command

    php -v



   **Step 4: Create a Virtual Host**

To test the PHP script to ensure PHP is running properly alongside Apache, we have create an Apache virtual host that will contain the website's files and folders. Virtual host means running more than one 
 website on a single machine. This process is done using different IP address or name of IP addresses.

 
Apache has a default server block from which it serves documents from the /var/www/html directory.
We would set up a domain called Project Lamp and this would be located in a new directory /var/www/projectlamp/ which would host our project's files:


- Create a new directory using the command:
    
       sudo mkdir /var/www/projectlamp/
  

+ Next, we would assign ownership of the directory to the current user using the command:
       sudo chown -R $USER:$USER /var/www/projectlamp


+ We then create a configuration file for our project named projectlamp.conf. This will contain the barebone configuration for the virtual host created and will be stored in the Apache site-available 
 directory /etc/apache2/sites-available/.
We would use a command line editor like Nano to create the projectlamp.conf: sudo nano /etc/apache2/sites-available/projectlamp.conf

        <VirtualHost *:80> 
        ServerName projectlamp 
        ServerAlias www.projectlamp 
        ServerAdmin webmaster@localhost 
        DocumentRoot /var/www/projectlamp 
        ErrorLog ${APACHE_LOG_DIR}/error.log 
        CustomLog ${APACHE_LOG_DIR}/access.log combined 
        </VirtualHost> 


   ![creating projectlampconf on nano](https://github.com/user-attachments/assets/0e9f127e-2592-45c5-bda9-5d28083a6b8a)


 + We then enable the new virtual host using the command :


        sudo a2ensite projectlamp

   
 + Apache has a default website running. This website will interfere with our virtual host if not disabled. We would be getting the same old default page.




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



