# Observability Dashboard

**Technology Stack:** Python, FastAPI, Kubernetes, SQLite, Alertz API  
**Purpose:** Automated observability gap analysis and log management for microservices

## Overview

The Observability Dashboard automates the analysis of Kubernetes pod logs to identify monitoring gaps in microservice environments. It provides programmatic access to Kubernetes clusters for mapping codebase components to emitted metrics, enabling systematic identification of observability coverage issues.

## Core Functionality

### Log Analysis
- Parse Kubernetes pod logs to extract API endpoints and usage patterns
- Analyze load balancer request logs for API discovery
- Map HTTP requests to service endpoints
- Calculate API call volumes and client IP distributions

### Metrics Validation
- Collect JMX metrics from Java applications
- Validate metrics emission for discovered APIs
- Map Hystrix circuit breaker commands to endpoints
- Monitor thread pool utilization and performance metrics

### Alert Coverage Analysis
- Integrate with Alertz API for alert discovery
- Map alerts to API endpoints
- Identify APIs without alert configurations
- Generate coverage reports with priority recommendations

### Automated Reporting
- Generate comprehensive observability gap analysis
- Export reports in multiple formats (TXT, JSON, HTML)
- Provide volume-based prioritization of gaps
- Support email notifications for automated reporting

## Architecture

### Components

1. **Web Application** (`main.py`)
   - FastAPI-based interface for log analysis and reporting
   - Real-time pod log analysis capabilities
   - Interactive dashboard for results visualization

2. **Log Processing Engine** (`parser.py`)
   - Parse and normalize log data
   - Extract API endpoints and patterns
   - Handle multiple log formats

3. **Log Pooling Service** (`log-pooling-service/`)
   - Continuous log collection from Kubernetes pods
   - Historical data storage in SQLite
   - Automated data retention and cleanup

4. **Alert Management** (`fetch_alerts_dump.py`, `fetch_alert_details.py`)
   - Alertz API integration for alert discovery
   - Alert-to-API mapping and coverage analysis
   - Automated alert pipeline execution

5. **Configuration Management** (`dynamic_config_manager.py`)
   - Dynamic team and service discovery from Alertz API
   - Smart caching with configurable retention
   - Graceful degradation for API failures

## Prerequisites

### System Requirements
- Python 3.8+
- kubectl with cluster access
- Network access to Kubernetes API and Alertz endpoints
- 4GB RAM minimum (8GB recommended)
- 10GB disk space

### Required Permissions
- Kubernetes RBAC permissions for pod log access
- Alertz API key with appropriate scope
- Namespace access for target services

## Installation

### 1. Environment Setup

```bash
git clone <repository-url>
cd observability-dashboard
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

### 2. Configuration

Create `.env` file from `.env.example`:

```env
# Kubernetes Configuration
KUBECONFIG_PATH=/path/to/kubeconfig
DEFAULT_NAMESPACE=your-namespace
KUBERNETES_CONTEXT=your-cluster

# Alertz API Configuration
ALERTZ_BASE_URL=https://your-alertz-instance.com/fk-alert-service
ALERTZ_V2_BASE_URL=https://your-alertz-instance.com/fk-alert-service/v2
ALERTZ_API_KEY=your_api_key

# Database Configuration
LOG_DB_PATH=logs_pool.db
DB_RETENTION_DAYS=30

# Service Configuration
DEFAULT_SERVICE_NAME=your-service
LOG_LEVEL=INFO
```

### 3. Service Configuration

Edit `config.py` for service-specific settings:

```python
SERVICE_CONFIGS = {
    'your-service': {
        'name': 'Your Service Name',
        'alert_severity': 'SEV0',
        'critical_exception_threshold': 1000,
        'critical_timeout_threshold': 100
    }
}

VOLUME_THRESHOLDS = {
    'high_volume': 100000,
    'medium_volume': 10000,
    'low_volume': 1000
}
```

### 4. Pod Configuration

Configure target pods in `log-pooling-service/manual_pod_config.py`:

```python
TARGET_PODS = [
    {
        "name": "your-pod-name-1234567890-abcde",
        "namespace": "your-namespace"
    }
]

AUTO_DISCOVERY_ENABLED = True
POD_DISCOVERY_PATTERNS = ["your-service-*"]
```

## Usage

### Starting the Application

```bash
# Web interface
python main.py
# Access at http://localhost:8000

# Log pooling service
python log-pooling-service/start_pooling_service.py
```

### Core Workflows

#### Log Analysis
1. Access web interface at http://localhost:8000
2. Select "Pod Log Analysis"
3. Enter pod name, namespace, and analysis parameters
4. Review parsed API endpoints and usage patterns
5. Export results as needed

#### Observability Report Generation
1. Navigate to "Observability Report" section
2. Enter pod details and configuration
3. System analyzes logs, metrics, and alerts
4. Download comprehensive gap analysis report

#### Alert Management
```bash
# Fetch alerts for teams
python fetch_alerts_dump.py

# Generate detailed alert analysis
python fetch_alert_details.py

# Run complete pipeline
python automated_alert_pipeline.py
```

#### Historical Data Analysis
1. Access "Pooled Logs" section
2. Configure query parameters (time range, pod, log level)
3. Search for specific patterns
4. Export historical data for trend analysis

### Advanced Features

#### Dynamic Configuration
```python
from dynamic_config_manager import config_manager

teams = config_manager.discover_all_teams()
services = config_manager.discover_services()
team_name = config_manager.get_team_display_name("team_id")
```

#### Hystrix Mapping
```bash
python run_hystrix_mapper.py --service your-service --namespace your-namespace
```

#### JMX Diagnostics
```bash
python jmx_diagnostic.py --pod pod-name --namespace namespace
```

## API Endpoints

### Core Endpoints
- `POST /analyze` - Main log analysis
- `POST /metrics_report` - Generate metrics reports
- `POST /group_by_api_and_ip_aggregated` - Group API calls by IP
- `GET /view_latest_report` - View latest analysis

### Utility Endpoints
- `GET /log_details/{method}/{endpoint}/{client_ip}` - Detailed log info
- `POST /export_txt` - Export analysis as text
- `GET /debug_report` - Debug information

## Troubleshooting

### Common Issues

#### Kubernetes Access
```bash
# Verify access
kubectl cluster-info
kubectl get pods --all-namespaces
kubectl auth can-i get pods --namespace <namespace>
```

#### Log Analysis Issues
```bash
# Check log format
head -n 10 /path/to/logs/example.log

# Enable debug logging
export LOG_LEVEL=DEBUG
```

#### Alert API Errors
```bash
# Verify API connectivity
curl -H "Authorization: Bearer $ALERTZ_API_KEY" $ALERTZ_BASE_URL/teams

# Test configuration
python test_dynamic_config.py
```

### Performance Optimization
- Monitor Python memory usage
- Implement log rotation and cleanup
- Enable result caching for repeated queries
- Consider PostgreSQL for large datasets

## Project Structure

```
observability-dashboard/
├── main.py                           # Web application
├── parser.py                         # Log parsing engine
├── config.py                         # Configuration
├── requirements.txt                  # Dependencies
├── templates/                        # Web templates
├── log-pooling-service/              # Log collection
├── fetch_alerts_dump.py             # Alert collection
├── fetch_alert_details.py           # Alert analysis
├── automated_alert_pipeline.py      # Automated pipeline
├── dynamic_config_manager.py        # Configuration management
├── jmx_diagnostic.py                # JMX metrics
├── run_hystrix_mapper.py            # Hystrix mapping
└── logs_pool.db                     # Historical data
```
