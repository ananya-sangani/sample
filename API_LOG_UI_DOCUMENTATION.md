# Observability Dashboard

**Technology Stack:** Python, FastAPI, Kubernetes, SQLite, Alertz API  
**Purpose:** Automated observability gap analysis and log management for microservices

A comprehensive observability platform that automates the analysis of Kubernetes pod logs, identifies missing monitoring coverage, and generates detailed reports for DevOps teams. The system addresses critical gaps in microservice monitoring by providing automated log collection, API mapping, metrics emission validation, and alert coverage analysis.

## 🎯 Purpose

This observability solution enhances monitoring and alerting by identifying gaps in the current system. It provides programmatic access to Kubernetes clusters to enable intelligent mapping between codebase components and metrics emitted by services, helping find gaps in system observability.

## ✨ Key Features

### 🔍 Automated Codebase-Metrics Mapping
- **Intelligent API Discovery**: Automatically maps codebase components to their corresponding metrics
- **Request Log Analysis**: Analyzes load balancer request logs to identify APIs being used
- **Metrics Validation**: Verifies whether metrics are being emitted correctly for discovered APIs
- **Hystrix Command Mapping**: Maps Hystrix circuit breaker commands to API endpoints

### 📊 Comprehensive Observability Analysis
- **Gap Identification**: Proactively identifies missing metrics and alerting gaps
- **Performance Insights**: Provides intelligent insights on service performance and health
- **Alert Coverage Analysis**: Evaluates existing alert coverage against actual API usage
- **Volume-Based Prioritization**: Prioritizes gaps based on API call volume and business impact

### 🚀 Automated Pipelines
- **Automated Alert Pipeline**: Generates and validates alert configurations
- **Automated Mapping Pipeline**: Creates comprehensive codebase-to-metrics mappings
- **Log Pooling Service**: Continuous log collection and analysis from Kubernetes pods
- **JMX Diagnostics**: Automated JMX metrics collection and analysis

### 📈 Reporting & Visualization
- **Interactive Web Dashboard**: FastAPI-based web interface for analysis and reporting
- **Comprehensive Reports**: Detailed observability gap analysis reports
- **Email Notifications**: Automated email reports with metrics and gap analysis
- **Real-time Monitoring**: Live pod log analysis and metrics collection

## 🏗️ Technical Architecture

### Core Components

1. **Web Application (main.py)**
   - FastAPI-based web interface
   - Real-time log analysis
   - Interactive dashboards
   - Report generation

2. **Log Processing Engine (parser.py)**
   - Intelligent log parsing
   - API endpoint extraction
   - Pattern recognition
   - Data normalization

3. **Log Pooling Service (log-pooling-service/)**
   - Continuous log collection
   - Historical data storage
   - Automated cleanup
   - Query interface

4. **Alert Management System**
   - Alertz API integration
   - Alert-to-API mapping
   - Coverage analysis
   - Automated reporting

5. **Dynamic Configuration System**
   - API-driven team discovery
   - Service mapping automation
   - Smart caching
   - Graceful degradation

## 🛠️ Technology Stack

- **Backend**: Python 3.8+, FastAPI
- **Frontend**: HTML, Jinja2 Templates, JavaScript
- **Container Orchestration**: Kubernetes
- **Database**: SQLite (for local storage)
- **Monitoring**: JMX, Prometheus-compatible metrics
- **Alerting**: Alertz API integration
- **Log Analysis**: Custom parsing and analysis engines
- **Email**: SMTP integration for automated reporting

## 📋 Prerequisites

### Required Software
1. **Python Environment**
   - Python 3.8+ installed
   - pip package manager
   - Virtual environment support

2. **Kubernetes Access**
   - kubectl configured with cluster access
   - Appropriate RBAC permissions
   - Access to target namespaces

3. **Network Connectivity**
   - Access to Kubernetes API server
   - Access to Alertz API endpoints
   - Access to JMX endpoints 

## 🚀 Installation & Setup

### Step 1: Environment Setup

