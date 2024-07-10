Prometheus: Prometheus is an open-source cross platform monitoring and alerting tool kit that scrapes the configured targets such as servers, containers, and applications periodically and stores the collected metrics in a time-series database which can be retrieved and visualised later using prometheus built-in feautres like console-templates and expression browser or third-party tools like Grafana.

Architecture of Prometheus:

![image](https://github.com/venkatesh-reddy679/Prometheus-alertmanager-project/assets/60383183/f0c4fe0c-6de6-400c-b71c-d80d053bd634)

There are 3 components within in the prometheus server
   1. Retrieval worker: It is reponsible for scraping the configred targets periodically
   2. Time-Sereis database: The collected metrics are stored along with the timestamp when is is collected in the time-series database. Basically, a time-series data is a metic with a unique set of labels for which we collected the values overtime
   3. HTTP api server: Promethues use a specific query language called PormQL to retreive the time-series data and validate. We can execute those PromQL queries programatically through the HTTP api server. This api is exposed at "/api/v1/query"

      example: timestamp=$(($(date +%s) - 300))
               curl -X POST http://ip:9090/api/v1/query --data-urlencode "query=up" --data-urlencode "time=${timestamp}"





