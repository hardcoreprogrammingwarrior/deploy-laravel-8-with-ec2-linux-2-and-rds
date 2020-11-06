# How to Speedrun Deploy  Laravel 8 with ec2 Linux 2 and RDS within 14mins

sudo yum update -y  
sudo amazon-linux-extras enable php7.4 -y  
sudo yum clean metadata  
sudo yum install  -y php php-{pear,cgi,common,curl,mbstring,gd,mysqlnd,gettext,bcmath,json,xml,fpm,intl,zip,imap}  
sudo yum install  -y git  
curl -sS https://getcomposer.org/installer | php  
sudo  mv composer.phar /usr/bin/composer  
chmod +x /usr/bin/composer  

php --version  

// Dependencies installation will take some time. After than set proper permissions on files.
sudo chown -R ec2-user:apache /var/www  
sudo chmod 2775 /var/www && find /var/www -type d -exec sudo chmod 2775 {} \;  
find /var/www -type f -exec sudo chmod 0664 {} \;  

cd /var/www/html  
composer create-project --prefer-dist laravel/laravel speedrun "8.*"  
cd /var/www/html/speedrun  
composer install  


cp .env.example .env  
php artisan key:generate  

// Set the host:
sudo vim /etc/httpd/conf/httpd.conf   
// Add the File bottom
<VirtualHost *:80>  
       ServerName laravel.example.com  
       DocumentRoot /var/www/html/speedrun/public  
<Directory /var/www/html/speedrun>  
              AllowOverride All  
       </Directory>  
</VirtualHost>  

sudo systemctl start httpd  
sudo systemctl start php-fpm  
sudo systemctl enable php-fpm  
