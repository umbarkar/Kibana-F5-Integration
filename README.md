# Kibana-F5-Integration

#System : Ubuntu 16.10 Server
#Step 1: Install Java
#Add oracle Java PPA to APT:
sudo add-apt-repository -y ppa:webupd8team/java

#update your apt package database:
sudo apt-get update

#Install latest stable version of Oracle Java 8
sudo apt-get -y install oracle-java8-installer

#Step 2: Install Elasticsearch
#Import elasticsearch public GSPG key into apt:
wget -qO - https://packages.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -

#Create elastisearch source list:
echo "deb http://packages.elastic.co/elasticsearch/2.x/debian stable main" | sudo tee -a /etc/apt/sources.list.d/elasticsearch-2.x.list

#Update apt package database 
sudo apt-get update

#Install elasticsearch
sudo apt-get -y install elasticsearch

#Edit Config file of elasticseach
sudo nano /etc/elasticsearch/elasticsearch.yml

#uncomment the network host line and replace value with localhost to ensure third part host cant access the DB or manipulate the elasticsearch process
#Use the following section as is on elasticseach.yml file.


#############  Updated configuration   #############
       network.host: localhost
       cluster.name: F5-ELK
       node.name: server_1 
###############################################

#save and exit the elasticseach.yml & restart the service.
sudo service elasticsearch restart

#run following command to start elasticseach on boot up.
sudo update-rc.d elasticsearch defaults 95 10

#Step 3: Install Kibana
#create source list
echo "deb http://packages.elastic.co/kibana/4.4/debian stable main" | sudo tee -a /etc/apt/sources.list.d/kibana-4.4.x.list

#Update the apt package database
sudo apt-get update

#Install kibana
sudo apt-get -y install kibana

#enable service and move it to enable on boot up
sudo update-rc.d kibana defaults 96 9
sudo service kibana start

#Step4: Install Syslog-ng

#Install syslog-ng-core
sudo apt-get install syslog-ng-core

#edit config of syslog-ng
sudo nano /etc/syslog-ng/conf.d/bigip.conf

#enter the content of bigip.conf file here (uploaded on github)
#restart service 
sudo service syslog-ng restart

#Step 5: Install logstash
#Create logstash source list

echo 'deb http://packages.elastic.co/logstash/2.2/debian stable main' | sudo tee /etc/apt/sources.list.d/logstash-2.2.x.list

#update apt package database
sudo apt-get update

#Install logstash 
sudo apt-get install logstash

#Create config file
sudo nano /etc/logstash/conf.d/config.conf

#enter the content of config.conf file here (uploaded on github)
#save and quit

#Step 6: Upload templates
# Copy paste bigip_template, dns_template, DDoS_template, http_template one after other ~ you should see {"acknowledged":true} after each iteration

# Step 7: Create index patterns
# Access http://<ip of Ubuntu server>:5601 
#change logstash-* to bigip.logs* and press tab. The screen should change and provide you with the “Time-field name”. Select @timestamp.
# Click on create and repeat the same process by clicking on Add new for http.logs-*, ddos.logs-*, dns.logs-*

#Step 8: Configure kibana dashboard
# Access Settings> Objects and select Import
#Import F5_ELK_Dashboard.json (shared on github)
#start logstash service
service logstash start

#Configure BigIP as log forwarder for designated ports (mentioned in syslog-ng config file). Enjoy !


##Special Thanks to Zakeer & Jarrod ! ##
 






