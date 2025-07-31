What is Observability?
======================
Here we will see the concept of Observability which is a very important aspect for all DevOps Engineers. In DevOps, observability is referred to the software tools and methodologies that help Dev and Ops teams to log, collect, correlate, and analyze massive amounts of performance data from a distributed application and glean real-time insights. Observability is crucial for maintaining the reliability and availability of software systems.   

It can be divided into three main components namely :
· Monitoring
· Logs
· Alerts

**_In general when we are using prometheus and grafana for visualization, this is the flow and requirements_**
--------------------------

<img width="800" height="536" alt="image" src="https://github.com/user-attachments/assets/92665c94-2d07-4248-a01a-f09ab6c65c75" />


What is Grafana ?
====================
Grafana was built on the principle that data should be accessible to everyone in your organization, not just the single Ops person.  

By democratizing data, Grafana helps to facilitate a culture where data can easily be used and accessed by the people that need it, helping to break down data silos and empower teams.  

With Grafana, you can take any of your existing data- be it from your Kubernetes cluster, raspberry pi, different cloud services, or even Google Sheets- and visualize it however you want, all from a single dashboard.

Grafana = Visualization Purpose

What is Prometheus?
===================

Prometheus is an open-source tool for monitoring and alerting systems. It collects and stores metrics over time, and can be used to monitor applications, infrastructure, and more

What is Grafana Loki?
=====================
Loki is a horizontally scalable, highly available, multi-tenant log aggregation solution. It’s designed to be both affordable and simple to use. Rather than indexing the contents of the logs, it uses a set of labels for each log stream.

Loki is Simply Log Aggregation tool
Prometheus is a metrics monitoring tool.
Loki’s design is inspired by Prometheus but for logs.

What is Promtail?
================

Promtail is an agent that ships the logs from the application to Loki. In Simple words, Loki acts as a data source and provides logs to Grafana whereas Promtail captures logs from various sources.


Monitoring
==========

We need monitoring server to monitor all servers and their metrics

There are many like

nagio, kibana, elk, jabbics, Grafana, new-relic, Prometheus , Splunk


PROMETHEUS:
============
Prometheus is an open-source monitoring system that is especially well-suited for cloud-native environments, like Kubernetes.
It can monitor the performance of your applications and services.
it will sends an alert you if there are any issues.
It has a powerful query language that allows you to analyze the data.
It pulls the real-time metrics, compresses and stores in a time-series database. Prometheus is a standalone system, but it can also be used in conjunction with other tools like Alertmanager to send alerts based on the data it collects.
it can be integration with tools like PagerDuty, Teams, Slack, Emails to send alerts to the appropriate on-call personnel.
it collects, and it also has a rich set of integrations with other tools and systems. 

For example, you can use Prometheus to monitor the health of your Kubernetes cluster, and use its integration with Grafana to visualize the data it collects.


SYNOPSIS:
==========

PROMETEUS:
its a free & opensource monitoring tool it collects metrics of nodes
it store metrics on time series database we use PromQL language
we can integrate promethus with tools like pagerduty, slack and email to send notifications PORT: 9090

GRAFANA:
its a visualization tool used to create dashboard.
Datasource is main component (from where you are getting data) Prometheus will show data but cant create dashboards
Dashboards: create, Import
Prometheus will show data but cannot create dashboards
we can integrate Grafana with tools like
pagerduty, slack and email to send notifications
PORT: 3000

username: admin, password: admin

Launch Amazonlinux2 instance call as Promotheus&Grafana Server, from this server we will create K8S CLuster and monitor all those

Setup KOPS cluster first , amazon Linux 2

```
vim .bashrc
```
```
export PATH=$PATH:/usr/local/bin/
```
```
source .bashrc
```

```
vi kops.sh
```
#vim .bashrc  
#export PATH=$PATH:/usr/local/bin/  
#source .bashrc  

```
#! /bin/bash
aws configure
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
wget https://github.com/kubernetes/kops/releases/download/v1.25.0/kops-linux-amd64
chmod +x kops-linux-amd64 kubectl
mv kubectl /usr/local/bin/kubectl
mv kops-linux-amd64 /usr/local/bin/kops

aws s3api create-bucket --bucket reyaz-kops-testbkt123.k8s.local --region ap-south-1 --create-bucket-configuration LocationConstraint=ap-south-1
aws s3api put-bucket-versioning --bucket reyaz-kops-testbkt123.k8s.local --region ap-south-1 --versioning-configuration Status=Enabled
export KOPS_STATE_STORE=s3://reyaz-kops-testbkt123.k8s.local
kops create cluster --name reyaz.k8s.local --zones ap-south-1a --master-count=1 --master-size t2.medium --node-count=2 --node-size t2.micro
kops update cluster --name reyaz.k8s.local --yes --admin
```

