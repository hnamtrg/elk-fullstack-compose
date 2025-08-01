# ELK Stack Full Monitoring Solution with Docker Compose

A comprehensive Elastic Stack (ELK) deployment using Docker Compose, featuring Elasticsearch, Kibana, Logstash, and all Elastic Beats for complete infrastructure monitoring and log analysis.

## 📋 Table of Contents

- [Overview](#overview)
- [Architecture](#architecture)
- [Features](#features)
- [Prerequisites](#prerequisites)
- [Installation and Setup](#installation-and-setup)
- [Configuration](#configuration)
- [Usage](#usage)
- [Data Ingestion](#data-ingestion)
- [Accessing the Services](#accessing-the-services)
- [Troubleshooting](#troubleshooting)
- [Security Considerations](#security-considerations)

## 🔍 Overview

This project provides a complete ELK Stack deployment with the following components:

- **Elasticsearch**: Distributed search and analytics engine
- **Kibana**: Data visualization and exploration platform
- **Logstash**: Data processing pipeline for ingesting and transforming data
- **Metricbeat**: System and service metrics collection
- **Filebeat**: Log files shipping and processing
- **Heartbeat**: Uptime monitoring for services and websites
- **Packetbeat**: Network packet analysis
- **Auditbeat**: File integrity monitoring and auditing

## 🏗️ Architecture

The stack is deployed using Docker Compose with the following services:

```
┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│   Kibana    │    │Elasticsearch│    │  Logstash   │
│   :5601     │◄──►│    :9200    │◄──►│   :9600     │
└─────────────┘    └─────────────┘    └─────────────┘
                          ▲                   ▲
                          │                   │
┌─────────────────────────┴───────────────────┴────┐
│                 Beats                            │
│  ┌───────────┐ ┌───────────┐ ┌───────────┐       │
│  │Metricbeat │ │ Filebeat  │ │Heartbeat  │       │
│  └───────────┘ └───────────┘ └───────────┘       │
│  ┌───────────┐ ┌───────────┐                     │
│  │Packetbeat │ │Auditbeat  │                     │
│  └───────────┘ └───────────┘                     │
└──────────────────────────────────────────────────┘
```

## ✨ Features

- **Full SSL/TLS encryption** with automatic certificate generation
- **Authentication and authorization** with Elastic Security
- **Multi-beat monitoring** for comprehensive infrastructure visibility
- **Sample air quality data** for demonstration purposes
- **Persistent data storage** with Docker volumes
- **Health checks** for all services
- **Configurable resource limits** for different environments

## 📋 Prerequisites

### System Requirements for VMware Ubuntu Server 22.04

#### Minimum Requirements:
- **CPU**: 4 cores (8 cores recommended for production)
- **RAM**: 4 GB (Recommended)
- **Storage**: 30 GB free space (50 GB recommended)
- **Network**: Internet connection for downloading Docker images

#### Software Requirements:
- Ubuntu Server 22.04 LTS
- Docker Engine 20.10 or later
- Docker Compose 2.0 or later
- Git (for cloning the repository)

### Installing Prerequisites on Ubuntu Server 22.04

#### 1. Update System Packages
```bash
sudo apt update && sudo apt upgrade -y
```

#### 2. Install Docker Engine
```bash
# Install required packages
sudo apt install -y apt-transport-https ca-certificates curl gnupg lsb-release

# Add Docker's official GPG key
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

# Add Docker repository
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Install Docker
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io

# Add your user to docker group
sudo usermod -aG docker $USER
newgrp docker
```

#### 3. Install Docker Compose
```bash
# Install Docker Compose
sudo apt install -y docker-compose-plugin

# Verify installation
docker compose version
```

#### 4. Install Git
```bash
sudo apt install -y git
```

## 🚀 Installation and Setup

### 1. Clone the Repository
```bash
git clone <repository-url>
cd elk-fullstack-compose
```

### 2. Configure Environment Variables
The `.env` file contains all necessary configuration. Key variables include:

```bash
# Project namespace
COMPOSE_PROJECT_NAME=elastic-server

# Authentication
ELASTIC_PASSWORD=elastic          # Change in production
KIBANA_PASSWORD=kibana           # Change in production

# Stack version
STACK_VERSION=8.7.1

# Cluster configuration
CLUSTER_NAME=docker-cluster
LICENSE=basic

# Port mappings
ES_PORT=9200
KIBANA_PORT=5601

# Memory limits (adjust based on your system)
ES_MEM_LIMIT=1073741824         # 1GB
KB_MEM_LIMIT=1073741824         # 1GB
LS_MEM_LIMIT=1073741824         # 1GB

# Encryption key (generate new one for production)
ENCRYPTION_KEY=c34d38b3a14956121ff2170e5030b471551370178f43e5626eec58b04a30fae2
```

### 3. Adjust Memory Limits (if needed)
For systems with limited RAM, reduce memory limits in `.env`:
```bash
ES_MEM_LIMIT=536870912          # 512MB
KB_MEM_LIMIT=536870912          # 512MB
LS_MEM_LIMIT=536870912          # 512MB
```

### 4. Start the ELK Stack
```bash
# Start all services
docker compose up -d

# Check service status
docker compose ps

# View logs
docker compose logs -f
```

### 5. Verify Installation
```bash
# Check Elasticsearch health
curl -k -u elastic:elastic https://localhost:9200/_cluster/health

# Check Kibana (wait for it to be ready)
curl -k -I https://localhost:5601
```

## ⚙️ Configuration

### Service Configurations

#### Elasticsearch (`docker-compose.yml`)
- **Security**: SSL/TLS enabled with automatic certificate generation
- **Authentication**: Built-in users with configurable passwords
- **Memory**: Configurable heap size via environment variables
- **Storage**: Persistent volume for data retention

#### Kibana
- **Access**: Web interface on port 5601 (HTTPS)
- **Authentication**: Integrated with Elasticsearch security
- **SSL**: Enabled with generated certificates

#### Logstash (`logstash.conf`)
- **Input**: Monitors CSV files in `/logstash_ingest_data/`
- **Processing**: Direct pass-through (filters can be added)
- **Output**: Sends to Elasticsearch with daily indices

#### Beats Configuration

**Metricbeat** (`metricbeat.yml`):
- Monitors Elasticsearch, Logstash, Kibana, and Docker metrics
- Collects system performance data

**Filebeat** (`filebeat.yml`):
- Monitors log files in `/filebeat_ingest_data/`
- Docker container log collection

**Heartbeat** (`heartbeat.yml`):
- Monitors Kibana service availability
- HTTP health checks every 10 seconds

**Packetbeat** (`packetbeat.yml`):
- Network traffic analysis
- Monitors HTTP, TCP, and TLS protocols

**Auditbeat** (`auditbeat.yml`):
- File integrity monitoring
- Monitors `/home/auditbeat` directory

## 🎯 Usage

### Accessing Services

#### Kibana Web Interface
1. Open browser and navigate to: `https://localhost:5601`
2. Login credentials (if the Credentials in .env is changed):
   - Username: `elastic` (or value from `.env`)
   - Password: `elastic` (or value from `.env`)
3. Accept SSL certificate warnings (self-signed certificates)

#### Elasticsearch API
```bash
# Cluster health
curl -k -u elastic:elastic https://localhost:9200/_cluster/health

# List indices
curl -k -u elastic:elastic https://localhost:9200/_cat/indices?v

# Search data
curl -k -u elastic:elastic https://localhost:9200/_search?pretty
```

### Creating Kibana Dashboards

1. **Access Kibana**: Navigate to `https://localhost:5601`
2. **Create Index Patterns**:
   - Go to Stack Management → Index Patterns
   - Create patterns for: `logstash-*`, `filebeat-*`, `metricbeat-*`
3. **Explore Data**: Use Discover tab to explore ingested data
4. **Create Visualizations**: Build charts and graphs in Visualize tab
5. **Build Dashboards**: Combine visualizations in Dashboard tab

## 📊 Data Ingestion

### Sample Data Included

The project includes sample air quality data:

- **Logstash Data**: `logstash_ingest_data/Air_Quality.csv` (16,124 records)
- **Filebeat Data**: `filebeat_ingest_data/Air_Quality.log` (16,124 records)

Data includes:
- Air quality measurements (Ozone, SO2, PM2.5)
- Geographic data (NYC boroughs and districts)
- Time series data (2005-2014)
- Health impact metrics

### Adding Your Own Data

#### For Logstash:
1. Place CSV files in `logstash_ingest_data/` directory
2. Restart Logstash: `docker compose restart logstash01`
3. Check completion: `cat logstash_ingest_data/logstash_completed.log`

#### For Filebeat:
1. Place log files in `filebeat_ingest_data/` directory
2. Filebeat automatically detects and processes new files
3. View ingested data in Kibana under `filebeat-*` index

## 🔧 Management Commands

### Docker Compose Operations
```bash
# Start all services
docker compose up -d

# Stop all services
docker compose down

# Restart specific service
docker compose restart <service-name>

# View logs
docker compose logs -f <service-name>

# Scale services (if needed)
docker compose up -d --scale metricbeat01=2
```

### Data Management
```bash
# Backup Elasticsearch data
docker run --rm -v elastic-server_esdata01:/data -v $(pwd):/backup alpine tar czf /backup/elasticsearch-backup.tar.gz /data

# Clean up volumes (WARNING: Deletes all data)
docker compose down -v
```

### Monitoring
```bash
# Check service health
docker compose ps

# Monitor resource usage
docker stats

# Check Elasticsearch cluster health
curl -k -u elastic:elastic https://localhost:9200/_cluster/health?pretty
```
### Log Analysis
```bash
# Check specific service logs
docker compose logs elasticsearch
docker compose logs kibana
docker compose logs logstash01

# Follow logs in real-time
docker compose logs -f --tail=50 elasticsearch
```

## 🔐 Security Considerations

#### For Production Use, Implement:
- 🔒 **Strong passwords**: Change default passwords in `.env`
- 🔒 **Certificate management**: Use proper SSL certificates
- 🔒 **Network security**: Implement firewall rules
- 🔒 **Access control**: Set up proper user roles and permissions
- 🔒 **Monitoring**: Implement security monitoring and alerting
- 🔒 **Backup**: Regular data backups and disaster recovery
- 🔒 **Updates**: Regular security updates and patches

### Default Credentials
```
Elasticsearch/Kibana:
- Username: elastic
- Password: elastic

Kibana System User:
- Username: kibana_system  
- Password: kibana
```

**⚠️ IMPORTANT**: Change these passwords & encryption key before production deployment!

## 📚 Additional Resources

- [Elastic Stack Documentation](https://www.elastic.co/guide/index.html)
- [Docker Compose Documentation](https://docs.docker.com/compose/)
- [Elasticsearch Reference](https://www.elastic.co/guide/en/elasticsearch/reference/current/index.html)
- [Kibana User Guide](https://www.elastic.co/guide/en/kibana/current/index.html)
- [Beats Documentation](https://www.elastic.co/guide/en/beats/libbeat/current/index.html)

## 📝 Author

- Hoai Nam Truong, Jr. DevOps Engineer - KMS Technology.
- Reference: Eddie Mitchell, elkninja

## 📝 License

This project is for educational and demonstration purposes. Please refer to Elastic's license terms for production use.
