# Maintenance and UI

### APIs opened on the nodes:
  - A Lighthouse beacon node can be configured to expose an HTTP server by supplying the --http flag. The default listen address is http://127.0.0.1:5052
  - A Lighthouse validator client can be configured to expose a HTTP server by supplying the --http flag. The default listen address is http://127.0.0.1:5062
  - Lighthouse provides an extensive suite of metrics and monitoring in the Prometheus export format via a HTTP server built into Lighthouse.These metrics are generally consumed by a Prometheus server and displayed via a Grafana dashboard. These components are available in a docker-compose format at sigp/lighthouse-metrics.By default, these metrics are disabled but can be enabled with the --metrics flag. Start a beacon node with the metrics server enabled:ensure that the metrics are available on the default port:localhost:5054/metrics
  - Start a validator client with the metrics server enabled:lighthouse vc --metrics ensure that the metrics are available on the default port: curl localhost:5064/metrics

### automatic start up or resume
  - Warning: it will take around 20 minutes for validator to be activated after it is been stopped and then restarted
  - chmod +x $HOME/ethnode/startExecuteNode.sh
  - chmod +x $HOME/ethnode/startBeaconNode.sh
  - chmod +x $HOME/ethnode/startValidator.sh

  - sudo nano geth.service
    ```
      ### content to be writen to geth.service
      [Unit]
      Description       = Geth Node Service
      
      [Service]
      User              = steven
      Type              = simple
      # WorkingDirectory  = /home/steven/ethnode
      ExecStart         = /bin/bash -c '/home/steven/ethnode/startExecuteNode.sh'
      ExecReload        = pkill -HUP geth
      KillSignal        = SIGINT
      RestartKillSignal = SIGINT
      TimeoutStopSec    = 300
      LimitNOFILE       = 32768
      Restart           = always
      RestartSec        = 10
      SyslogIdentifier  = geth
      
      [Install]
      WantedBy          = multi-user.target
    ```
 
  - sudo mv $HOME/ethnode/geth.service /etc/systemd/system/geth.service
  - sudo chmod 644 /etc/systemd/system/geth.service
  - sudo systemctl daemon-reload
  - sudo systemctl enable geth
  - sudo systemctl restart geth
  - sudo systemctl status geth
  - journalctl --unit=geth --follow

  - sudo nano lighthousebn.service
    ```
      ### content to be writen to lighthousebn.service
      [Unit]
      Description       = lighthouse beacon node Service
      
      [Service]
      User              = steven
      Type              = simple
      # WorkingDirectory  = /home/steven/ethnode
      ExecStart         = /bin/bash -c '/home/steven/ethnode/startBeaconNode.sh'
      ExecReload        = pkill -HUP lighthousebn
      KillSignal        = SIGINT
      RestartKillSignal = SIGINT
      TimeoutStopSec    = 300
      LimitNOFILE       = 32768
      Restart           = always
      RestartSec        = 10
      SyslogIdentifier  = lighthousebn
      
      [Install]
      WantedBy          = multi-user.target
    ```
 
  - sudo mv $HOME/ethnode/lighthousebn.service /etc/systemd/system/lighthousebn.service
  - sudo chmod 644 /etc/systemd/system/lighthousebn.service
  - sudo systemctl daemon-reload
  - sudo systemctl enable lighthousebn
  - sudo systemctl restart lighthousebn
  - sudo systemctl status lighthousebn
  - journalctl --unit=lighthousebn --follow

### warning: don't start validator automatically . it has high risk to duplicate or start more than one instance.
  - sudo nano lighthouseva.service
    ```
      ### content to be writen to lighthouseva.service
      [Unit]
      Description       = lighthouse validator Node Service
      
      [Service]
      User              = steven
      Type              = simple
      # WorkingDirectory  = /home/steven/ethnode
      ExecStart         = /bin/bash -c '/home/steven/ethnode/startValidator.sh'
      ExecReload        = pkill -HUP lighthouseva
      KillSignal        = SIGINT
      RestartKillSignal = SIGINT
      TimeoutStopSec    = 300
      LimitNOFILE       = 32768
      Restart           = always
      RestartSec        = 10
      SyslogIdentifier  = lighthouseva
      
      [Install]
      WantedBy          = multi-user.target
    ```
 
  - sudo mv $HOME/ethnode/lighthouseva.service /etc/systemd/system/lighthouseva.service
  - sudo chmod 644 /etc/systemd/system/lighthouseva.service
  - sudo systemctl daemon-reload
  - sudo systemctl enable lighthouseva
  - sudo systemctl restart lighthouseva
  - sudo systemctl status lighthouseva
  - journalctl --unit=lighthouseva --follow