run the below commands
```
export KOPS_STATE_STORE=s3://reyaz-kops-testbkt123.k8s.local
kops validate cluster --wait 10m
kops update cluster --name reyaz.k8s.local --yes --admin
kops rolling-update cluster
```


if you want to delete : ``` kops delete cluster --name reyaz.k8s.local --yes ```

<img width="761" height="434" alt="image" src="https://github.com/user-attachments/assets/e51e8bc4-e926-4c37-b073-b84e3328d746" />

## Install Metric Server

```
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/high-availability-1.21+.yaml
```


HELM:
=====

In K8S Helm is a package manager to install packages
In RedHat: yum 
In Ubuntu: apt
In K8S: helm

It is used to install packages on cluster
we can install and deploy applications by using helm
it manages k8s resources packages through charts
chart is a collection of files organized in a directory structure
chat is a collection of manifest files
a Running instance of a chart with a specific config is called Release

Usually in K8S we use manifest file to deploy, but here we will convert manifest to helm chart and deploy



## Install HELM
```
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
helm version
```

### First add helm repositories for prometheus and grafana

```
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts 
helm repo add grafana https://grafana.github.io/helm-charts
```
### UPDATE HELM CHART REPOS:

```
helm repo update
```
```
helm repo list
```
## Steps to Install Prometheus

### CREATE PROMETHEUS NAMESPACE:

```
kubectl get ns
```
```
kubectl create namespace prometheus
```
```
kubectl get ns
```

### INSTALL PROMETHEUS:

```
helm install prometheus prometheus-community/prometheus --namespace prometheus --set alertmanager.persistentVolume.storageClass="gp2" --set server.persistentVolume.storageClass="gp2"
```
```
kubectl get pods -n prometheus
```
```
kubectl get all -n prometheus
```
## Steps to Install Grafana

### Create Namespace Grafana

```
kubectl create namespace grafana
```

### Install Grafana

```
helm install grafana grafana/grafana --namespace grafana --set persistence.storageClassName="gp2" --set persistence.enabled=true --set adminPassword='Root123456' --set service.type=LoadBalancer
```
```
kubectl get pods -n grafana
```
```
kubectl get service -n grafana
```

Copy the ELB url from above command and paste in browser   
username:admin, password = Root123456  

Check SG , nodes and ELB  


Go to Grafana Dashboard --> Add the DataSource --> Select Prometheus and below URL, this URL is same in K8s for all
```
http://prometheus-server.prometheus.svc.cluster.local/
```

--- all dashboard id's are here
```
https://grafana.com/grafana/dashboards/
```
   

Import Grafana dashboard --> New --> Import --> 6417 --> load --> select Prometheus --> import   

New --> Import --> 315 --> load --> select Prometheus --> import   

Add 1860 port to monitor Nodes individually  

15661  --> K8S cluster dashboard.
11454 --> pv and pvc  
747 --> pod metrics  
14623 --> k8s overview -- use this   
10907 --> monitor api-server  


---  to start grafana service or see in script  
```
systemctl start grafana-server.service
```





NODEXPORTER: will help to collect the data from workernodes

install NodeExporter
============
```
vi nodeexp.sh
```
```
#NODEEXPORTER
wget https://github.com/prometheus/node_exporter/releases/download/v1.5.0/node_exporter-1.5.0.linux-amd64.tar.gz
tar -xf node_exporter-1.5.0.linux-amd64.tar.gz
sudo mv node_exporter-1.5.0.linux-amd64/node_exporter  /usr/local/bin
rm -rv node_exporter-1.5.0.linux-amd64*
sudo useradd -rs /bin/false node_exporter

sudo cat <<EOF | sudo tee /etc/systemd/system/node_exporter.service
[Unit]
Description=Node Exporter
After=network.target

[Service]
User=node_exporter
Group=node_exporter
Type=simple
ExecStart=/usr/local/bin/node_exporter

[Install]
WantedBy=multi-user.target
EOF

sudo cat /etc/systemd/system/node_exporter.service
sudo systemctl daemon-reload  && sudo systemctl enable node_exporter
sudo systemctl start node_exporter.service && sudo systemctl status node_exporter.service --no-pager
```


```
sh nodeexp.sh
```
```
http://ip:9100 -- to see if nodeexporter is working
```
