![image](https://github.com/venkatesh-reddy679/Prometheus-alertmanager-project/assets/60383183/633f40b6-b544-4305-b0dc-393b2ade497e)


***Prometheus***: Prometheus is an open-source cross platform monitoring and alerting tool kit that scrapes the configured targets such as servers, containers, and applications periodically and stores the collected metrics in a time-series database which can be retrieved and visualised later using prometheus built-in feautres like console-templates and expression browser or third-party tools like Grafana.

***Architecture of Prometheus***:

![image](https://github.com/venkatesh-reddy679/Prometheus-alertmanager-project/assets/60383183/f0c4fe0c-6de6-400c-b71c-d80d053bd634)

There are 3 components within in the prometheus server
   1. Retrieval worker: It is reponsible for scraping the configred targets periodically
   2. Time-Sereis database: The collected metrics are stored along with the timestamp when is is collected in the time-series database. Basically, a time-series data is a metic with a unique set of labels for which we collected the values overtime
   3. HTTP api server: Promethues use a specific query language called PormQL to retreive the time-series data and validate. We can execute those PromQL queries programatically through the HTTP api server. This api is exposed at "/api/v1/query"

      example: curl -X POST http://<prometheus-ip>:9090/api/v1/query --data "query=up" --data "time=unixtimestamp"
      
Prometheus comes with various components like
1. ***Exporters***: Prometheus basically operates based on pull model, means that when targets expose metrics, then prometheus server scrapes them periodically. By default, targets like servers (linux or windows) or applications doesn't expose metrics. So Promethues provides exporters like Node exporters (for linux servers), Windows exporters (for windows servers), blackbox exporters (for monitoring the performance and health of endpoints) which should be installed on the specific target and responsible for collecting metrics from the targets and expose the in a format that the prometheus can understand and scrape.
2. ***Prometheus-client libraries***: Thes libraries enable us to instrument custom-built application developed in python, java, ruby, and go to expose custome metrics like total number of requests to an api, total no of errors, and so on.
3. ***Pushgateway***: Prometheus may not be able to scrape short-lived jobs which are configured as targets as they run for shorter period of time. To overcome this, prometheus provides a component called pushgateway that collectes the metrics from jobs and expose them in a format that promethue can understand. Prometheus scrapes the pushgateways like any other target perodically.
4. ***Service Discovery***: As promethues operate based on pull model, it should know  about list of target to scrape. So, in the case of a static environment, we update the promethues configuration file and restart the promethus server everytime we add or remove a target. But in the case of dynamic environment like kubernetes or any auto-scaling  enabled cloud environment (AWS, Azure, GCP) where the resources will be created and terminated automatically, it will be very difficult to update the promethues configuration everytime. So, prometheus comes with a component called service-discovery that discovers the list of targets to monitor dynamically.
5. ***AlertManager***: Prometheus allows us to define alert rules and when any rules is violated, then promethues triggers that alert but it can't handle the nofitications itself. So, Promethues comes with alertmanager which actions on the alerts by sending notification to the administrators or corresponding people through gmal, slack channel, sms, etc.

***Steps to install Prometheus as SystemD service***: 

with this type of installation, we can operate the prometheus server lifecycle using systemctl 

1. go to prometheus official downloads page and choose the version based on operating system

   ![image](https://github.com/venkatesh-reddy679/Prometheus-alertmanager-project/assets/60383183/75c36f14-fee9-47ba-adad-ec0453f1f64d)
2. download and extract the prometheus tar file

   ![image](https://github.com/venkatesh-reddy679/Prometheus-alertmanager-project/assets/60383183/c451eb07-35d3-410c-9c6f-47fbec2fd257)4

3. create user named prometheus, create a directory with name prometheus in /etc, and move prometheus.yml, consoles, and console_libraries, then finally give the ownership of prometheus folder to user prometheus.

   ![image](https://github.com/venkatesh-reddy679/Prometheus-alertmanager-project/assets/60383183/ea3f5c7f-3caf-415f-93fa-17cb5b1a51e6)

4. move the prometheus executable file and promtool command line utility into /usr/local/bin, give ownership to user prometheus

   ![image](https://github.com/venkatesh-reddy679/Prometheus-alertmanager-project/assets/60383183/850a427a-897c-49d8-9478-ef4e1f2c48d6)

5. create a directory with name prometheus in /var/lib whcih will be used by promethues to store the time-series data and give ownership to user prometheus. Create a service unit file in /etc/systemd/system with ame prometheus.service and configure the service.

   ![image](https://github.com/venkatesh-reddy679/Prometheus-alertmanager-project/assets/60383183/f60ffe28-39d4-49f1-af4e-1dbe2f5c87a4)
   ![image](https://github.com/venkatesh-reddy679/Prometheus-alertmanager-project/assets/60383183/422a90ea-2ac4-4b81-adf9-33b476521faa)

6. reload the daemon and start the prometheus service, enable it to start on  boot

   ![image](https://github.com/venkatesh-reddy679/Prometheus-alertmanager-project/assets/60383183/a42f9440-11fb-4a78-b9ba-d32140cbe6d4)

7. prometheus is running on port 9090 of the server and prometheus scrapes itself by default to monitor the reachability, cpu, storage consumed by promethues

   ![image](https://github.com/venkatesh-reddy679/Prometheus-alertmanager-project/assets/60383183/53f1848c-aa23-4924-8f62-8a56d871a57e)

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

***Node Exporter***: Node Exporter is a type of prometheus exporter that is designed to extract the metrics from linux servers and expose them in a format that prometheus can understand and scrape. Node Exporter should be installed onn target machines.

***Steps to install and setup Node Exporter as SystemD service***:

1. go to prometheus downloads page and choose the version based on your operating system

   ![image](https://github.com/venkatesh-reddy679/Prometheus-alertmanager-project/assets/60383183/692357fd-c917-435a-aca1-5786b04b2157)

2. download the tar file using wget and extract it

   ![image](https://github.com/venkatesh-reddy679/Prometheus-alertmanager-project/assets/60383183/2e986c1e-301e-493e-a78b-96cbdaac31c7)

3. create a user named node_exporter, move the node_exporter executable file into /usr/local/bin folder and give the ownership of the file to the user node_exporter

   ![image](https://github.com/venkatesh-reddy679/Prometheus-alertmanager-project/assets/60383183/fd102060-502e-4772-8759-85d1b830f6eb)
   
4. create a service unit file /etc/systemd/system/node-exporter.service

   ![image](https://github.com/venkatesh-reddy679/Prometheus-alertmanager-project/assets/60383183/63d0bdff-187f-4650-887f-9c02fa9401cd)

5. reload the system daemon, start the node_exporer service, and enable it to start on system boot

   ![image](https://github.com/venkatesh-reddy679/Prometheus-alertmanager-project/assets/60383183/2ceb3600-55ef-4673-ac32-f6183d1059d2)

6. node_exporter runs on port 9100 of the target. click on the /target url to view the metrics  such as cpu, storage, memory, arp entries, etc. collected by node_exporter.

   ![image](https://github.com/venkatesh-reddy679/Prometheus-alertmanager-project/assets/60383183/92bd8f6d-c165-493c-a318-ba7554f8255e)

7. update the prometheus configuration file with the address of target and restart prometheus service

   ![image](https://github.com/venkatesh-reddy679/Prometheus-alertmanager-project/assets/60383183/7b1be010-db85-487a-aeae-5ef28e5e66dc)

8. In the prometheus web UI, navigate to targets section to verify whether prometheus is able to reach the configured target. 

   ![image](https://github.com/venkatesh-reddy679/Prometheus-alertmanager-project/assets/60383183/c62faf53-46fe-49bf-b07f-f06185c36cce)

9. "up" is the most simplest PromQL expression that indicates whether prometheus is able to scrape the target and the two target labels instance and job will be associated with every timeseries that we get from the target.

    ![image](https://github.com/venkatesh-reddy679/Prometheus-alertmanager-project/assets/60383183/9bf6717d-2ce8-414f-8613-a18aaf70beb0)

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------


***Blackbox Exporter***: Blackbox exporter is a type of prometheus exporter that is used to probe the endpoints such as http, https, tcp, dns and more. It allows us to monitor the health and performance of these endpoints from an external perspective, essentially treating the endpoints as "black boxes" whose internal workings are not inspected, only their responses.

***Key Features***:

HTTP(S) Probing: Measures latency, response codes, and other HTTP-level metrics.

TCP Probing: Checks the availability and latency of TCP connections.

DNS Probing: Measures the performance and correctness of DNS lookups.

ICMP Probing: Similar to ping, it checks the availability and round-trip time of a host.

***Steps to install and setup blackbox exporter as SystemD service***: (over the same prometheus server)

1. go to prometheus downloads page and choose the specific version based on the operating system

   ![image](https://github.com/venkatesh-reddy679/Prometheus-alertmanager-project/assets/60383183/43e00b1e-c9f8-4d24-9d56-fff672ead830)

2. download the tar file using wget, and extract it

   ![image](https://github.com/venkatesh-reddy679/Prometheus-alertmanager-project/assets/60383183/22044e08-b417-42a9-9305-dcfdfc29dadc)

3. create a user named blackbox_exporter, move the blackbox_exporter executable file into /usr/local/bin and giv the ownership to the user blackbox_user, then create directory  named blackbox_exporter in /etc and move the configuration file into the directry and give the ownership to the user blackbox_exporter.

   ![image](https://github.com/venkatesh-reddy679/Prometheus-alertmanager-project/assets/60383183/4342120b-5aba-4301-a5bd-9dc86fea084e)

4. create a service unit file in /etc/systemd/system directory and start the blackbox_exporter service. Blackbox_exporter runs on port 9115 of the host.

   ![image](https://github.com/venkatesh-reddy679/Prometheus-alertmanager-project/assets/60383183/c90660e4-0fe7-4291-be3d-fb0844da93da)
   ![image](https://github.com/venkatesh-reddy679/Prometheus-alertmanager-project/assets/60383183/7d000264-db75-4181-accf-26d7f3b82211)
   ![image](https://github.com/venkatesh-reddy679/Prometheus-alertmanager-project/assets/60383183/c87e881b-8bb7-4f66-b9ab-7e04ebfb482e)

5. I have exposed a java application on port 8080 of the server where the node_exporter is already running and will configure this endpoint as target in the blackbox_exporter configuration. we can find the blackbox_exporter prometheus configuration in thier offifical github https://github.com/prometheus/blackbox_exporter and restart prometheus

   Note:
   
   if you have a java application built using maven framework, then package it using "mvn package" command and run it using "java -jar" command

   if you have a standalone java application, compile it using "javac" command that creates a ".class" file, create a META-INF/MANIFEST.MF file with property "main-class: name of the java class which conatins the main method", then create a jar file using the command "java cfm name_of_jar_file.jar MANIFEST.MF java.class", and finally run the application packed as a JAR file using "java -jar" command
   
   ![image](https://github.com/venkatesh-reddy679/Prometheus-alertmanager-project/assets/60383183/7f4c1795-a747-49cc-9576-37b206610789)
   ![image](https://github.com/venkatesh-reddy679/Prometheus-alertmanager-project/assets/60383183/4eaf9874-e463-4d8a-8c0e-20f039a79143)

   we have to configure the endpoint to probe under static_configs > targets and configure the ip where the blackbox_exporter is running under relabel_configs > replacement. for this project, i'm running  the exporter on the same prometheus server.

6. In prometheus web UI, go to status > targets to view the configured target status. Blackbox_exporter probes the configured endpoints, collects the metrics, and expose them over /probe endpoint in a format that prometheus understand and scrape. Prometheus scrapes the blackbox_exporter like any other target (metrics_path default to /metrics endpoint) but over /probe endpoint .

   ![image](https://github.com/venkatesh-reddy679/Prometheus-alertmanager-project/assets/60383183/1b2345d2-3008-4079-9473-8cdc67fd0abc)
   ![image](https://github.com/venkatesh-reddy679/Prometheus-alertmanager-project/assets/60383183/19817472-3dad-4999-b9bb-822306e96151)

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------


***Alert Manager***: prometheus can trigger alert but can't action on the alerts. So, Alert manager is reponsible for receiving the alerts from prometheus server and action on them by sending actual notifications to the users.

***Architecture of Alert Manager***:

![image](https://github.com/user-attachments/assets/62426345-ba4e-4388-aa19-43bd69751f33)

We can configure a single alert manager to receive alerts from multiple Prometheus servers. So, the Prometheus servers send alerts to the alert manager through alertmanager api.

When any alert comes to the alert manager, dispatcher component receives it first and forwards the alert to the inhibition node which allows us to specify rules where we can supress alerts if other alert exists. For example, if alert x exist, I don’t want you to do anthing on alert y. Then, the alert will be forwarded to silencing node where the silencers will mute the alerts. For example, if there is kown maintenance, we expect certain alerts and as it is a planned maintenance, we may want to suppress alerts from being triggerd and notification from being sent. We can set up a silence rule in the alert manager UI to silence the alerts for specific period of time. Then, the alert will be forwarded to routing section which is responsible for deciding whilch alert will be sent to who through what integration. Then the alert will be forwarded to notification engine which is responsible for having all of those third-party integration like email, pager, and chat and sending notification out to a user.

***steps to install and setup alert manager as SystemD service***: (installing on same promethues server)

1. go to prometheus downloads page, and download the appropriate version based on your operating system

   ![image](https://github.com/user-attachments/assets/55ef8db1-342c-40c6-84ef-9b4624b40bc6)
   ![image](https://github.com/user-attachments/assets/5c093ba5-8c35-4b52-808a-08dffea236b5)

2. create an user named alertmanager, move the alertmanager executable file, and amtool to /usr/local/bin and give ownership on them to use alertmanager, make a directory alertmanager in /etc, move the alertmanager.yml file into the director and give ownership ofthe directory to alertmanager

    ![image](https://github.com/user-attachments/assets/ed0eff16-dee0-421e-bf48-f5a98277a54c)

3. create a service unit file for alertmanager, reload the system daemon and start the alertmanager service

   ![image](https://github.com/user-attachments/assets/bc4d5ff2-a042-48aa-99c5-2490bfeeadd7)
   ![image](https://github.com/user-attachments/assets/4cc1e5cd-968e-4e5b-91fc-fd1d2b4ca9ce)

4. Alertmanager is exposed on port 9093 oh the host

   ![image](https://github.com/user-attachments/assets/b5ec87bb-9b8d-44ab-94b5-c84053303345)


---------------------------------------------------------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

***setting up the alerts***:

1. create a rules.yml file in /etc/prometheus and give ownership to the user prometheus. Define the alert rules in the file using PromQL expresion language and when any alert expression evaluates to true, means any expression retuns a vector , then the a alert is triggered for each result vector. (refer to the rules.yml file in the git)

Alerts can be in 3 differet states:

***inactive***: this state indicates that the PromQL expressions have not returned any result

***pending***: this state indicates that the PromQL expressions returned results but it hasn't been long enough to be considered firing. This actually depends on what value we set for for clause for each alert. if there is no for clause defined, then the alert immediatly enter firing state

***firing***: this state indicates that PromQL expression evaluates to true for the period defined in for clause continuosly and prometheus server sends the alert to alert manager to send the notification to the users.

2. update the prometheus configuration file and restart prometheus server, and verify the configured alert rules in prometheus web UI

   ![image](https://github.com/user-attachments/assets/4b60cbfe-2fbf-4b9b-ad60-480c72187db0)
   ![image](https://github.com/user-attachments/assets/52e43186-303a-43cb-9baa-50953429f7ba)

3. In the alerts section we can see which alert are in inactive, pending and firing state

   ![image](https://github.com/user-attachments/assets/f5ac8a78-c3a3-4f2b-ad87-82609ab5abae)

-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

***Configuring the alertmanager to send notifications***:














   



 










   








    


   























