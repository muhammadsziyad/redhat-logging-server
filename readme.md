# Centralized Logging Server Setup on RHEL using ELK Stack

## Objective

The objective of this project is to set up a centralized logging server on Red Hat Enterprise Linux (RHEL) 
using the ELK Stack (Elasticsearch, Logstash, and Kibana). This setup will aggregate logs from various 
sources, process them, and provide a web interface for visualization and analysis.

```mermaid
graph LR;
    A[Client Application] --> B[Logstash Input]
    B --> C[Processing & Filtering]
    C --> D[Elasticsearch Storage]
    D --> E[Kibana Visualization]
    E --> F[Analyze Logs]
```

## Project Overview

1.  **Planning and Preparation**
    
    -   Define requirements and prepare the RHEL environment.
    -   Install Elasticsearch, Logstash, and Kibana.
    -   Configure Logstash to collect and process logs.
2.  **Install and Configure Elasticsearch**
    
    -   Install Elasticsearch.
    -   Configure Elasticsearch settings.
3.  **Install and Configure Logstash**
    
    -   Install Logstash.
    -   Configure Logstash to collect, parse, and send logs to Elasticsearch.
4.  **Install and Configure Kibana**
    
    -   Install Kibana.
    -   Configure Kibana to visualize data from Elasticsearch.
5.  **Testing and Validation**
    
    -   Verify log collection and processing.
    -   Test data visualization in Kibana.
6.  **Documentation and Conclusion**
    
    -   Document the setup process.
    -   Evaluate benefits, challenges, and performance.

## Step-by-Step Configuration

### 1. Planning and Preparation

-   **Install RHEL**: Ensure RHEL is installed and updated on the server node.
-   **Update System**:
    
    `sudo yum update -y` 
    

### 2. Install and Configure Elasticsearch

#### On the Centralized Logging Server

-   **Download and Install Elasticsearch**:

    
    `sudo rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch
    sudo vi /etc/yum.repos.d/elasticsearch.repo` 
    
    Add the following repository configuration:
    
    
    ```
    [elasticsearch-7.x]
    name=Elasticsearch repository for 7.x packages
    baseurl=https://artifacts.elastic.co/packages/7.x/yum/
    gpgcheck=1
    gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
    enabled=1
    autorefresh=1
    type=rpm-md
    ``` 
    
    Install Elasticsearch:

    
    `sudo yum install elasticsearch -y` 
    
-   **Start and Enable Elasticsearch Service**:

    
    `sudo systemctl start elasticsearch
    sudo systemctl enable elasticsearch` 
    
-   **Configure Elasticsearch**: Edit `/etc/elasticsearch/elasticsearch.yml` to configure network settings 
(e.g., set `network.host` to `0.0.0.0` for external access).
    
-   **Verify Installation**:

    
    `curl -X GET "localhost:9200/"` 
    

### 3. Install and Configure Logstash

#### On the Centralized Logging Server

-   **Download and Install Logstash**:

    
    `sudo vi /etc/yum.repos.d/logstash.repo` 
    
    Add the following repository configuration:

    
    ```[logstash-7.x]
    name=Elastic repository for 7.x packages
    baseurl=https://artifacts.elastic.co/packages/7.x/yum/
    gpgcheck=1
    gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
    enabled=1
    autorefresh=1
    type=rpm-md``` 
    
    Install Logstash:

    `sudo yum install logstash -y` 
    
-   **Configure Logstash**: Create a Logstash configuration file in `/etc/logstash/conf.d/` (e.g., 
`logstash.conf`):

    
    ```input {
      beats {
        port => 5044
      }
    }
    filter {
      # Add any required filters here
    }
    output {
      elasticsearch {
        hosts => ["localhost:9200"]
        index => "logs-%{+YYYY.MM.dd}"
      }
    }``` 
    
-   **Start and Enable Logstash Service**:

    
    `sudo systemctl start logstash
    sudo systemctl enable logstash` 
    

### 4. Install and Configure Kibana

#### On the Centralized Logging Server

-   **Download and Install Kibana**:

    
    `sudo vi /etc/yum.repos.d/kibana.repo` 
    
    Add the following repository configuration:

    
    ```[kibana-7.x]
    name=Kibana repository for 7.x packages
    baseurl=https://artifacts.elastic.co/packages/7.x/yum/
    gpgcheck=1
    gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
    enabled=1
    autorefresh=1
    type=rpm-md``` 
    
    Install Kibana:

    
    `sudo yum install kibana -y` 
    
-   **Start and Enable Kibana Service**:

    
    `sudo systemctl start kibana
    sudo systemctl enable kibana` 
    
-   **Configure Kibana**: Edit `/etc/kibana/kibana.yml` to configure the Kibana settings (e.g., set 
`server.host` to `0.0.0.0` for external access).
    
-   **Verify Installation**: Open a web browser and navigate to `http://<your-server-ip>:5601`.
    

### 5. Testing and Validation

-   **Verify Log Collection**:
    -   Send sample logs to Logstash and check if they are indexed in Elasticsearch.
-   **Verify Data Visualization**:
    -   Access Kibana and create visualizations and dashboards to view the logs.

### 6. Documentation and Conclusion

-   **Document Configuration**: Record all installation and configuration steps, including Elasticsearch, 
Logstash, and Kibana settings.
-   **Evaluate Benefits**:
    -   **Centralized Logging**: Aggregate and manage logs from multiple sources in one place.
    -   **Data Visualization**: Use Kibana for powerful log analysis and visualization.
-   **Challenges**:
    -   **Configuration Complexity**: Properly configuring and tuning ELK stack components.
    -   **Resource Usage**: Elasticsearch and Kibana can be resource-intensive.
