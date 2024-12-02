## Monitoring Stack with Grafana, Prometheus, and Manticore Prometheus Exporter

This project sets up a monitoring stack using **Grafana**, **Prometheus**, and the **Manticore Prometheus Exporter** to monitor ManticoreSearch instances. The stack is orchestrated using **Docker Compose**, and all services are connected on the same custom Docker network for seamless communication.

## Prerequisites

Before getting started, make sure you have the following installed:

-   Docker
-   Docker Compose

## Project Structure

-   **`docker-compose.yml`**: Defines the services (Grafana, Prometheus, Manticore Prometheus Exporter) and their configuration.
-   **`Dockerfile`**: Builds the Manticore Prometheus Exporter container.
-   **`prometheus.yml`**: Prometheus configuration file to scrape metrics from the Manticore Prometheus Exporter.
-   **`manticore.env`**: Environment file with ManticoreSearch instance connection details.

## Setup and Installation

### 1. Clone the Repository

Clone this repository to your local machine:

```bash
git clone https://github.com/elk-utilities/manticore-grafana-monitoring-stack.git
cd manticore-grafana-monitoring-stack

```

### 2. Configure Manticore Exporter

Create the `manticore.env` file in the root directory with the following contents:

```env
MANTICORE_HOST=<your_manticore_host>
MANTICORE_PORT=<your_manticore_port>

```

Replace `<your_manticore_host>` and `<your_manticore_port>` with the appropriate values for your ManticoreSearch instance.

### 3. Build and Start the Containers

Run the following command to build and start the containers:

```bash
docker-compose up --build

```

This will:

-   Build the **Manticore Prometheus Exporter** from the `Dockerfile` found in the [Manticore Prometheus Exporter GitHub repository](https://github.com/manticoresoftware/manticoresearch-prometheus.git).
-   Start the **Grafana**, **Prometheus**, and **Manticore Prometheus Exporter** containers.
-   Set up a custom Docker network (`monitoring_network`) to allow communication between the containers.

### 4. Access the Services

Once the containers are up, you can access the following services:

-   **Grafana**: http://localhost:3000
  -   Default credentials:
    -   Username: `admin`
    -   Password: `admin`
-   **Prometheus**: http://localhost:9090
-   **Manticore Prometheus Exporter**: Runs on port `8081`, but it is primarily accessed by Prometheus to scrape metrics.

### 5. Configure Grafana

1.  Open Grafana in your browser at http://localhost:3000.
2.  Log in with the default credentials.
3.  Add Prometheus as a data source:
    -   Go to **Configuration** (gear icon) > **Data Sources** > **Add data source**.
    -   Choose **Prometheus**.
    -   Set the URL to `http://prometheus:9090`.
    -   Click **Save & Test**.

### 6. Create Grafana Dashboards

You can now create custom dashboards in Grafana to visualize the metrics scraped from your ManticoreSearch instances. You can use the pre-defined Manticore metrics or create your own queries based on your needs.

## Configuration

### Prometheus Configuration (`prometheus.yml`)

The **Prometheus** configuration file is mounted into the container and can be found in the root of the repository (`prometheus.yml`). This file tells Prometheus to scrape metrics from the Manticore Prometheus Exporter.

```yaml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'manticore'
    static_configs:
      - targets: ['manticore_exporter:8081']

```

### Manticore Prometheus Exporter (`Dockerfile`)

The **Manticore Prometheus Exporter** container is built from the `Dockerfile` in this repository. It clones the repository from GitHub, installs dependencies, and starts the exporter on port `8081`. It is configured with the environment variables from the `manticore.env` file to connect to your ManticoreSearch instance.

### Manticore Environment File (`manticore.env`)

The **`manticore.env`** file contains the connection details for your ManticoreSearch instance:

```env
MANTICORE_HOST=<your_manticore_host>
MANTICORE_PORT=<your_manticore_port>

```

Replace the placeholder values with your actual ManticoreSearch host and port. This file is loaded into the Manticore Prometheus Exporter container to establish the connection to your ManticoreSearch instance.

## Stopping and Restarting the Services

To stop the containers, run:

To restart the containers:

```bash
docker-compose up --build

```

## Troubleshooting

-   If Grafana is not showing data, make sure Prometheus is correctly scraping the Manticore Prometheus Exporter metrics at `http://manticore_exporter:8081`.
    
-   Check the logs of any container using:
  ```bash
  docker logs <container_name>
  ```
  
  For example, to check Prometheus logs:
  
  ```bash
  docker logs prometheus
  ```