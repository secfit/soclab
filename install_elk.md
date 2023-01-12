<br />
<div align="center">
  <h3 align="center">ELK installation</h3>
  <p align="left">
    elasticsearch 7.14.1<br>
    kibana 7.14.2<br>
    packetbeat 7.14.0<br>
</div>

## Elasticsearch
### import repository
Add the following to /etc/yum.repos.d/elasticsearch.repo
  ```sh
    [Elasticsearch-7]
    name=Elasticsearch repository for 7.x packages
    baseurl=https://artifacts.elastic.co/packages/7.x/yum
    gpgcheck=1
    gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
    enabled=1
    autorefresh=1
    type=rpm-md
  ```
### Install Java-11
Check the latest release of (OpenJDK 11)[https://jdk.java.net/11/] before running the commands below: 
  ```sh
  	curl -O https://download.java.net/java/GA/jdk11/9/GPL/openjdk-11.0.2_linux-x64_bin.tar.gz
  ```
 After the download, extract the archive
   ```sh
  	tar -xvf openjdk-11.0.2_linux-x64_bin.tar.gz
  ```
 Move the resulting folder to /usr/local/
   ```sh
  	mv jdk-11.0.2  /usr/local/
  ```
  Set environment variables<br>Add the following to /etc/profile.d/jdk11.sh
   ```sh
  	export JAVA_HOME=/usr/local/jdk-11.0.2
    export PATH=$PATH:$JAVA_HOME/bin
  ```
 
  Source your profile file and check java command
   ```sh
    $ source /etc/profile.d/jdk11.sh
    $ java -version
    openjdk version "11.0.2" 2019-01-15
    OpenJDK Runtime Environment 18.9 (build 11.0.2+9)
    OpenJDK 64-Bit Server VM 18.9 (build 11.0.2+9, mixed mode)
    $ which java
    /usr/bin/java
  ```
  
  Manage Java version
   ```sh
  	update-alternatives --install /usr/bin/java java /usr/local/jdk-11.0.2/bin/java 2
    /usr/sbin/alternatives --config java
  ```
   <img src="https://github.com/secfit/elk/raw/main/images/java_version.JPG">
  
  ### Installation
   ```sh
    yum install elasticsearch
  ```
 change permission
   ```sh
    chown elasticsearch:elasticsearch /var/log/elasticsearch
    chown elasticsearch:elasticsearch /var/lib/elasticsearch
  ```
  Add the following values to /etc/security/limits.conf
   ```sh
    elasticsearch - nproc 4096
    root - nproc 4096
  ```
  Change the following values on /etc/elasticsearch/jvm.options
   ```sh
    sed -i "s;Xms1g;Xms15g;g" /etc/elasticsearch/jvm.options
    sed -i "s;Xmx1g;Xmx15g;g" /etc/elasticsearch/jvm.options
  ```
  Ensure the values on config file /etc/elasticsearch/elasticsearch.yml
   ```sh
    path.data: /var/lib/elasticsearch
    path.logs: /var/log/elasticsearch
    bootstrap.system_call_filter: false
    network.host: 127.0.0.1
    http.port: 9200
    cluster.name: elastic_cluster_localhost
    node.name: localhost_cluster
    cluster.initial_master_nodes: ["127.0.0.1"]
  ```
   Start the service
   ```sh
    service elasticsearch start
  ```
  
## Packetbeat
#### Installation
   ```sh
    yum install libpcap
    wget https://artifacts.elastic.co/downloads/beats/packetbeat/packetbeat-7.14.2-x86_64.rpm
    rpm -ivh packetbeat-7.14.2-x86_64.rpm
  ```
Change host/port value on /etc/packetbeat/packetbeat.yml in [output.elasticsearch] section
   ```sh
    output.elasticsearch:
    hosts: ["127.0.0.1:9200"]
  ```
change elasticsearch output parameter on /etc/packetbeat/packetbeat.yml
   ```sh
    output.elasticsearch.index: "packetbeat_admin-system_-%{[agent.version]}-%{+yyyy.MM.dd}"
    setup.template.name: "packetbeat_localhost-system_"
    setup.template.pattern: "packetbeat_localhost-system_-*"
  ```
service initialisation
   ```sh
    service packetbeat start
  ```
Check packetbeat Indice
   ```sh
    curl -XGET "http://localhost:9200/_cat/indices"
  ```
 ## Kibana
 Installation
   ```sh
    yum install kibana
  ```
  
 Change host/port & output values on /etc/packetbeat/kibana.yml
   ```sh
    server.port: 5694
    server.host: "127.0.0.1"
    elasticsearch.hosts: ["http://localhost:9200"]
  ```
  service initialisation
   ```sh
    service kibana start
  ```
