# Zabbix Docker Compose

A complete Zabbix monitoring solution using Docker Compose with Traefik reverse proxy, supporting both Zabbix 6.x and 7.x versions.

## Features

- **Zabbix Server**: Core monitoring engine
- **Zabbix UI**: Web interface for monitoring and administration
- **MariaDB Database**: Persistent data storage
- **Traefik Router**: Reverse proxy with automatic SSL certificates
- **Adminer (PMA)**: Database administration interface
- **Multi-version Support**: Easy switching between Zabbix 6.x and 7.x

## Requirements

### Database Requirements
- **MariaDB 11.1** (for Zabbix 7.x)
- **MariaDB 10.0** (for Zabbix 6.x)

## Configuration

### Environment Variables (.env file)

The most important configuration is the `ZABBIX_BASE` variable, which determines the Zabbix version and is propagated to all services (UI, Server, DB).

#### Essential Variables

```bash
# Zabbix Version Configuration (CRITICAL)
ZABBIX_BASE=maborak/zabbix-base:7.4.1    # For Zabbix 7.4.1
# ZABBIX_BASE=maborak/zabbix-base:7.0.1  # For Zabbix 7.0.1
# ZABBIX_BASE=maborak/zabbix-base:6.4.1  # For Zabbix 6.4.1
# ZABBIX_BASE=maborak/zabbix-base:6.0.1  # For Zabbix 6.0.1

# Project Configuration
COMPOSE_PROJECT_NAME=zabbix
BIND_IP=0.0.0.0

# Domain Configuration
DOMAIN_UI=zabbix.yourdomain.com
DOMAIN_ROUTER=router.yourdomain.com
DOMAIN_PMA=pma.yourdomain.com
CONTEXT=zabbixmonitor

# SSL Certificate Configuration
CLOUDFLARE_EMAIL=your-email@domain.com
CLOUDFLARE_DNS_API_TOKEN=your-cloudflare-api-token
ACME_URL=https://acme-v02.api.letsencrypt.org/directory
ACME_HTTP_CHALLENGE=false
```

#### Version Switching

To switch between Zabbix versions:

1. Update the `ZABBIX_BASE` variable in your `.env` file
2. Rebuild the containers:
   ```bash
   docker-compose build --no-cache
   docker-compose up -d
   ```

## Quick Start

1. **Clone the repository**:
   ```bash
   git clone <repository-url>
   cd zabbix-docker-compose
   ```

2. **Configure your environment**:
   ```bash
   cp .env.example .env
   # Edit .env with your settings, especially ZABBIX_BASE
   ```

3. **Start the services**:
   ```bash
   docker-compose up -d
   ```

4. **Access the web interface**:
   - **URL**: `http://your-domain-ui` or `https://your-domain-ui`
   - **Username**: `Admin`
   - **Password**: `zabbix`

## Services

### Zabbix Server
- **Purpose**: Core monitoring engine
- **Configuration**: Uses `ZABBIX_BASE` from .env
- **Port**: Internal only (proxied through Traefik)

### Zabbix UI
- **Purpose**: Web interface for monitoring
- **Configuration**: Uses `ZABBIX_BASE` from .env
- **Access**: Through Traefik reverse proxy
- **Default Credentials**: Admin/zabbix

### MariaDB Database
- **Purpose**: Persistent data storage
- **Configuration**: Uses `ZABBIX_BASE` from .env
- **Root Password**: zabbix_root
- **Database**: zabbix
- **User**: zabbix
- **Password**: zabbix

### Traefik Router
- **Purpose**: Reverse proxy with SSL termination
- **Features**: Automatic SSL certificates via Let's Encrypt
- **Ports**: 80 (HTTP), 443 (HTTPS)

### Adminer (PMA)
- **Purpose**: Database administration interface
- **Access**: Through Traefik reverse proxy

## Default Zabbix Server and Agent2 Configuration

The Zabbix server and agent2 configurations are located in the `conf/` directory. Both services run in the same container managed by supervisord.

### Zabbix Server Configuration (`conf/zabbix_server.conf`)

```bash
LogType=console                    # Log to console instead of file
ListenIP=0.0.0.0                  # Listen on all interfaces
ListenPort=9997                    # Server listening port
DebugLevel=3                       # Debug level (0-5, 3=info)
DBHost=db                          # Database host (Docker service name)
DBName=zabbix                      # Database name
DBUser=zabbix                      # Database user
DBPassword=zabbix                  # Database password
StartPollers=10                    # Number of poller processes
StartPreprocessors=10              # Number of preprocessor processes
StartPollersUnreachable=10         # Number of unreachable poller processes
StartPingers=10                    # Number of pinger processes
StartDiscoverers=10                # Number of discovery processes
StartHTTPPollers=10                # Number of HTTP poller processes
StartTimers=10                     # Number of timer processes
StartAlerters=10                   # Number of alerter processes
CacheSize=500M                     # Cache size for data
StartDBSyncers=4                   # Number of database sync processes
HistoryCacheSize=160M              # History cache size
HistoryIndexCacheSize=114M         # History index cache size
TrendCacheSize=114M                # Trend cache size
Timeout=4                          # Timeout for operations
LogSlowQueries=3000                # Log queries slower than 3 seconds
StartLLDProcessors=10              # Number of LLD processor processes
StatsAllowedIP=127.0.0.1          # IP allowed to access statistics
StartReportWriters=2               # Number of report writer processes
WebServiceURL=http://zabbix_web_service:9998/report  # Web service URL
```

