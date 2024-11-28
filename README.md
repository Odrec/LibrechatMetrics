# LibreChat Metrics

This script is designed to collect and expose various metrics from the LibreChat application for monitoring and analysis using Prometheus.

## Overview

The script connects to the MongoDB database used by LibreChat, aggregates relevant data, and exposes the metrics on an HTTP server that Prometheus can scrape.

## Features

- Collects metrics such as:
  - **Unique users per day**
  - **Average and standard deviation of users per day**
  - **Average and standard deviation of messages per user**
  - **Messages per model per day**
  - **Average and standard deviation of messages per model per day**
- Exposes metrics on port `8000` for Prometheus to scrape
- Designed to run continuously, collecting metrics at regular intervals

## Prerequisites

- **Python 3.6** or higher
- Access to the **LibreChat MongoDB** database
- **Prometheus** installed and configured to scrape the metrics endpoint

## Setup Instructions

### 1. Clone the Repository

Clone the repository containing the script:

```bash
git clone https://github.com/yourusername/librechat-metrics.git

cd librechat-metrics
```
### 2. Install Dependencies

Install the required Python packages using pip:

```pip install -r requirements.txt```

### 3. Configure the Environment Variables

If you want to change the default database connection string `mongodb://mongodb:27017/` or the default port for Prometheus to scrape `8000`, you can create a `.env` file and set up the mongo database connection string from your environment:

```
MONGODB_URI=mongodb://mongodb:27017/
PROMETHEUS_PORT=8000
```

### 4. Run the Script

Start the metrics collection script:

```python metrics.py```
The script will start an HTTP server on port 8000 to expose the metrics.
### 5. Configure Prometheus

Add the following job to your Prometheus configuration file (prometheus.yml):scrape_configs:
```
  - job_name: 'librechat_metrics'
    scrape_interval: 60s
    static_configs:
      - targets: ['localhost:8000']  # Update if the script runs on a different host or port
```
Reload Prometheus to apply the new configuration.


## Docker Deployment (Optional)

A Dockerfile is included for containerized deployment.

If you want to run the script inside the mongodb librechat container, you can add something like this to the docker compose:

```yaml
metrics:
  build:
    context: ./metrics
  networks:
    - librechat
  depends_on:
    - mongodb
  ports:
    - "8000:8000"  # Expose port for Prometheus
  restart: unless-stopped
  volumes:
    - ./metrics/.env:/app/.env  # Mount the .env file
```

Make sure the networks attribute is the same as your mongodb container.