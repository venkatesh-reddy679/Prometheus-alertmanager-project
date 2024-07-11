![image](https://github.com/venkatesh-reddy679/Prometheus-alertmanager-project/assets/60383183/633f40b6-b544-4305-b0dc-393b2ade497e)


Prometheus: Prometheus is an open-source cross platform monitoring and alerting tool kit that scrapes the configured targets such as servers, containers, and applications periodically and stores the collected metrics in a time-series database which can be retrieved and visualised later using prometheus built-in feautres like console-templates and expression browser or third-party tools like Grafana.

Architecture of Prometheus:

![image](https://github.com/venkatesh-reddy679/Prometheus-alertmanager-project/assets/60383183/f0c4fe0c-6de6-400c-b71c-d80d053bd634)

There are 3 components within in the prometheus server
   1. Retrieval worker: It is reponsible for scraping the configred targets periodically
   2. Time-Sereis database: The collected metrics are stored along with the timestamp when is is collected in the time-series database. Basically, a time-series data is a metic with a unique set of labels for which we collected the values overtime
   3. HTTP api server: Promethues use a specific query language called PormQL to retreive the time-series data and validate. We can execute those PromQL queries programatically through the HTTP api server. This api is exposed at "/api/v1/query"

      example: curl -X POST http://<prometheus-ip>:9090/api/v1/query --data "query=up" --data "time=unixtimestamp"
      
Prometheus comes with various components like
1. Exporters: Prometheus basically operates based on pull model, means that when targets expose metrics, then prometheus server scrapes them periodically. By default, targets like servers (linux or windows) or applications doesn't expose metrics. So Promethues provides exporters like Node exporters (for linux servers), Windows exporters (for windows servers), blackbox exporters (for monitoring the performance and health of endpoints) which should be installed on the specific target and responsible for collecting metrics from the targets and expose the in a format that the prometheus can understand and scrape.
2. Prometheus-client libraries: Thes libraries enable us to instrument custom-built application developed in python, java, ruby, and go to expose custome metrics like total number of requests to an api, total no of errors, and so on.
3. Pushgateway: Prometheus may not be able to scrape short-lived jobs which are configured as targets as they run for shorter period of time. To overcome this, prometheus provides a component called pushgateway that collectes the metrics from jobs and expose them in a format that promethue can understand. Prometheus scrapes the pushgateways like any other target perodically.
4. Service Discovery: As promethues operate based on pull model, it should know  about list of target to scrape. So, in the case of a static environment, we update the promethues configuration file and restart the promethus server everytime we add or remove a target. But in the case of dynamic environment like kubernetes or any auto-scaling  enabled cloud environment (AWS, Azure, GCP) where the resources will be created and terminated automatically, it will be very difficult to update the promethues configuration everytime. So, prometheus comes with a component called service-discovery that discovers the list of targets to monitor dynamically.
5. AlertManager: Prometheus allows us to define alert rules and when any rules is violated, then promethues triggers that alert but it can't handle the nofitications itself. So, Promethues comes with alertmanager which actions on the alerts by sending notification to the administrators or corresponding people through gmal, slack channel, sms, etc.

Steps to install Prometheus as SystemD service: 

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


Node Exporter: Node Exporter is a type of prometheus exporter that is designed to extract the metrics from linux servers and expose them in a format that prometheus can understand and scrape. Node Exporter should be installed onn target machines.

Steps to install and setup Node Exporter as SystemD service:

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


Blackbox Exporter: Blackbox exporter is a type of prometheus exporter that is used to probe the endpoints such as http, https, tcp, dns and more. It allows us to monitor the health and performance of these endpoints from an external perspective, essentially treating the endpoints as "black boxes" whose internal workings are not inspected, only their responses.

Key Features
HTTP(S) Probing: Measures latency, response codes, and other HTTP-level metrics.
TCP Probing: Checks the availability and latency of TCP connections.
DNS Probing: Measures the performance and correctness of DNS lookups.
ICMP Probing: Similar to ping, it checks the availability and round-trip time of a host.

Steps to install and setup blackbox exporter as SystemD service: (over the same prometheus server)

1. go to prometheus downloads page and choose the specific version based on the operating system

   ![image](https://github.com/venkatesh-reddy679/Prometheus-alertmanager-project/assets/60383183/43e00b1e-c9f8-4d24-9d56-fff672ead830)

2. download the tar file using wget, and extract it

   ![image](https://github.com/venkatesh-reddy679/Prometheus-alertmanager-project/assets/60383183/22044e08-b417-42a9-9305-dcfdfc29dadc)

3. create a user named blackbox_exporter, move the blackbox_exporter executable file into /usr/local/bin and giv the ownership to the user blackbox_user, then create directory  named blackbox_exporter in /etc and move the configuration file into the directry and give the ownership to the user blackbox_exporter.

   ![image](https://github.com/venkatesh-reddy679/Prometheus-alertmanager-project/assets/60383183/4342120b-5aba-4301-a5bd-9dc86fea084e)

4. create a service unit file in /etc/systemd/system directory and start the blackbox_exporter service

   ![image](https://github.com/venkatesh-reddy679/Prometheus-alertmanager-project/assets/60383183/c90660e4-0fe7-4291-be3d-fb0844da93da)
   ![image](https://github.com/venkatesh-reddy679/Prometheus-alertmanager-project/assets/60383183/7d000264-db75-4181-accf-26d7f3b82211)


   








    


   























