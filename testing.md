
# Attack Anatomy

After Installing ELK & fluentd
  We should generate some log in the following files : <br>
     - /var/log/httpd/access_log<br>
	   - /var/log/httpd/error_log<br>
	   - /var/log/httpd/modsec_audit.log

By Testing web application attack using the following link:<br>
#### Begin attack Simulation
SQL injection<br>
http://127.0.0.1/Vulnerable-Web-Application-master/SQL/sql1.php?category=Gifts%27+OR+1=1--

XSS<br>
http://127.0.0.1/Vulnerable-Web-Application-master/XSS/XSS_level1.php
<script>alert(123)</script>

* Let move to elacticSearch 
  #### go to  Stack Management > Index Management 
   
   you should fine the following Index
   <img src="images/elk_index.JPG">
   
   #### Creat Index pattern  Stack Management > Index patterns
   <img src="images/create_index1.JPG">
   
   Name : apache_error_log_result*
   Timestamp field : @timestamp
    <img src="images/create_index2.JPG">
    
  * Now go to Discover, you should have date visualisation like this
  <img src="images/elk_data.JPG">


[6c1d8c4afbc7f85f05fb2e4d17e5553255b0195a0b56ba5309e362e2156debfc]



 
