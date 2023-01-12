<br />
<div align="center">
  <h3 align="center">Install  OWASP / Vulnerable-Web-Application </h3>
</div>


### MYSQL installation
  ```sh
rpm -ivh https://repo.mysql.com/yum/mysql-connectors-community/el/7/x86_64/mysql-community-release-el7-5.noarch.rpm
cd /etc/yum.repos.d/
wget https://github.com/secfit/soclab/blob/main/repo/mysql-community-source.repo
wget https://github.com/secfit/soclab/blob/main/repo/mysql-community.repo
yum -y install mysql-community-server
service mysqld start
  ```

### PHP installation
  ```sh
yum install php-common php-mysql php-cli
sed -i "s;allow_url_fopen = Off;allow_url_fopen = On;g" /etc/php.ini
sed -i "s;allow_url_include = Off;allow_url_include = On;g" /etc/php.ini
  ```
  
### Apache installlation
  ```sh
yum install httpd
service httpd start
  ```

### Install Vulnerable-Web-Application from OWASP fundation
Vulnerable-Web-Application categorically includes Command Execution, File Inclusion, File Upload, SQL and XSS. For database-requiring categories, it creates a database under localhost with one button during setup. In case of corrupted or changed databases, you can create a database again.
  ```sh
cd /var/www/html/
wget https://github.com/OWASP/Vulnerable-Web-Application/archive/refs/heads/master.zip
unzip master.zip
  ```

### Open Application
http://127.0.0.1/Vulnerable-Web-Application-master/

<img src="images/owasp_vwa.JPG">

Create Database.
