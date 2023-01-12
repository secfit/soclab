<div align="center">
  <h3 align="center">Fluentd Installation Steps</h3>
</div>

* import repository<br>
    Add the following to /etc/yum.repos.d/td.repo
```sh
          [treasuredata]
	      name=TreasureData
	      baseurl=http://packages.treasuredata.com/3/redhat/\$releasever/\$basearch
	      gpgcheck=1
	      gpgkey=https://packages.treasuredata.com/GPG-KEY-td-agent
```
  * Checking for update & installing td-agent using YUM command<br>
      ```sh
	      yum install -y td-agent
	      chkconfig td-agent on
      ```
  * systemd running permission<br>
  change values on this file : /usr/lib/systemd/system/td-agent.service <br>
  change 
      ```sh
        change 
              User=td-agent
	          Group=td-agent
        by
              User=root
              Group=root
      ```
   
  * Reload systemd file
      ```sh
        systemctl daemon-reload
      ``` 
  * Set dir permission
      ```sh
        chown -R root:td-agent /var/log/td-agent/
        chmod -R 775 /var/log/td-agent/
        mkdir /opt/fluent/
      ``` 
  * Download Config File & start service

  cd /etc/td-agent/
  wget https://raw.githubusercontent.com/secfit/soclab/main/repo/mod_security.conf
  service td-agent restart
