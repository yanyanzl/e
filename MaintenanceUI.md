# Maintenance and UI

### Servers and APIs opened on the nodes:
  1. Interacting with the nodes Servers and API
      - A Lighthouse beacon node can be configured to expose an HTTP server by supplying the --http flag. The default listen address is http://127.0.0.1:5052
      - A Lighthouse validator client can be configured to expose a HTTP server by supplying the --http flag. The default listen address is http://127.0.0.1:5062
      - geth HTTP Server : geth --http   default  http://127.0.0.1:8545   http://127.0.0.1:8545/graphql/ui
      - geth WS Server: WebSocket Server geth --ws --ws.port 8546 --ws.api eth,net,web3  default 127.0.0.1:8546
            1. Websocket is a bidirectional transport protocol. A Websocket connection is maintained by client and server until it is explicitly terminated by one. Most modern browsers support Websocket which means it has good tooling.
      - geth IPC Server : IPC Server
            1. IPC is normally available for use in local environments where the node and the console exist on the same machine. Geth creates a pipe in the computers local file system (at ipcpath) that configures a connection between node and console. The geth.ipc file can also be used by other processes on the same machine to interact with Geth.

      - To enable communication with Clef using Curl, --http can be passed which will start an HTTP server on http://localhost:8550 by default.

  2. Metrics API and servers:
      - Lighthouse provides an extensive suite of metrics and monitoring in the Prometheus export format via a HTTP server built into Lighthouse.These metrics are generally consumed by a Prometheus server and displayed via a Grafana dashboard. These components are available in a docker-compose format at sigp/lighthouse-metrics.By default, these metrics are disabled but can be enabled with the --metrics flag. Start a beacon node with the metrics server enabled:ensure that the metrics are available on the default port: http://localhost:5054/metrics
      - Start a validator client with the metrics server enabled:lighthouse vc --metrics ensure that the metrics are available on the default port: curl localhost:5064/metrics
      - Geth: execution node. collects metrics if the --metrics flag is provided at startup. Those metrics are available via an HTTP server if the --metrics.addr flag is also provided. By default the metrics are served at http://127.0.0.1:6060/debug/metrics
          1. a custom IP address can be provided. A custom port can also be provided to the --metrics.port flag. More computationally expensive metrics are toggled on or off by providing or omitting the --metrics.expensive flag. For example, to serve all metrics at the default address and port:
          2.  geth <other commands> --metrics --metrics.addr 127.0.0.1 --metrics.expensive
  4. Between Geth (Execution Node) and Lighthouse (Beacon Node) API:
       - In order to connect to a consensus client, Geth must expose a port for the inter-client RPC connection.The RPC connection must be authenticated using a jwtsecret file. This is created and saved to <datadir>/geth/jwtsecret by default but can also be created and saved to a custom location or it can be self-generated and provided to Geth by passing the file path to --authrpc.jwtsecret. The jwtsecret file is required by both Geth and the consensus client. the following command open the RPC :localhost:8551. 
            1. geth --authrpc.addr localhost --authrpc.port 8551 --authrpc.vhosts localhost --authrpc.jwtsecret /tmp/jwtsecret
            2. The consensus client must be started with the right port configuration to establish an RPC connection to the local Geth instance. In the example above, localhost:8551 was authorized for this purpose. The consensus clients all have a command similar to --http-webprovider that takes the exposed Geth port as an argument.
            3.  start lighthouse beacon node with these : --execution-endpoint http://localhost:8551 --execution-jwt /secrets/jwt.hex