```bash
# Clone repository
git clone <repository-url>
cd observability-dashboard

# Create virtual environment
python3 -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Install additional dependencies for Hystrix mapping (optional)
pip install -r hystrix_mapper_requirements.txt
```

### Step 2: Configuration Setup

Create a `.env` file in the project root:

```bash
# Copy example environment file
cp .env.example .env

# Edit configuration
nano .env
```

**Required Environment Variables:**

```env
# Kubernetes Configuration
KUBECONFIG_PATH=/path/to/your/kubeconfig
DEFAULT_NAMESPACE=your-default-namespace
KUBERNETES_CONTEXT=your-cluster-context

# Alertz API Configuration
ALERTZ_BASE_URL=https://your-alertz-instance.com/fk-alert-service
ALERTZ_V2_BASE_URL=https://your-alertz-instance.com/fk-alert-service/v2
ALERTZ_API_KEY=your_api_key_here
ALERTZ_TIMEOUT=30
ALERTZ_RETRY_ATTEMPTS=3
ALERTZ_CACHE_DURATION_HOURS=24

# Database Configuration
LOG_DB_PATH=logs_pool.db
DB_RETENTION_DAYS=30

# Service Configuration
DEFAULT_SERVICE_NAME=your-service-name
LOG_LEVEL=INFO
MAX_LOG_FILES=50
METRICS_COLLECTION_INTERVAL=300

# Email Configuration (optional)
SMTP_SERVER=smtp.gmail.com
SMTP_PORT=587
SMTP_USERNAME=your-email@gmail.com
SMTP_PASSWORD=your-app-password

# Discovery Settings
TEAM_DISCOVERY_ENABLED=true
SERVICE_DISCOVERY_ENABLED=true
AUTO_DISCOVERY_ENABLED=true
```

### Step 3: Service Configuration

Edit `config.py` to customize service-specific settings:

```python
# Example service configuration
SERVICE_CONFIGS = {
    'your-service': {
        'name': 'Your Service Name',
        'description': 'Service description',
        'alert_severity': 'SEV0',
        'critical_exception_threshold': 1000,
        'critical_timeout_threshold': 100
    }
}

# Volume thresholds for API classification
VOLUME_THRESHOLDS = {
    'high_volume': 100000,    # APIs with >100k calls
    'medium_volume': 10000,   # APIs with >10k calls
    'low_volume': 1000        # APIs with >1k calls
}
```

### Step 4: Pod Configuration

Configure target pods in `log-pooling-service/manual_pod_config.py`:

```python
# Example pod configuration
TARGET_PODS = [
    {
        "name": "your-pod-name-1234567890-abcde",
        "namespace": "your-namespace"
    }
]

# Or enable automatic pod discovery
AUTO_DISCOVERY_ENABLED = True
POD_DISCOVERY_PATTERNS = [
    "your-service-*",
    "*-your-service-*"
]
```

### Step 5: Verification

```bash
# Test installation
python main.py --help

# Verify kubectl access
kubectl get pods -n <namespace>

# Test dynamic configuration
python test_dynamic_config.py

# Verify Alertz API connectivity
curl ${ALERTZ_BASE_URL}/teams
```

## 🎮 Usage Guide

### Starting the Application

#### Web Interface
```bash
# Start the main application
python main.py

# Or use uvicorn directly
uvicorn main:app --host 0.0.0.0 --port 8000 --reload

# Access web interface
# Open browser: http://localhost:8000
```

#### Log Pooling Service
```bash
# Start continuous log collection
python log-pooling-service/start_pooling_service.py

# Or use the launcher
python launch_pooling_service.py

# For manual pod discovery
python log-pooling-service/discover_pods.py
```

### Core Workflows

#### 1. Log Analysis Workflow
1. **Access Web Interface**
   - Navigate to http://localhost:8000
   - Select "Pod Log Analysis"

2. **Configure Analysis**
   - Enter pod name and namespace
   - Choose time range (hours back) or line count
   - Select analysis type (real-time or historical)

