## This runbook creates a webtier and a database and testing the read replica for the database. You can upscale it by creating a golden ami at the end and putting an ASG and ELB infront of the instances.

Create an ubuntu instance
SSH into the instance

Run the following commands
sudo apt-get -y install apache2   #This installs apache2
sudo locale-gen en_US.UTF-8  #This is installs the unicode transformation
export LANG=en_US.UTF-8   #This command updates use of Unicoding 
sudo apt-get update   #Update the system
sudo apt-get install -y software-properties-common python-software-properties   #This command installs software properties
sudo LC_ALL=en_US.UTF-8 add-apt-repository -y ppa:ondrej/php
sudo apt-get update   #update the system
sudo apt-get -y install php7.0 php7.0-curl php7.0-bcmath php7.0-intl php7.0-gd php7.0-dom php7.0-mcrypt php7.0-iconv php7.0-xsl php7.0-mbstring php7.0-ctype php7.0-zip php7.0-pdo php7.0-xml php7.0-bz2 php7.0-calendar php7.0-exif php7.0-fileinfo php7.0-json php7.0-mysqli php7.0-mysql php7.0-posix php7.0-tokenizer php7.0-xmlwriter php7.0-xmlreader php7.0-phar php7.0-soap php7.0-mysql php7.0-fpm libapache2-mod-php7.0  #This command installs the different php utilities grouped in one command 

sudo sed -i -e"s/^memory_limit\s*=\s*128M/memory_limit = 512M/" /etc/php/7.0/apache2/php.ini     #Update a string in php config file
sudo apt-get install wget -y   #Install wget to download web based objects

sudo a2enmod rewrite   #setting up modules to help the webserver do its job with regards to managing reqeusts
sudo a2enmod headers   #Setting up another module (header) to help the webserver. "There are many other modules in apache"

sudo service apache2 restart  #Restarting the service. Has to be done after changing a module
sudo service apache2 enable

sudo apt-get -y install mysql-client   #Installing mysql in the server. It is needed to connect to the database. 

curl -O https://wordpress.org/latest.tar.gz   #Downloading the wordpress application stack
sudo tar xzf latest.tar.gz -C /var/www      #untar/unzip the tar file which had been downloaded and sending it to /var/www. 
sudo rm -f latest.tar.gz      #remove the downloaded tar.gz file 

sudo rm /etc/apache2/sites-enabled/000-default.conf     #This command removes the native config file for logging. We have to pass our own config file info which can be found in my github "https://raw.githubusercontent.com/ambedarrell/webapp-database-project/main/appserver-apache2-config/000-default.conf"

sudo vi /etc/apache2/sites-enabled/000-default.conf    # paste the config file information from the repo above

sudo service apache2 restart   # Restart apache2 to apply changes

cd /var/www/wordpress
ls 
#####  sudo vi wp-config-sample.php # This is the file that has the configuration between wordpress and database

----

Create a private subnet in your vpc in which your database would reside. 
Go to your aws management console, and then to RDS. Create a database
Go to security groups and update the security group of the database just created and update it to listen on the security group of your application

sudo rm /var/wwww/wordpress/wp-config-sample.php     # This will remove a file containing configuration that we do not need and replace with information in the repo "https://raw.githubusercontent.com/ambedarrell/webapp-database-project/main/appserver-database-config/wp-config.php"

sudo vi /var/www/wordpress/wp-config-sample.php   # Fill in the information with the info found in the repo above. We also have to update the file by providing the database name, username, password and hostname(endpoint)

sudo chown -R www-data:www-data /var/www/wordpress   # This command will change the ownership of the config file

sudo systemctl restart apache2    # restart the apache service

At this level, the application is all set up and ready. 

### To remote into your database from your instance, use this command: mysql -u USERNAME -h HOSTNAMEORIP -p
### Be sure to pdate the username and hostname(endpoint)
### When logged in, you can run the command: <show databases;> to view your databases in your database instance
### You can create a database by running the command <create database name-you-want>