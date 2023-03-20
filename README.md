# SwarmWatch
The monitoring and alerting project for Docker Swarm using Alertmanager, cAdvisor, Prometheus, Grafana, and Node Exporter is designed to provide real-time visibility and insights into the performance of Docker containers running on a Swarm cluster.

**Prerequisites**
Before installing the Prometheus stack, make sure you have the latest version of Docker and Docker Swarm installed on your Docker host machine. Docker Swarm is installed automatically when using Docker for Mac or Docker for Windows.

**Installation and Configuration**

Clone the project locally to your Docker host.
If you would like to change which targets should be monitored or make configuration changes, edit the */prometheus/prometheus.yml file*. The targets section defines what should be monitored by Prometheus. The names defined in this file are sourced from the service name in the docker-compose.yml file. If you wish to change the names of the services, you can add the "container_name" parameter in the docker-compose.yml file.
From the /prometheus project directory, run the following command: `HOSTNAME=$(hostname) docker stack deploy -c docker-stack.yml prom`

The *docker stack deploy* command deploys the entire Grafana and Prometheus stack automatically to the Docker Swarm. *By default, cAdvisor and node-exporter are set to Global deployment, which means they will propagate to every Docker host attached to the Swarm.*

The Grafana Dashboard is now accessible via: `http://<Host IP Address>:3000` (e.g., http://192.168.10.1:3000). The username is admin, and the password is foobar (stored in the /grafana/config.monitoring env file).

To check the status of the newly created stack: $ `docker stack ps prom`

View running services: $ `docker service ls`

View logs for a specific service: $ ```docker service logs prom_<service_name>```

**Add Datasources and Dashboards**

Grafana version 5.0.0 has introduced the concept of provisioning, which allows you to automate the process of adding Datasources & Dashboards. The */grafana/provisioning/* directory contains the datasources and dashboards directories, which contain YAML files that allow you to specify which datasource or dashboards should be installed.

If you would like to automate the installation of additional dashboards, copy the Dashboard JSON file to */grafana/provisioning/dashboards*, and it will be provisioned the next time you stop and start Grafana.

*Alternatively*, you can install a Dashboard template available on the Grafana Docker Dashboard. Simply select Import from the Grafana menu -> Dashboards -> Import and provide the Dashboard ID #179.

**Alerting**

Alerting has been added to the stack with Slack integration. Two alerts have been added and are managed:

*Alerts - prometheus/alert.rules Slack configuration - alertmanager/config.yml*

**The Slack configuration requires you to build a custom integration. Here's how you can set it up:**

Open your Slack team in your browser: https://<your-slack-team>.slack.com/apps
Click "build" in the upper right corner.
Choose "Incoming Web Hooks" link under Send Messages.
Click on the "incoming webhook integration" link.
Select which channel.
Click on "Add Incoming WebHooks integration."
Copy the Webhook URL into the alertmanager/config.yml URL section.
Fill in Slack username and channel.
View Prometheus alerts: http://<Host IP Address>:9090/alerts
View Alert Manager: http://<Host IP Address>:9093
A quick test for your alerts is to stop a service. Stop the node_exporter container, and you should notice the alert arrive in Slack. Also, check