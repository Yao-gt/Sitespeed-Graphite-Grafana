# Sitespeed Graphite Grafana

A Docker-based monitoring and visualization stack for [Sitespeed.io](https://www.sitespeed.io/) performance testing results, combining Graphite for time-series data storage and Grafana for dashboard visualization.

## 📋 Overview

This project provides a containerized solution for:
- **Sitespeed.io**: Captures and processes web performance metrics
- **Graphite**: Stores and manages time-series performance data
- **Grafana**: Creates rich, interactive dashboards for metrics visualization

### Features

- ✅ **Real-time Performance Monitoring**: Track website performance metrics over time
- ✅ **Time-Series Data Storage**: Efficient data storage with Graphite
- ✅ **Beautiful Dashboards**: Create custom dashboards with Grafana
- ✅ **Docker Containerized**: Easy deployment with Docker Compose
- ✅ **Pre-configured Integrations**: Datasources and dashboards pre-configured
- ✅ **Customizable Security**: Configurable authentication and anonymous access

## 🏗️ Architecture

The stack consists of three main services:

### 1. Grafana (Port 3000)
- **Version**: 12.2.0
- **Image**: `grafana/grafana:12.2.0`
- **Purpose**: Data visualization and dashboard management

### 2. Graphite (Ports 2003, 8080)
- **Version**: 1.1.10-3
- **Image**: `sitespeedio/graphite:1.1.10-3`
- **Purpose**: Time-series metrics storage and retrieval
- **Port 2003**: Carbon protocol for metric ingestion
- **Port 8080**: Web interface

### 3. Results Server (Port 8888)
- **Image**: `nginx:alpine`
- **Purpose**: Serves Sitespeed.io HTML performance reports
- **Volume**: `./sitespeed-result`

## 📁 Project Structure

```
Sitespeed-Graphite-Grafana/ 
├── docker-compose.yml # Main Docker Compose configuration 
├── grafana/ 
│ └──provisioning/ 
│     └── dashboards/ # Pre-built dashboard definitions 
├── graphite/ 
│ ├── conf/ 
│ │ ├── storage-schemas.conf # Data retention policies 
│ │ ├── storage-aggregation.conf # Data aggregation rules 
│ │ └── carbon.conf # Carbon daemon configuration 
│ └── graphite.db # Graphite database 
└── sitespeed-result/ # Sitespeed.io HTML reports storage
```

## 🔧 Prerequisites

- Docker
- Docker Compose
- At least 2GB available disk space

## 🚀 Quick Start

### 1. Clone the Repository

```bash
git clone https://github.com/Yao-gt/Sitespeed-Graphite-Grafana.git
cd Sitespeed-Graphite-Grafana
```

### 2. Start the Stack

```
docker-compose up -d
```

### 3. Access the Services

- Grafana: http://localhost:3000
- Graphite Web UI: http://localhost:8080
- Sitespeed Results: http://localhost:8888

### 4. Configure Graphite as a datasource in Grafana

- Open Grafana (http://localhost:3000)
- Log in with default credentials
- Navigate to Configuration → Data Sources
- Click "Add data source"
- Select "Graphite"
- Set URL to http://graphite:8080
- Click "Save & Test"

## Example of Grafana dashboard

Sitespeed.io metris in Graphite
![k6 Dashboard](https://github.com/user-attachments/assets/fa7ead16-0c3b-42e3-ade5-51153d25f49c)

## 📋 Configuration

### Grafana Environment Variables

The following environment variables are set in the docker-compose.yml:

|Variable| Value| Description
|------|---------|---------|
|**GF_SECURITY_ADMIN_PASSWORD **| admin	| Admin user password |
|**GF_SECURITY_ADMIN_USER **| admin | Admin username |
|**GF_AUTH_ANONYMOUS_ENABLED **| true | Allow anonymous access |
|**GF_USERS_ALLOW_SIGN_UP **| false | Disable user registration |
|**GF_USERS_ALLOW_ORG_CREATE **| false | Disable org creation |
|**GF_INSTALL_PLUGINS **| See compose | Auto-install plugins |

### Graphite Configuration

Configuration files are located in graphite/conf/:

- storage-schemas.conf: Defines retention and aggregation policies for different metrics
- storage-aggregation.conf: Specifies how to aggregate data when moving between time buckets
- carbon.conf: Carbon daemon settings

## Volumes

The stack uses the following Docker volumes for data persistence:

- grafana-data: Grafana configuration and dashboard storage
- whisper-data: Graphite time-series data storage
- ./graphite/graphite.db: Graphite metadata database

## Network

All services communicate through a custom Docker network: sitespeed-graphite-grafana_network

## Usage with Sitespeed.io

To send Sitespeed.io metrics to this Graphite instance, configure Sitespeed.io in Docker to emit metrics to the Graphite service:

```
docker run --rm --network sitespeed-graphite-grafana_network -v "$(pwd):/sitespeed.io" sitespeedio/sitespeed.io:latest https://www.sitespeed.io -n 1 --outputFolder sitespeed-result/www_sitespeed_io/actual --graphite.host graphite
```

## Stopping the Stack

```
docker-compose down
```

To also remove volumes (**warning**: deletes all stored data):

```
docker-compose down -v
```

## 📚 Related Resources

- [Sitespeed.io](https://www.sitespeed.io/)
- [Graphite](https://graphite.readthedocs.io/en/stable/)
- [Grafana](https://grafana.com/docs/grafana/latest/)