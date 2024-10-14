# Node Exporter Setup Linux

## What is Node Exporter ?

<ul>
  <li>Node exporter is one of the Prometheus exporters which is used to expose servers or system OS metrics.</li>
  <li>With the help of Node exporter we can expose various resources of the system like RAM, CPU utilization, Memory Utilization, disk space.</li>
  <li>Node exporter runs as a system service which gathers the metrics of your system and that gathered metrics is displayed with the help of Grafana visualization tool.</li>
</ul>

## Node Exporter Architecture 

![node-img-1-1024x487-1](https://github.com/user-attachments/assets/73b02b13-38ee-4534-9885-a33a43c027b7)

## Step #1 : Creating Node Exporter Systemd service

### Create a node_exporter service file in the /etc/systemd/system directory

    sudo nano /etc/systemd/system/node_exporter.service

Enter this code and Ctrl + S to save and Ctrl + X to exit

    [Unit]
    Description=Node Exporter
    Wants=network-online.target
    After=network-online.target
    [Service]
    Type=simple
    User=node_exporter
    Group=node_exporter
    ExecStart=/usr/local/bin/node_exporter \
            --collector.mountstats \
            --collector.logind \
            --collector.processes \
            --collector.ntp
            --collector.systemd \
            --collector.tcpstat \
            --collector.wifi \
    Restart=always
    RestartSec=10s
    [Install]
    WantedBy=multi-user.target


Now lets start and enable the node_exporter service using below commands

    sudo systemctl daemon-reload
    sudo systemctl start node_exporter
    sudo systemctl enable node_exporter
    sudo systemctl status node_exporter

## Step #2 : Configure the Node Exporter as a Prometheus target

### Now to scrape the node_exporter lets instruct the Prometheus by making a minor change in prometheus.yml file

So go to etc/prometheus and open prometheus.yml

    sudo nano /etc/prometheus/prometheus.yml

Enter this code and Ctrl + S to save and Ctrl + X to exit

    # my global config
    global:
      scrape_interval: 15s # Set the scrape interval to every 15 seconds. Default is every 1 minute.
      evaluation_interval: 15s # Evaluate rules every 15 seconds. The default is every 1 minute.
      # scrape_timeout is set to the global default (10s).

    # Alertmanager configuration
    alerting:
      alertmanagers:
        - static_configs:
            - targets:
              # - alertmanager:9093

    # Load rules once and periodically evaluate them according to the global 'evaluation_interval'.
    rule_files:
      # - "first_rules.yml"
      # - "second_rules.yml"

    # A scrape configuration containing exactly one endpoint to scrape:
    # Here it's Prometheus itself.
    scrape_configs:
      # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
      - job_name: "prometheus"

        # metrics_path defaults to '/metrics'
        # scheme defaults to 'http'.

        static_configs:
          - targets: ["localhost:9090","localhost:9100"]

Now restart the Prometheus Service

    sudo systemctl restart prometheus

Hit the URL in your web browser to check weather our target is successfully scraped by Prometheus or not

![node-exporter-target-1024x391](https://github.com/user-attachments/assets/05429d16-222e-4c6e-aea3-477f888779ea)

