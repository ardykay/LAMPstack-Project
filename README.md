  #     LAMPStack-Project
##  LAMP STACK PROJECT(Linux, Apache, Mysql,Php)  Using AWS
For this project-based learning task,  I deployed a LAMP stack on an AWS EC2 instance. The LAMP stack consists of Linux, Apache, MySQL, and PHP. I used an Ubuntu 22 server for Linux, Apache for the web server, MySQL for the database, and PHP for the server-side scripting language. I also configured the Apache web server with virtual hosts.

## Learning Outcomes

- [x] I learned to set up my AWS console for managing cloud resources
- [x] I learned to create an EC2 instance on AWS and connect to it using SSH
- [x] I learned about Web Server Technology Stacks
- [x] I learned How to install Apache, MySQL, and PHP on an EC2 instance
- [x] I learned to configure the Apache web server with virtual hosts

Prerequisites
AWS Account: 
If you don't have one, sign up for an AWS account.
AWS EC2: This tutorial assumes you're using an Amazon EC2 instance for hosting.
Basic knowledge: Understanding of SSH, Linux commands, and web development basics.

### Step 1: Launch an EC2 Instance
1. **Sign in to AWS Management Console** and type **EC2** on the search bar
2. Click on **Launch Instance**.
3. **Configure instance**:
   - **Name**: Give your instance a name (e.g., "LAMPStack").
   - **AMI**: Select **Ubuntu 24.04 LTS HVM** (64-bit architecture).
   - **Instance type**: Choose **t3.micro** (eligible for free tier).
   
    ![1EC2](https://github.com/user-attachments/assets/0a778765-20c4-4ec1-8f84-2cc4b0c16887)

4. **Key pair**: If you have one, choose it. Otherwise, create a new key pair and download the `.pem` file, you will need it if you want to connect to your server from a remote environment.

   ![click create](https://github.com/user-attachments/assets/ead0cfbb-6923-45e1-b89c-c73589e35ca4)
   
  ![Key PAir](https://github.com/user-attachments/assets/0544f2d6-2217-4d81-af39-97429f112831)

5. **Edit Networking setting:**
    -**choose default VPC**
    -**Enable auto Asign public IP**
![edit networking](https://github.com/user-attachments/assets/c80e075e-e94f-4d10-bde1-5c97301147df)

   **Security group**: Create a new security group or use an existing one. Ensure that the following ports are allowed:
   - **SSH (port 22)** – for remote access
   - **HTTP (port 80)** – for web traffic
   - **HTTPS (port 443)** – for secure web traffic 
6. **Launch** the instance.

![NEW SG](https://github.com/user-attachments/assets/c9b25251-3fc3-4781-aba8-dc27cbd214c0)

  **Go to EC2 Dashboard:**
    -**click instances**
![EC2 SUCESS](https://github.com/user-attachments/assets/029831e5-2127-4944-9f6a-57486931d1e5)

### Step 2: Connect to the EC2 Instance via EC2 Connect
  -**In the EC2 dashboard pick your instance  click connect**.
  ![Connect EC2](https://github.com/user-attachments/assets/484f19a7-85cb-4683-a2c1-920dc17fbda8)
  
  -**In the Connect to instance page click connect
![EC2 CONNECT](https://github.com/user-attachments/assets/5b9aff46-8321-495d-aa0e-abe4cee5620e)

The EC2 instance environment 
![EC2 environment](https://github.com/user-attachments/assets/38786cb2-3214-4fc8-92ac-0e9e98ab13a4)

### Step 3: Update the Server
Run the following commands to update your server:
```bash
sudo apt update
sudo apt upgrade -y
```

### Step 4: Install Apache
1. Install **Apache**:
   ```bash
   sudo apt install apache2 -y
   ```
2. Verify Apache is running:
   ```bash
   sudo systemctl status apache2
   ```
![ubuntu status](https://github.com/user-attachments/assets/acddf7fa-1d1c-4fab-9a6a-cda3892ea12c)

3. Open your browser paste your public IP <http://Public-IP>, You should see the Apache default page.
  ![Ubuntu Test Page](https://github.com/user-attachments/assets/a7c16fad-d0b8-4691-aea5-cd1477a19770)

### Step 5: Install MySQL
1. Install **MySQL**:
   ```bash
   sudo apt install mysql-server -y
   ```
2. Login to Mysql
   ```bash
   sudo mysql
   ```
    ![SQL](https://github.com/user-attachments/assets/67091459-467b-4a9d-9c9d-66ca4fcb9dad)


   ```bash
   ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';
   ```
  ![SQL2](https://github.com/user-attachments/assets/5419888c-d60d-4abc-bac1-43bf63873635)


3. Secure the installation: Run the security script to set up a root password and remove unnecessary defaults:
    -Follow the prompts to secure your database (you'll be asked to set a root password, remove anonymous users, disallow root login remotely, and more).

   '''bash
   sudo mysql_secure_installation
   '''
   

4. Log in to MySQL to ensure it works:
   -Enter your root password to access the MySQL prompt.
   ```bash
   sudo mysql -p
   ```

### Step 6: Install PHP
1. Install **PHP** and necessary PHP extensions for Apache and MySQL:
   ```bash
   sudo apt install php libapache2-mod-php php-mysql php-cli -y
   ```

2. Check PHP Version
   ```bash
   php -v
   ```
![php succ](https://github.com/user-attachments/assets/3a1a4ec6-816b-4a0b-b2e0-215edc611ead)


3. Restart Apache to load PHP:
   ```bash
   sudo systemctl restart apache2
   ```
##At this point the LAMP Stack is completely install and fully operational.
 To test your setup with a php script it is better to set up a proper Apache Virtual Host to hold your website file and folder.

### Step 7: Set Up Virtual Hosts 
Document root: Apache serves files from /var/www/html by default. Place your PHP project files in this directory. We will leave this document as it is and add our own project directory next to the default one.

1. Create a new directory for your website in this location /var/www/:
   ```bash
   sudo mkdir /var/www/lampStack-project
   ```
  Next assign the ownership of the directory with the $USER environment variable which will refrence your current system user
   '''bash
      sudo chown -R $USER:$USER /var/www/lampStack-project
   '''

2. Create a new virtual host configuration file in Apache :
   ```bash
   sudo vi /etc/apache2/sites-available/lampStack-project.conf
   ```
3. Add the following content to configure the virtual host:
   ```apache
   <VirtualHost *:80>
       ServerName lampStack-project
       ServerAlias www.lampStack-project
       ServerAdmin webmaster@localhost
       DocumentRoot /var/www/lampStack-project
       ErrorLog ${APACHE_LOG_DIR}/error.log
       CustomLog ${APACHE_LOG_DIR}/access.log combined
   </VirtualHost>
   ```

4. Enable the virtual host:
   ```bash
   sudo ls /etc/apache2/sites-available 
   sudo a2ensite lampStack-project      #To enable new virtual host
   sudo a2dissite 000-default     #To disable Apaches's default website
   sudo apache2ctl configtest     #To test your config file doesn' contain syntax errors
   ```
    ![config new](https://github.com/user-attachments/assets/4d18208a-dd06-488e-8ca9-bd94b9cf000f)

   ```bash
  sudo systemctl reload apache2
   ```
  ![Index html Test page](https://github.com/user-attachments/assets/82529e67-ee93-419a-acba-0f7c8f3e1766)

  ##The Website is mow active, but the web root is empty.

  Verify PHP is working: Create a test phpinfo() file to ensure PHP is installed correctly:
  sudo vi /var/www/html/info.php
  Add the following content:
  ```php
   <?php
   phpinfo();
   ?>
   ```
  ![php test page](https://github.com/user-attachments/assets/0c824639-4185-49a3-9484-c302ff76c3aa)



### Step 8: Test with HTML scripts
1. Navigate to the project path
   ```bash
   cd /var/www/lampStack-project
   ```
2. Create an index.html file
   ```bash
   vi index.html
   ```
  then paste the html contents below. 

'''html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My LAMP Stack Project</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
            background-color: #f4f4f4;
            color: #333;
            margin-top: 50px;
        }
        h1 {
            color: #0056b3;
        }
        p {
            font-size: 1.2rem;
        }
    </style>
</head>
<body>
    <h1>KAYODE ADEYEMI</h1>
    <h2>My First DevOps and Cloud Project</h2>
    <p>Implementing a LAMP stack on an AWS EC2 instance</p>
</body>
</html>
'''
The page looks like After serving my content from the root
![Final page](https://github.com/user-attachments/assets/07af3ebd-5cd7-4b17-b2b6-d74027cff664)

