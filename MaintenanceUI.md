# Maintenance and UI

### Lighthouse UI Siren
  - Siren is a user interface built for Lighthouse that connects to a Lighthouse Beacon Node and a Lighthouse Validator Client to monitor performance and display key validator metrics.
  - docker run --rm -ti --name siren -p 80:80 sigmaprime/siren
  - This will open port 80 and allow your browser to connect. You can choose another local port by modifying the command. For example -p 8000:80 will open port 8000. To view Siren, simply go to http://localhost in your web browser.
  - add --gui --http --http-allow-origin "*" to the lighthouse Beacon Node
  - add --http --http-allow-origin "*" to the lighthouse Validator Node

### Metrics monitoring
  - Metrics, monitoring, and alerting are the key elements of a monitoring system. Metrics are the input, the raw data needed for monitoring performance, health, and availability. Monitoring is what alerting is built on top of. Together, they provide insight into how your applications and infrastructure are performing. They detect performance or usage anomalies, help you troubleshoot and identify issues, reveal usage and behavior patterns or trends, and help you understand the impact of any changes you make to your applications or infrastructure. Whenever metrics meet conditions you have defined as part of alert rules, monitoring solutions send notifications prompting you to investigate issues and even help you narrow down and identify possible root causes.
  - 
