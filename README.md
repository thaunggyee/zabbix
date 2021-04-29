# zabbix

h1. Zabbix (Monitoring Tool)

h2. How to set up Zabbix on kubernetes using helm chart

* Create namespace for zabbix
<pre>
$kubectl create namespace monitoring
</pre>
* Add repository to hlem chart and update the list
<pre>
$helm repo add cetic https://cetic.github.io/helm-charts
$helm repo update
</pre>
* Install the zabbix with the monitoring namespace
<pre>
$helm install zabbix cetic/zabbix  -n monitoring
</pre>
* Run the following command and get the CLUSTER IP to access the zabbix web interface
* <pre>
$kubectl get service -n monitoring
</pre>
* Access zabbix in http://CLUSTERIP
* If you don't understand , please follow this link https://hub.kubeapps.com/charts/cetic/zabbix

h2. How to get mail alert from zabbix

Please follow this guide https://bestmonitoringtools.com/zabbix-alerts-setup-zabbix-email-notifications-escalations/#Option_1_Send_Zabbix_email_notifications_via_Gmail

h2. How to get Telegram alert from zabbix

* Start a conversation with BotFather and enter the following text to get the new bot.Test "/newbot".
* And give the username of your bot.for me Zabbix_KNT_bot
* Get the token and start the conversation with Zabbix_KNT_bot. After that , test "/start".
* Start the conversation with IDBot and get the id of your telegram by sending "/getid"
* Configure the telegram media from web interface. Go to Administration->Media type->Telegram and add the token.*
* Configure user media type and Action with your telegram id.

h2. How to monitor remote server using zabbix

* Set up the Fedora or Ubuntu server in virtual box
* Install the zabbix-agent on Fedora Linux
<pre>
$yum install zabbix-agent
</pre>
* Install the zabbix-agent on Ubuntu Linux
<pre>
$sudo apt install zabbix-agent
</pre>

* Edit the server, serverActive and hostname in zabbix-agentd.conf . I added 192.168.56.1 in server and umcommet the ListenPort.(Server IP, hostname, listenPort)
* Add port in firewall
<pre>
$firewall-cmd --permanent --add-port=10050/tcp
</pre>
* Enable the zabbix agent
<pre>
$sudo systemctl enable zabbix-agent.service
$sudo systemctl start zabbix-agent.service
</pre>
* Add host in zabbix server using zabbix web interface. Go to the Configuration->Host->Create New Host and add then hostname,name and server ip which is the ip address of remote server

h2. How to authenticate with LDAP server

* First create the super admin user in zabbix which is the *same name* with the user exit in LDAP server.(eg. I created user khin.n.tun in zabbix)
If you don't know how to create user, please follow this link. https://www.zabbix.com/documentation/current/manual/config/users_and_usergroups/user
* Log in to the account and go to Administration->Authentication->LDAP setting.
* Enable LDAP authentication and Configuration is as follow.(FRONTIIR uses AD)
<pre>
LDAP Host          = 192.168.10.96
Port               = 389
BaseDN             = OU=FRONTIIR,DC=FRONTIIR,DC=net
Search Attribute   = sAMAccountName
Bind DN            = LDAP user (khin.n.tun@frontiir.net)
Bind Password      = LDAP password (**********)
Login              = khin.n.tun  (crated zabbix account)
User password      = LDAP password (**********)
</pre>
* Try to text . If successful, go to the tab Authentication and update the Default Authentication to LDAP.
* log out from this user and try to log in with LDAP account .

h2. How to create zabbix user for LDAP user

* I found this tool "zabbix-ldap-sync" https://github.com/dnaeon/zabbix-ldap-sync to automate the user creation.
* It creates the user according to LDAP group and remove the zabbix user that is not in LDAP server.

* I found the bash-script "zabbix-ldap-sync-bash" https://github.com/BernhardLinz/zabbix-ldap-sync-bash to sync with LDAP user group and Zabbix user group. 
