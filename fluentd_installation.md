# Fluentd Installation Steps


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
  
```sh
    cd /etc/td-agent/
    wget https://raw.githubusercontent.com/secfit/soclab/main/repo/td-agent.conf
    service td-agent restart
```





# Fluentd ModSecurity plugin

## Preparation
#### define fluentd ruby plugins location

```sh
  ls -d /opt/td-agent/embedded/lib/ruby/gems/2.4.0/gems/fluentd-*/lib/fluent/plugin/
/opt/td-agent/embedded/lib/ruby/gems/2.4.0/gems/fluentd-1.11.5/lib/fluent/plugin/
``` 

* copy this file "out_modsecurity-audit-format.rb" to last fluentd-ruby-plugins-location
```sh
  cd /opt/td-agent/embedded/lib/ruby/gems/2.4.0/gems/fluentd-1.11.5/lib/fluent/plugin/
  wget https://raw.githubusercontent.com/secfit/fluentd/main/Plugins/modsecurity/out_modsecurity-audit-format.rb
```

* set permission
```sh
  chmod 664 /opt/td-agent/embedded/lib/ruby/gems/2.4.0/gems/fluentd-1.11.5/lib/fluent/plugin/out_modsecurity-audit-format.rb
```

* copy plugin file fluent-plugin-modsecurity.gemspec to /etc/td-agent/
```sh
  cd /etc/td-agent/
  wget https://raw.githubusercontent.com/secfit/fluentd/main/Plugins/modsecurity/fluent-plugin-modsecurity.gemspec
```

* set s.files value in /etc/td-agent/fluent-plugin-modsecurity.gemspec
```sh
  sed -i "s;__sfile__;/opt/td-agent/embedded/lib/ruby/gems/2.4.0/gems/fluentd-1.11.5/lib/fluent/plugin/out_modsecurity-audit-format.rb;g" /etc/td-agent/fluent-plugin-modsecurity.gemspec
```

* build the gem
```sh
  /opt/td-agent/embedded/bin/gem build /etc/td-agent/fluent-plugin-modsecurity.gemspec
```
You should get success message:

*WARNING:  description and summary are identical<br>
WARNING:  See http://guides.rubygems.org/specification-reference/ for help<br>
  Successfully built RubyGem<br>
  Name: fluent-plugin-modsecurity<br>
  Version: 0.2<br>
  File: fluent-plugin-modsecurity-0.2.gem<br>*


* Integrate into td-agent.conf
```sh
## Source
  	  #---- Mod_Security log ----#
	  <source>
	  @type tail
	  tag raw-modsec
	  path /var/log/httpd/modsec_audit.log
	  pos_file /opt/fluent/mod_security.log.pos
	  <parse>
		@type multiline
		format_firstline /^--[a-fA-F0-9]{8}-A--$/
			format1 /(?<message>.*)/
	  </parse>
	</source>
	
## injection
	<match raw-modsec>
	  @type modsecurity-audit-format
	  tag modsec.formatted
	</match>
	
## Output
	#---- Mod_Security_log ----#
	<match modsec.formatted.*>
  	@type copy
        <store>
        @type elasticsearch
        host 192.168.1.225
        port 9200
                include_tag_key true
        logstash_format true
        logstash_prefix apache_modsec_log_result
        logstash_dateformat %Y%m%d
        flush_interval 1s
		</store>
		<store>
	 	 @type file
	 	 path /var/log/td-agent/modsec
		</store>
	</match>

	
```

* restart fluentd service
```sh
  service td-agent restart
```
