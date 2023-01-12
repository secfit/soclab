<br />
<div align="center">
  <h3 align="center">Mod_Security installation</h3>
</div>


Install Epel Release
  ```sh
  rpm -ivh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
  yum install mod_security
  ```
  
Download mod_security config file
```sh
  cd /etc/httpd/conf.d/
  wget https://raw.githubusercontent.com/secfit/soclab/main/repo/mod_security.conf
  service httpd restart
``` 
  
  
  