### Zabbix Agent2 Configuration (`conf/zabbix_agent2.conf`)

```bash
# Server to connect to
Server=127.0.0.1                   # Passive mode server IP
ServerActive=127.0.0.1             # Active mode server IP

# Hostname for this agent
Hostname=zabbix-server             # Agent hostname

# Listen port
ListenPort=9998                    # Agent listening port

# Log file
LogFile=/var/log/zabbix/zabbix_agent2.log

# Log level (0-5)
LogLevel=3                         # Info level logging

# Enable persistent buffer
EnablePersistentBuffer=1           # Enable persistent buffer

# Buffer size
BufferSize=100                     # Buffer size for data

# Buffer flush interval
BufferFlushInterval=60             # Flush buffer every 60 seconds

# Allow key
AllowKey=system.run[*]             # Allow system.run commands

# Deny key
DenyKey=system.run[*]              # Deny system.run commands (conflicts with AllowKey)

# Timeout
Timeout=3                          # Operation timeout

# Include directory
Include=/var/lib/zabbix/etc/zabbix_agent2.d/*.conf

# User parameter file
UserParameterFile=/var/lib/zabbix/etc/zabbix_agent2.d/*.conf
```

### Key Configuration Points:

#### **Server Configuration Highlights:**
- **Database Connection**: Uses Docker service name `db` for database host
- **Performance Tuning**: Optimized with 10 processes for most components
- **Caching**: Large cache sizes (500M main cache, 160M history cache)
- **Network**: Listens on all interfaces (0.0.0.0) on port 9997
- **Logging**: Console logging for Docker container visibility

#### **Agent2 Configuration Highlights:**
- **Dual Mode**: Supports both passive (Server) and active (ServerActive) modes
- **Buffering**: Persistent buffer enabled for data reliability
- **Security**: Mixed allow/deny keys (needs clarification)
- **Monitoring**: Self-monitoring on port 9998
- **User Parameters**: Support for custom monitoring scripts

#### **Service Management:**
- **Supervisord**: Manages both server and agent2 processes
- **Auto-restart**: Services automatically restart on failure
- **Logging**: Separate log files for each service
- **User**: Runs as zabbix user (UID 1997)

#### **Port Usage:**
- **Server**: Port 9997 (listening)
- **Agent2**: Port 9998 (listening)
- **Agent2 Active**: Connects to server on port 9997

#### **Configuration Issues to Note:**
1. **Conflicting Keys**: Both `AllowKey=system.run[*]` and `DenyKey=system.run[*]` are set - this needs clarification
2. **Security**: `StatsAllowedIP=127.0.0.1` restricts statistics to localhost only
3. **Database**: Uses default credentials (zabbix/zabbix) - should be secured in production

## Important Notes

- **ZABBIX_BASE Variable**: This is the most critical configuration. It determines the Zabbix version and is used by all services (UI, Server, DB).
- **Default Credentials**: The web interface uses Admin/zabbix by default.
- **Database**: MariaDB is configured with native password authentication.
- **SSL**: Traefik automatically handles SSL certificates via Let's Encrypt.
- **Version Compatibility**: Ensure database version matches Zabbix version requirements.

## Troubleshooting

### Version Switching Issues
If you encounter issues when switching versions:
```bash
# Clean rebuild
docker-compose down
docker-compose build --no-cache
docker-compose up -d
```

### Database Issues
If database connection fails:
- Verify MariaDB version compatibility with Zabbix version
- Check database credentials in docker-compose.yml
- Ensure database container is running: `docker-compose ps`

### SSL Issues
If SSL certificates fail:
- Verify domain configuration in .env
- Check Cloudflare API token permissions
- Ensure DNS records point to your server

## File Structure

```
zabbix-docker-compose/
├── docker-compose.yml    # Main orchestration file
├── .env                  # Environment configuration
├── .env.example         # Example environment file
├── conf/                # Configuration files
│   ├── zabbix_server.conf    # Zabbix server configuration
│   ├── zabbix_agent2.conf   # Zabbix agent2 configuration
│   ├── zabbix.conf.php      # Zabbix UI configuration
│   └── php_extra.ini        # PHP configuration
├── build/               # Docker build contexts
│   ├── zabbix-server/   # Server build context
│   ├── zabbix-ui/       # UI build context
│   └── zabbix-db/       # Database build context
├── volumes/             # Persistent data volumes
└── tmp/                 # Temporary files
```


