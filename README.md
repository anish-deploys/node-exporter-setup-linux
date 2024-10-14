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
