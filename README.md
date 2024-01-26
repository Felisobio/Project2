# Project2
## WEB STACK IMPLEMENTATION (LEMP STACK)
## Requirements
-Cloud Service Provider: AWS, Azure, GCP, etc.

-Provision a Linux Instance (Preferably Ubuntu).

-Basic understanding of SSH access to a virtual host.

## Step 1  INSTALLING THE NGINX WEB SERVER
### What is NGINX
NGINX, pronounced "engine-x,"  is a high-performance open-source web server, reverse proxy, load balancer, and HTTP cache known for its speed, efficiency, and versatility in serving web content and managing web traffic.

### Below are steps to install NGINX WEB SERVER

1. Update Package Index:
   
   `sudo apt update`

  <img width="698" alt="p2 sudo update" src="https://github.com/Felisobio/Project2/assets/149904523/5f8b1749-84ef-4b4e-91f7-82e1ef9e4922">

3. Install NGINX:
   
   `sudo apt install nginx`
   
5. Start NGINX Service:

     `sudo systemctl start nginx`

7. Enable NGINX to Start on Boot:

   `sudo systemctl enable nginx`
8. Check NGINX Status:

   `sudo systemctl status nginx`

   <img width="646" alt="P2 confirm nginx is installed" src="https://github.com/Felisobio/Project2/assets/149904523/a051e4c8-c3be-4492-b2ca-e3bdb5e06662">

10. Adjust Firewall (if applicable):If a firewall is enabled, allow HTTP and HTTPS traffic:

   `sudo ufw allow 'Nginx Full'`
11. Verify NGINX Installation:
   
   Open a web browser and enter your server's IP address. If NGINX is installed correctly, you should see the default NGINX welcome page.using this `http://Public-IP-Address:80`
11. Configuration Files:

   NGINX's main configuration file: `/etc/nginx/nginx.conf`
   
   Default site configuration: `/etc/nginx/sites-available/default`
   
## Step 2 — INSTALLING MYSQL
Now that your web server is operational, you'll need to install a Database Management System (DBMS) to store and manage data for your site in a relational database. MySQL, a popular choice within PHP environments, will be used for our project. Once again, utilize 'apt' to obtain and install this software.

1. Install MySQL Server:

   `sudo apt install mysql-server`

   <img width="722" alt="p2 instal sql" src="https://github.com/Felisobio/Project2/assets/149904523/98e1be6b-baee-4386-a70e-6094d1b6c64f">

3. Run MySQL Security Script: After installation, MySQL includes a security script to improve the default security settings. This script allows you to configure settings        such as setting the root password, removing anonymous users, disallowing root login remotely, removing test databases, and reloading privilege tables. Run it with:

   `sudo mysql_secure_installation`

<img width="536" alt="p2 mysql secure install" src="https://github.com/Felisobio/Project2/assets/149904523/f0f2a833-d656-4673-86c3-59c8bfaa5d96">

It’s recommended that you run a security script that comes pre-installed with MySQL. This script will remove some insecure default settings and lock down access to your database system. Before running the script, you will set a password for the root user, using mysql_native_password as default authentication method. We’re defining this user’s password as PassWord.1.

`ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';`

4.  Check MySQL Service Status: This command verifies whether MySQL is running or not.

    `sudo systemctl status mysql`
5. Start/Stop/Restart MySQL Service:

   `sudo systemctl start mysql
    sudo systemctl stop mysql
    sudo systemctl restart mysql`
6. Enable MySQL Service to Start on Boot:

   `sudo systemctl enable mysql`
7. Verify MySQL Installation: Log in to the MySQL server as the root user: Enter the root password you set during the installation or press Enter if you haven't set one yet.

   `sudo mysql -u root -p`

   <img width="527" alt="p2 mysql P" src="https://github.com/Felisobio/Project2/assets/149904523/bd4658f6-193e-4263-a002-e1b62600ba9d">

9. Access MySQL Prompt: To access the MySQL prompt in the future: Enter your password to log in.

  `mysql -u root -p`
  ## You have successfully installed MySQL on your Ubuntu system.
  
## Step 3 – INSTALLING PHP
With Nginx serving content and MySQL managing data, the next step is installing PHP for code execution and dynamic content generation. Unlike Apache, Nginx requires an external program like php-fpm for PHP processing. Additionally, install php-mysql for PHP communication with MySQL databases. Core PHP packages install automatically as dependencies.

To install these 2 packages at once, run:

    `sudo apt install php-fpm php-mysql` 

    <img width="695" alt="image" src="https://github.com/Felisobio/Project2/assets/149904523/05a2d76b-6d89-4ba1-8acf-147b377b5e7f">

Once prompted, type 'Y' and press ENTER to confirm the installation process.
Your PHP components are now installed. Next, configure Nginx to utilize them.