3. **Review Results**
   - View parsed API endpoints and usage patterns
   - Analyze client IP distributions
   - Export results in preferred format

#### 2. Observability Report Generation
1. **Navigate to Report Section**
   - Select "Observability Report"
   - Enter pod details and configuration

2. **System Analysis**
   - System automatically analyzes logs, metrics, and alerts
   - Generates comprehensive gap analysis
   - Creates priority-based recommendations

3. **Review and Export**
   - Download detailed report
   - Share with stakeholders
   - Use for planning monitoring improvements

#### 3. Alert Management Workflow
```bash
# Fetch alerts for specific teams (automatically discovered)
python fetch_alerts_dump.py

# Generate detailed alert information
python fetch_alert_details.py

# Run complete automated pipeline
python automated_alert_pipeline.py

# Use intelligent alert mapper
python intelligent_alert_mapper.py
```

#### 4. Historical Data Analysis
1. **Access Pooled Logs**
   - Navigate to "Pooled Logs" section
   - Configure query parameters

2. **Query Historical Data**
   - Filter by time range, pod, or log level
   - Search for specific patterns
   - Export historical data

3. **Trend Analysis**
   - Review usage patterns over time
   - Identify performance trends
   - Generate capacity planning insights


#### Dynamic Configuration
The system automatically discovers teams and services from Alertz API:

```python
from dynamic_config_manager import config_manager

# Discover all teams
teams = config_manager.discover_all_teams()

# Discover services
services = config_manager.discover_services()

# Get team display name
team_name = config_manager.get_team_display_name("team_id")

# Get service display name
service_name = config_manager.get_service_display_name("app_id")
```

#### Hystrix Mapping
```bash
# Run Hystrix mapper
python run_hystrix_mapper.py --service your-service --namespace your-namespace

# Configure Hystrix mapping
# Edit hystrix_mapper_config.yaml
```

#### JMX Diagnostics
```bash
# Collect JMX metrics
python jmx_diagnostic.py --pod pod-name --namespace namespace

# Debug JMX timeout issues
python debug_jmx_timeout.py --pod pod-name
```

## 📊 Dashboard Features

### Main Analysis Interface
- **Log Upload**: Upload log files for analysis
- **Pod Analysis**: Direct Kubernetes pod log analysis
- **Request Log Analysis**: Load balancer request log processing
- **Metrics Collection**: JMX metrics gathering and analysis

### Reports and Visualizations
- **API Usage Analysis**: Endpoint usage patterns and volume
- **Gap Analysis**: Missing metrics and alert coverage
- **Performance Metrics**: Response times, error rates, throughput
- **Alert Mapping**: API-to-alert correlation analysis

### Export Options
- **TXT Reports**: Detailed text-based analysis reports
- **JSON Exports**: Structured data for further processing
- **Email Reports**: Automated email notifications
- **HTML Reports**: Interactive web-based reports

## 🔧 API Endpoints

### Core Analysis Endpoints
- `POST /analyze` - Main log analysis endpoint
- `POST /metrics_report` - Generate metrics reports
- `POST /group_by_api_and_ip_aggregated` - Group API calls by IP
- `GET /view_latest_report` - View latest analysis report

### Utility Endpoints
- `GET /log_details/{method}/{endpoint}/{client_ip}` - Detailed log information
- `POST /export_txt` - Export analysis as text file
- `GET /debug_report` - Debug information endpoint

## 📈 Analysis Capabilities

### Log Analysis
- **IP Address Extraction**: Identify client IP addresses
- **API Endpoint Mapping**: Map HTTP requests to endpoints
- **Volume Analysis**: Calculate API call volumes
- **Error Pattern Detection**: Identify error patterns and trends

### Metrics Analysis
- **JMX Metrics Collection**: Gather Java application metrics
- **Hystrix Command Analysis**: Circuit breaker metrics
- **Thread Pool Monitoring**: Thread pool utilization
- **Performance Metrics**: Response times and throughput