### Lighthouse UI Siren
  - Siren is a user interface built for Lighthouse that connects to a Lighthouse Beacon Node and a Lighthouse Validator Client to monitor performance and display key validator metrics.
  - docker run --rm -ti --name siren -p 80:80 sigmaprime/siren
  - This will open port 80 and allow your browser to connect. You can choose another local port by modifying the command. For example -p 8000:80 will open port 8000. To view Siren, simply go to http://localhost in your web browser.
  - add --gui --http --http-allow-origin "*" to the lighthouse Beacon Node
  - add --http --http-allow-origin "*" to the lighthouse Validator Node

### Geth GraphQL 
  - In addition to the JSON-RPC APIs, Geth supports the GraphQL API as specified by EIP-1767.
  - --graphql flag should be passed to Geth
  - An easy way to try out queries is the GraphiQL interface shipped with Geth. To open it visit http://localhost:8545/graphql/ui

### Geth Monitoring with InfluxDB and Grafana
  - installation of InfluxDB:
        curl -tlsv1.3 --proto =https -sL https://repos.influxdata.com/influxdb.key | sudo apt-key add
        source /etc/lsb-release
        echo "deb https://repos.influxdata.com/${DISTRIB_ID,,} ${DISTRIB_CODENAME} stable" | sudo tee /etc/apt/sources.list.d/influxdb.list
        sudo apt update
        sudo apt install influxdb -y
        sudo systemctl enable influxdb
        sudo systemctl start influxdb
        sudo apt install influxdb-client
        influx
        create database geth
        create user geth with password choosepassword
        exit
    
  - Setting up Prometheus
      - add parameters to geth --metrics --metrics.addr 127.0.0.1 --metrics.influxdb --metrics.influxdb.endpoint "http://0.0.0.0:8086" --metrics.influxdb.username "geth" --metrics.influxdb.password "chosenpassword"
      - in terminal: use command to check it is working
      - influx
      - use geth
      - show measurements

  - setting up grafana
      - When Grafana is up and running, it should be reachable at localhost:3000
      - The browser first redirects to the Grafana home page to set up the source data. Click on the "Data sources" icon and then click on "InfluxDB".
      - fill the following fields:
            URL: http://localhost:8086
            InfluxDB Details
            Database: geth
            User: <your-user-name>
            Password: <your-password>
      - Grafana is now set up to read data from InfluxDB. Now a dashboard can be created to interpret and display it. Dashboards properties are encoded in JSON files which can be created by anybody and easily imported. On the left bar, click on the "Dashboards" icon, then "Import".


### Metrics monitoring
  - Metrics, monitoring, and alerting are the key elements of a monitoring system. Metrics are the input, the raw data needed for monitoring performance, health, and availability. Monitoring is what alerting is built on top of. Together, they provide insight into how your applications and infrastructure are performing. They detect performance or usage anomalies, help you troubleshoot and identify issues, reveal usage and behavior patterns or trends, and help you understand the impact of any changes you make to your applications or infrastructure. Whenever metrics meet conditions you have defined as part of alert rules, monitoring solutions send notifications prompting you to investigate issues and even help you narrow down and identify possible root causes.
  - add --validator-monitor-auto to lighthouse Beacon Node
  - add --metrics to lighthouse Beacon Node and Validator node
  - download lighthouse-metrics :
      - git clone --progress https://github.com/sigp/lighthouse-metrics.git
  - download docker-compose
      - sudo apt  install docker-compose
  - cd ~/metrics/lighthouse-metrics
  - docker-compose up --build (run this command everytime you start the metrics)
  - How to stop:
      1. solution 1:
        - cd ~/metrics/lighthouse-metrics
        - docker-compose stop
      2. solution 2:
        - press Ctrl-C to stop a docker-compose process running in the foreground
      3. For periodic cleanup of all stopped containers, run the following script while your docker host is running: devtools prune
      4. 
  - 
  - Prometheus is an open-source systems monitoring and alerting toolkit. Prometheus collects and stores its metrics as time series data, i.e. metrics information is stored with the timestamp at which it was recorded, alongside optional key-value pairs called labels.
      1. Install prometheus node exporter:
          - sudo apt-get install -y prometheus-node-exporter
          - sudo systemctl enable prometheus-node-exporter.service 
      2. install prometheus:
         - sudo apt-get install -y prometheus
      3. install
         - wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -
         - echo "deb https://packages.grafana.com/oss/deb stable main" > grafana.list
         - sudo mv grafana.list /etc/apt/sources.list.d/grafana.list
         - sudo apt-get update && sudo apt-get install -y grafana
      4. enable services
         - Enable services so they start automatically:
         - sudo systemctl enable grafana-server.service
         - sudo systemctl enable prometheus.service
         - sudo systemctl enable prometheus-node-exporter.service
      5. change the yml configuration files as needed. see prometheus.yml in this branch
      6. restart services:
          - sudo systemctl restart grafana-server.service
          - sudo systemctl restart prometheus.service
          - sudo systemctl restart prometheus-node-exporter.service       
      8. verify the service is running:
        - sudo systemctl status grafana-server.service prometheus.service prometheus-node-exporter.service
      9. setting up grafana on http://localhost:3000
      10. 