## STEP 4 — CONFIGURING NGINX TO USE PHP PROCESSOR
When using Nginx, we create server blocks (similar to Apache's virtual hosts) to host multiple domains on one server. By default, Ubuntu 20.04's Nginx serves files from /var/www/html. To manage multiple sites, create a directory structure within /var/www for your domain website, keeping /var/www/html as the default directory. 

1. Create the root web directory for your domain with the following steps:

   `sudo mkdir /var/www/projectLEMP`
2. Assign ownership of the directory with the $USER environment variable, which will reference your current system user:

   `sudo chown -R $USER:$USER /var/www/projectLEMP`
3. Then, open a new configuration file in Nginx’s sites-available directory using your preferred command-line editor. Here, we’ll use nano:

  `sudo nano /etc/nginx/sites-available/projectLEMP`
  
This will create a new blank file. Paste in the following bare-bones configuration:

<img width="696" alt="image" src="https://github.com/Felisobio/Project2/assets/149904523/ae17ef92-4be8-4769-9f71-c428a416cd2d">

`#/etc/nginx/sites-available/projectLEMP
 
server {
	listen 80;
	server_name projectLEMP www.projectLEMP;
	root /var/www/projectLEMP;
 
	index index.html index.htm index.php;
 
	location / {
    	try_files $uri $uri/ =404;
	}
 
	location ~ \.php$ {
    	include snippets/fastcgi-php.conf;
    	fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
 	}
 
	location ~ /\.ht {
    	deny all;
	}
 
}`

### Here’s what each of these directives and location blocks do:
- listen — Defines what port Nginx will listen on. In this case, it will listen on port 80, the default port for HTTP.

- root — Defines the document root where the files served by this website are stored.

- index — Defines in which order Nginx will prioritize index files for this website. It is a common practice to list index.html files with a higher precedence than         
  index.php files to allow for quickly setting up a maintenance landing page in PHP applications. You can adjust these settings to better suit your application needs.

- server_name — Defines which domain names and/or IP addresses this server block should respond for. Point this directive to your server’s domain name or public IP address.

- location / — The first location block includes a try_files directive, which checks for the existence of files or directories matching a URI request. If Nginx cannot find 
  the appropriate resource, it will return a 404 error.

- location ~ \.php$ — This location block handles the actual PHP processing by pointing Nginx to the fastcgi-php.conf configuration file and the php7.4-fpm.sock file, which 
  declares what socket is associated with php-fpm.

- location ~ /\.ht — The last location block deals with .htaccess files, which Nginx does not process. By adding the deny all directive, if any .htaccess files happen to 
  find their way into the document root, they will not be served to visitors.

After completing your edits, save and close the file. If you're using nano, press CTRL+X, then y, and ENTER to confirm the changes.

Activate your configuration by creating a symbolic link to the config file in Nginx's `sites-enabled` directory:

 `sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/`

This will tell Nginx to use the configuration next time it is reloaded. You can test your configuration for syntax errors by typing:
 
  `sudo nginx -t`

You shall see following message:

`nginx: the configuration file /etc/nginx/nginx.conf syntax is ok`
 
 `nginx: configuration file /etc/nginx/nginx.conf test is successful`

We also need to disable default Nginx host that is currently configured to listen on port 80, for this run:

 `sudo unlink /etc/nginx/sites-enabled/default`
 
When you are ready, reload Nginx to apply the changes:
 
 `sudo systemctl reload nginx`

 <img width="683" alt="image" src="https://github.com/Felisobio/Project2/assets/149904523/abeb0ae1-6274-4e92-ae8e-e6d2418c630c">
 
Your new website is now active, but the web root /var/www/projectLEMP is still empty. Create an index.html file in that location so that we can test that your new server block works as expected:

`sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html`

Now go to your browser and try to open your website URL using IP address:

`http://<Public-IP-Address>:80`

<img width="958" alt="image" src="https://github.com/Felisobio/Project2/assets/149904523/8a17b4cc-f53d-48a4-985a-cafadaa2d339">


## STEP 5 – TESTING PHP WITH NGINX

Your LEMP stack setup is now complete and operational.

To verify that Nginx can properly handle .php files through your PHP processor, create a test PHP file in your document root. Open a new file named info.php in your document root using your text editor.

`sudo nano /var/www/projectLEMP/info.php`

Type or paste the following lines into the new file. This is valid PHP code that will return information about your server:

`<?php
phpinfo();`

You can now access this page in your web browser by visiting the domain name or public IP address you’ve set up in your Nginx configuration file, followed by /info.php:

`http://`server_domain_or_IP`/info.php`

You will see a web page containing detailed information about your server:

<img width="947" alt="image" src="https://github.com/Felisobio/Project2/assets/149904523/37f86a6e-e228-409e-a013-aec5eb5c5da7">

After checking the relevant information about your PHP server through that page, it’s best to remove the file you created as it contains sensitive information about your PHP environment and your Ubuntu server. You can use rm to remove that file:

`sudo rm /var/www/your_domain/info.php`

##STEP 6 – RETRIEVING DATA FROM MYSQL DATABASE WITH PHP (CONTINUED)
###Step 6 — Retrieving data from MySQL database with PHP
access for Nginx to query and display data.

As the native MySQL PHP library mysqlnd doesn't support caching_sha2_authentication, the default for MySQL 8, we'll create a new user with the mysql_native_password authentication method to connect from PHP.

We'll create a database named "example_database" and a user named "example_user," but you can use different names if preferred.

First, connect to the MySQL console using the root account:

`sudo mysql`

I encountered an error while executing this command because I had previously a password. I only needed to run the command below to input my password.

 `sudo mysql -uroot -p`
  
<img width="344" alt="image" src="https://github.com/Felisobio/Project2/assets/149904523/3ee406bd-d9d0-4afa-8ef5-f324212e2abb">

To create a new database, run the following command from your MySQL console:

`mysql> CREATE DATABASE `example_database`;`

<img width="391" alt="image" src="https://github.com/Felisobio/Project2/assets/149904523/55b4fa4e-235e-4f57-a106-02b73eaf2fa1">

Now you can create a new user and grant him full privileges on the database you have just created.

The following command creates a new user named example_user, using mysql_native_password as default authentication method. We’re defining this user’s password as password, but you should replace this value with a secure password of your own choosing.

`mysql> CREATE USER 'example_user'@'%' IDENTIFIED WITH mysql_native_password BY 'password';`

I had an error as you can see in the screenshot below, I ran another command as my password did not satisfy the current policy requirements

  `mysql> SET GLOBAL validate_password.policy=LOW;`

<img width="599" alt="image" src="https://github.com/Felisobio/Project2/assets/149904523/0fe3887a-668f-42a8-ba90-450ab393a9f3">

Now we need to give this user permission over the example_database database:

  `mysql> GRANT ALL ON example_database.* TO 'example_user'@'%';`
  
This will give the example_user user full privileges over the example_database database, while preventing this user from creating or modifying other databases on your server.

Now exit the MySQL shell with:

`mysql> exit`

You can test if the new user has the proper permissions by logging in to the MySQL console again, this time using the custom user credentials:

mysql -u example_user -p


<img width="461" alt="image" src="https://github.com/Felisobio/Project2/assets/149904523/35e4c015-3909-4742-b824-7bbc19730fa7">


Notice the -p flag in this command, which will prompt you for the password used when creating the example_user user. After logging in to the MySQL console, confirm that you have access to the example_database database:

  `mysql> SHOW DATABASES;`

This will give you the following output:

`Output
+--------------------+
| Database       	|
+--------------------+
| example_database   |
| information_schema |
+--------------------+`
2 rows in set (0.000 sec)

<img width="449" alt="image" src="https://github.com/Felisobio/Project2/assets/149904523/7fea99de-8bf2-40b0-af9b-d359c6e72f89">

Next, we’ll create a test table named todo_list. From the MySQL console, run the following statement:

`CREATE TABLE example_database.todo_list (
mysql> 	item_id INT AUTO_INCREMENT,
mysql> 	content VARCHAR(255),
mysql> 	PRIMARY KEY(item_id)
mysql> );`

<img width="785" alt="image" src="https://github.com/Felisobio/Project2/assets/149904523/86f7fdb0-be68-46bc-8143-e3d6ddf3f072">

Insert a few rows of content in the test table. You might want to repeat the next command a few times, using different VALUES:

`mysql> INSERT INTO example_database.todo_list (content) VALUES ("My first important item");`

To confirm that the data was successfully saved to your table, run:

`mysql> SELECT * FROM example_database.todo_list;`

You’ll see the following output:

Output
+---------+--------------------------+
| item_id | content              	|
+---------+--------------------------+
|   	1 | My first important item  |
|   	2 | My second important item |
|   	3 | My third important item  |
|   	4 | and this one more thing  |
+---------+--------------------------+
4 rows in set (0.000 sec)

<img width="638" alt="image" src="https://github.com/Felisobio/Project2/assets/149904523/d5c16c2c-56f7-45c1-afcc-922cf5cb1bea">

And then Exit


Now you can create a PHP script that will connect to MySQL and query for your content. Create a new PHP file in your custom web root directory using your preferred editor. We’ll use vi for that:

nano /var/www/projectLEMP/todo_list.php

The following PHP script connects to the MySQL database and queries for the content of the todo_list table, displays the results in a list. If there is a problem with the database connection, it will throw an exception.
Copy this content into your todo_list.php script:

<?php
$user = "example_user";
$password = "password";
$database = "example_database";
$table = "todo_list";
 
`{
  $db = new PDO("mysql:host=localhost;dbname=$database", $user, $password);
  echo "<h2>TODO</h2><ol>";
  foreach($db->query("SELECT content FROM $table") as $row) {
	echo "<li>" . $row['content'] . "</li>";
  }
  echo "</ol>";
} catch (PDOException $e) {
	print "Error!: " . $e->getMessage() . "<br/>";
	die();
}`


Save and close the file when you are done editing.

You can now access this page in your web browser by visiting the domain name or public IP address configured for your website, followed by /todo_list.php:

  `http://<Public_domain_or_IP>/todo_list.php`
  
You should see a page like this, showing the content you’ve inserted in your test table:


<img width="960" alt="image" src="https://github.com/Felisobio/Project2/assets/149904523/113761ca-59bd-4aba-a048-28deaf192414">