### Gap Analysis
- **Missing Metrics**: APIs without corresponding metrics
- **Alert Coverage**: APIs without alert configurations
- **Volume-Based Prioritization**: Prioritize gaps by impact
- **Recommendations**: Suggested monitoring improvements

## 🚨 Alert Integration

### Alertz API Integration
- **Alert Discovery**: Find existing alerts for APIs
- **Coverage Analysis**: Identify APIs without alerts
- **Alert Mapping**: Map alerts to API endpoints
- **Gap Reporting**: Report missing alert coverage

### Alert Generation
- **Automated Alert Creation**: Generate alert configurations
- **Threshold Optimization**: Optimize alert thresholds
- **Severity Classification**: Classify alerts by severity
- **Integration Testing**: Test alert configurations

## 📝 Report Types

### Observability Reports
- **Comprehensive Gap Analysis**: Complete observability assessment
- **API Usage Reports**: Detailed API usage patterns
- **Performance Reports**: Service performance analysis
- **Alert Coverage Reports**: Alert coverage assessment

### Executive Summaries
- **High-Level Overview**: Executive-level insights
- **Critical Gaps**: Priority gaps requiring attention
- **Recommendations**: Actionable improvement suggestions
- **ROI Analysis**: Business impact assessment

## 🔍 Troubleshooting & Support

### Common Issues

#### 1. Kubernetes Access Issues
**Problem:** kubectl access denied or namespace not found  
**Solutions:**
```bash
# Verify kubeconfig file path and permissions
kubectl cluster-info
kubectl get pods --all-namespaces

# Check RBAC permissions
kubectl auth can-i get pods --namespace <namespace>
```

#### 2. Log Analysis Problems
**Problem:** Empty or incorrect analysis results  
**Solutions:**
```bash
# Verify log format matches expected patterns
head -n 10 /path/to/logs/example.log

# Check file permissions and access
ls -la /path/to/logs

# Enable debug logging
export LOG_LEVEL=DEBUG
```

#### 3. Alert API Errors
**Problem:** Failed to fetch alerts from Alertz API  
**Solutions:**
```bash
# Verify API key and endpoint URLs
curl -H "Authorization: Bearer $ALERTZ_API_KEY" $ALERTZ_BASE_URL/teams

# Check network connectivity
ping your-alertz-instance.com

# Validate team configurations
python test_dynamic_config.py
```

#### 4. Performance Issues
**Problem:** Slow analysis or high resource usage  
**Solutions:**
```bash
# Reduce log file size for analysis
# Use filtering options to limit data
# Increase system resources if needed

# Monitor resource usage
top -p $(pgrep -f "python.*main.py")
```

### Performance Optimization

#### System Tuning
- **Memory Management**: Monitor Python memory usage
- **Database Optimization**: Regular SQLite maintenance
- **Log Rotation**: Implement automatic log cleanup
- **Caching**: Enable result caching for repeated queries

#### Scalability Considerations
- **Horizontal Scaling**: Deploy multiple instances
- **Load Balancing**: Distribute analysis workload
- **Database Scaling**: Consider migration to PostgreSQL for large datasets
- **Resource Monitoring**: Track CPU, memory, and disk usage


## 🔧 Configuration Management

### Dynamic Configuration System
The system uses a dynamic configuration system that automatically discovers teams and services from Alertz API:

```python
# Automatic team discovery
teams = config_manager.discover_all_teams()

# Automatic service discovery
services = config_manager.discover_services()

# Smart caching (24-hour cache)
config_manager.refresh_cache()  # Force refresh if needed
```

### Service Configuration
```python
# config.py - Service-specific settings
SERVICE_CONFIGS = {
    'your-service': {
        'name': 'Your Service Name',
        'description': 'Service description',
        'alert_severity': 'SEV0',
        'critical_exception_threshold': 1000,
        'critical_timeout_threshold': 100
    }
}
```

### Alert Configuration
The system automatically discovers alert configurations from Alertz API:

```python
# Dynamic alert team discovery
teams = config_manager.discover_all_teams()

# Dynamic service mapping
service_mappings = config_manager.get_service_mappings()
```


---