### Using Geth
  - Interacting with Geth requires sending requests to specific JSON-RPC API methods.The RPC requests must be sent to the node and the response returned to the client using some transport protocol.JSON-RPC is provided on multiple transports. Geth supports JSON-RPC over HTTP, WebSocket and Unix Domain Sockets. Transports must be enabled through command-line flags. RPC methods are grouped into several categories depending on their purpose. All method names are composed of the namespace, an underscore, and the actual method name within the namespace. For example, the eth_call method resides in the eth namespace.
  - 
  - Geth is the portal for users to send transactions to Ethereum. The Geth Javascript console is available for this purpose, and the majority of the JSON-RPC API will remain available via web3js or HTTP requests with commands as json payloads. These options are explained in more detail on the Javascript Console page. The Javascript console can be started using the following command in a separate terminal (assuming Geth's IPC file is saved in datadir):

  - solution 1 HTTP :
      1. curl --data '{"jsonrpc":"2.0","method":"eth_getBalance", "params": ["0x9b1d35635cc34752ca54713bb99d38614f63c955", "latest"], "id":2}' -H "Content-Type: application/json" localhost:8545
      2. curl -H "Content-Type: application/json" -X POST localhost:8545 --data '{"method":"eth_call","params":[{"to":"0xebe8efa441b9302a0d7eaecc277c09d20d684540","data":"0x45848dfc"},"latest"],"id":1,"jsonrpc":"2.0"}' 

      3. complex and not easy to understand.
   
  
  -  solution 2 Web3.js.
      1. The purpose of Geth's Javascript console is to provide a built-in environment to use a subset of the Web3.js libraries to interact with a Geth node.
      2. geth attach http://127.0.0.1:8545 or geth attach ipc://home/steven/.ethereum/holesky/geth.ipc
      3. or The second way is to provide the console command when Geth is started up. This starts the node and runs the console in the same terminal. It is therefore convenient to suppress the logs from the node to prevent them from obscuring the console. If the logs are not needed, they can be redirected to the dev/null path, effectively muting them. Alternatively, if the logs are required they can be redirected to a text file. The level of detail provided in the logs can be adjusted by providing a value between 1-6 to the --verbosity flag as in the example below:
    ```
        # to mute logs
        geth <other flags> console 2> /dev/null
        
        # to save logs to file
        geth <other flags> console --verbosity 3 2> geth-logs.log
    ```
    

  - Clef:  Clef is an account management tool. Clef is a tool for signing transactions and data in a secure local environment.it can be started by command: clef init
      1. Clef listens on http.addr:http.port or ipcpath - the same as Geth - and expects messages to be formatted using the JSON-RPC 2.0 standard.
      2. Clef communicates with the process that invoked the binary using stin/stout. The process invoking the binary is usually the native console-based user interface (UI) but there is also an API that enables communication with an external UI. This has to be enabled using --stdio-ui at startup
      3. Most users use Clef by manually approving transactions through the UI as in the schematic above, but it is also possible to configure Clef to sign transactions without always prompting the user. This requires defining the precise conditions under which a transaction will be signed. These conditions are known as Rules and they are small Javascript snippets that are attested by the user by injecting the snippet's hash into Clef's secure whitelist. Clef is then started with the rule file, so that requests that satisfy the conditions in the whitelisted rule files are automatically signed.
      4. clef --keystore /my/keystore --chainid 17000 --http --configdir $HOME/ethnode/clef
      5. Clef can be instructed to run an attested rule file simply by passing the path to rules.js to the --rules flag
      6. For most users, manually confirming every transaction is the way to go. However, there are cases when it makes sense to set up some rules which permit Clef to sign a transaction without prompting the user. The rule file is a tiny JavaScript snippet that you can program however you want: ruleset.js
      7. Of course, Clef isn't going to just accept and run arbitrary scripts you give it, that would be dangerous if someone changes your rule file! Instead, you need to explicitly attest the rule file, which entails injecting its hash into Clef's secure store. Attestations : SHA256 hash into Clef's secure store. The following code snippet shows how to calculate a SHA256 hash for a file named rules.js and pass it to Clef. Note that Clef will prompt the user to provide the master password because the Clef store has to be decrypted in order to add the attestation to it.
         ```
         $ sha256sum rules.js
          645b58e4f945e24d0221714ff29f6aa8e860382ced43490529db1695f5fcc71c  rules.js

          $ clef attest 645b58e4f945e24d0221714ff29f6aa8e860382ced43490529db1695f5fcc71c
          Decrypt master seed of clef
          Password:
            INFO [07-01|13:25:03.290] Ruleset attestation updated
           sha256=645b58e4f945e24d0221714ff29f6aa8e860382ced43490529db1695f5fcc71c
          ```
      8. 


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
