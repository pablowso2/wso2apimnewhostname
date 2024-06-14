**WELCOME**

The purpose of this guide is to show you how can you change your localhost domain to any other you want.

Asumptions:
-APIM instance is already install in you localhost machine without any error. -In case you don´t have please check https://wso2.com/api-manager
-This Excersice was done with te version of APIM 4.3.0

Steps:

1) With the instance of the APIM turn off you must go to this directory API_HOME/repository/resources/security
2) Run this command: keytool -genkey -alias **<ALIAS_KEY>** -keyalg RSA  -keysize 2048 -keystore **<FILE_KEY_STORE_NAME>**.jks -storepass **<STORE_PASS>** -keypass **<KEY_STOREPASS>** -dname "CN=**<CN_NAME SHOULD BE EQUAL TO ALIAS_KEY>**, O=**<ORGANIZATIÓN>**, L=**<LOCATION>**, ST=**<STATE>**, C=**<XX>**, OU=**<BUSINESS UNIT>**" -ext "SAN=DNS:localhost"

3)Needs to export the public key genereated as base 64
keytool -exportcert -alias **<ALIAS_KEY>** -keystore **<FILE_KEY_STORE_NAME>**.jks -rfc -file **<FILE_NAME_TO_STORE_BASE64>**.pem

3)Delete the existing similar alias if exist. If you have already run this scripts, it is necesary to delete the existing entry of the ALIAS_KEY.
keytool -delete -alias **<ALIAS_KEY>**  -keystore client-truststore.jks -storepass wso2carbon

4)Import the public key in the client-trustore of the WSO2APIM
keytool -import -alias **<ALIAS_KEY>** -file **<FILE_NAME_TO_STORE_BASE64>**.pem -keystore client-truststore.jks -storepass wso2carbon

5)Edit you hosts file of your machine with this:
127.0.0.1	localhost dev.wso2.com

6)Needs to change the deployment.toml file with this entries:
[server]
hostname = "**<ALIAS_KEY>**"

#Create a new keystore.tls part and turn off the existing one
[keystore.tls]
file_name =  "**<FILE_KEY_STORE_NAME>**jks"
type =  "JKS"
password =  "**<STORE_PASS>** "
alias =  "**<ALIAS_KEY>**"
key_password =  "**<KEY_STOREPASS>**"

[[apim.gateway.environment]]
service_url = "https://**<ALIAS_KEY>**:${mgt.transport.https.port}/services/"
username= "${admin.username}"
password= "${admin.password}"
ws_endpoint = "ws://**<ALIAS_KEY>**:9099"
wss_endpoint = "wss://**<ALIAS_KEY>**:8099"
http_endpoint = "http://**<ALIAS_KEY>**:${http.nio.port}"
https_endpoint = "https://**<ALIAS_KEY>**:${https.nio.port}"
websub_event_receiver_http_endpoint = "http://**<ALIAS_KEY>**:9021"
websub_event_receiver_https_endpoint = "https://**<ALIAS_KEY>**:8021"


[apim.key_manager]
enable_apikey_subscription_validation = true
service_url = "https://**<ALIAS_KEY>**:${mgt.transport.https.port}/services/"
username = "$ref{super_admin.username}"
password = "$ref{super_admin.password}"

#[apim.devportal]
url = "https://**<ALIAS_KEY>**:${mgt.transport.https.port}/devportal"

[event_listener.properties]
notification_endpoint = "https://**<ALIAS_KEY>**:${mgt.transport.https.port}/internal/data/v1/notify"


7)Start APIM

8)After APIM has already started, you must go to carbon and change this "callback url"
regexp=(https://**<ALIAS_KEY>**:9446/publisher/services/auth/callback/login|https://**<ALIAS_KEY>**:9446/publisher/services/auth/callback/logout)





