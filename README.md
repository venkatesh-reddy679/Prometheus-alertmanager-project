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

1. 








