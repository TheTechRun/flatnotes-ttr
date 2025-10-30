# Docker Installation Guide

This guide covers production deployment of flatnotes with subdirectory support using Docker Compose.

## Quick Start

### Prerequisites

- Docker and Docker Compose installed
- Git client
- Sufficient disk space for notes and index

### Installation Steps

1. **Clone the repository**:
   ```bash
   git clone https://github.com/TheTechRun/flatnotes-ttr.git
   cd flatnotes-ttr
   ```

2. **Configure environment**:
   ```bash
   cp .env.example .env
   # Edit .env with your settings
   ```

3. **Start the application**:
   ```bash
   docker-compose up -d
   ```

## Environment Configuration

### Environment File (.env)

Create a `.env` file from the provided example:

```bash
# Flatnotes Authentication Configuration
FLATNOTES_AUTH_TYPE=password
FLATNOTES_USERNAME=your_username
FLATNOTES_PASSWORD=your_secure_password
FLATNOTES_SECRET_KEY=your_secret_key_here
```

**Security Notes**:
- Change default username and password
- Use a strong, unique secret key
- Keep `.env` file secure and never commit to version control

### Docker Compose Configuration

**File: `docker-compose.yaml`**
```yaml
services:
  flatnotes:
    container_name: flatnotes-ttr
    build:
      context: https://github.com/TheTechRun/flatnotes-ttr.git#develop
      dockerfile: Dockerfile.fork
    env_file:
      - .env
    environment:
      PUID: 5000 # change this to yours
      PGID: 5000 # change this to yours
      FLATNOTES_PATH: "/data"
      FLATNOTES_PORT: 8080
    volumes:
      - "./notes:/data"
      - "./index:/data/.flatnotes"
    ports:
      - "8053:8080"
    restart: unless-stopped
```

**Configuration Details**:
- **Build Context**: Builds from `develop` branch on GitHub
- **Container Name**: `flatnotes-ttr`
- **Host Port**: `8053` (change if needed)
- **Data Volume**: `./notes` - stores your markdown files
- **Index Volume**: `./index` - stores search database
- **Restart Policy**: `unless-stopped` for automatic recovery

## Data Persistence

### Volume Structure

After first run, you'll see this structure:

```
flatnotes-ttr/
├── notes/                    # Your actual notes
│   ├── Linux/
│   │   └── how-to-get-docker.md
│   ├── Development/
│   │   └── python/
│   │       └── virtualenv-guide.md
│   └── standalone-note.md
└── index/                    # Search index database
    └── .flatnotes/
        ├── _MAIN_0.toc
        ├── _MAIN_0 seg_001.dat
        └── _MAIN_0 seg_001.tran
```

### Backup Strategy

**Manual Backup**:
```bash
# Create backup archive
tar -czf flatnotes-backup-$(date +%Y%m%d-%H%M%S).tar.gz notes/ index/

# List backup files
ls -lh flatnotes-backup-*.tar.gz
```

**Automated Backup Script**:
```bash
#!/usr/bin/env bash

# backup-flatnotes.sh

BACKUP_DIR="/path/to/your/backups"
DATE=$(date +%Y%m%d-%H%M%S)
CONTAINER_NAME="flatnotes-ttr"

# Create backup directory if it doesn't exist
mkdir -p "$BACKUP_DIR"

# Backup data from container
docker exec $CONTAINER_NAME tar -czf - /data /data/.flatnotes | gzip > "$BACKUP_DIR/flatnotes-$DATE.tar.gz"

echo "Backup created: $BACKUP_DIR/flatnotes-$DATE.tar.gz"
```

**Restore from Backup**:
```bash
# Stop container
docker-compose down

# Remove existing data (optional)
rm -rf notes/ index/

# Restore from backup
tar -xzf flatnotes-backup-20241030-150000.tar.gz

# Restart container
docker-compose up -d
```

## Access and Usage

### First Time Setup

1. **Access the application**: http://localhost:8053
2. **Log in** with credentials from `.env` file
3. **Create a test note** to verify functionality:
   - Title: `test-note`
   - Content: `# Test Note\n\nThis is a test.`
4. **Create a subdirectory note**:
   - Title: `Linux/docker-setup`
   - Content: `# Docker Setup\n\nInstallation instructions...`

### Using Subdirectory Support

With subdirectory support, you can organize notes hierarchically:

**Examples**:
- `Linux/networking/tcp-ip-basics`
- `Development/python/virtualenv-guide`
- `Projects/website-rewrite/requirements`
- `Documentation/api/authentication-methods`

**Directory Creation**:
- Subdirectories are created automatically
- No manual directory setup required
- Mixed organization (root + subdirectories) supported

## Operations and Maintenance

### Container Management

**View Logs**:
```bash
# Real-time logs
docker-compose logs -f flatnotes

# Recent logs
docker-compose logs --tail=50 flatnotes
```

**Update to Latest Version**:
```bash
# Pull latest changes and rebuild
docker-compose pull
docker-compose up --build -d
```

**Stop and Start**:
```bash
# Stop the application
docker-compose down

# Start the application
docker-compose up -d
```

### Performance Monitoring

**Resource Usage**:
```bash
# Container resource usage
docker stats flatnotes

# Disk usage
du -sh notes/ index/

# Container size
docker images flatnotes-flatnotes
```

## Troubleshooting

### Common Issues

**Port Already in Use**:
```yaml
# Change port mapping in docker-compose.yaml
ports:
  - "8054:8080"  # Use 8054 instead of 8053
```

**Permission Denied Errors**:
```bash
# Fix volume permissions
sudo chown -R $USER:$USER notes/ index/
chmod 755 notes/ index/
```

**Container Won't Start**:
```bash
# Check container logs
docker-compose logs flatnotes

# Check for port conflicts
netstat -tulpn | grep 8053

# Rebuild container
docker-compose up --build -d --force-recreate
```

**Lost Data Recovery**:
```bash
# Check if volumes exist
docker volume ls | grep flatnotes

# Inspect container volume mounts
docker inspect flatnotes-ttr | grep Mounts

# Recover from backup (see Backup section)
```

### Health Checks

**Verify Application is Running**:
```bash
# Check container status
docker-compose ps

# Check if web interface responds
curl -f http://localhost:8053/health

# Expected response: "OK"
```

**Verify Subdirectory Feature**:
```bash
# Test API with subdirectory note
curl -X POST http://localhost:8053/api/notes \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -d '{"title": "test/subdir-note", "content": "# Test\n\nContent"}'

# Expected: Success response with note details
```

## Security Considerations

### Network Security

- **Firewall**: Only expose necessary ports (8053)
- **Reverse Proxy**: Consider using nginx/traefik for production
- **HTTPS**: Use SSL termination in reverse proxy

### Data Security

- **Regular Backups**: Schedule automated backups
- **Access Control**: Use strong authentication credentials
- **Volume Security**: Ensure proper file permissions
- **Secret Management**: Rotate FLATNOTES_SECRET_KEY regularly

### Container Security

```bash
# Run as non-root user (already configured)
# Container runs as user 5000:5000

# Minimal base image
# Uses official Python slim image

# Read-only filesystem where possible
# Only data directories are writable
```

## Production Checklist

### Before Going Live

- [ ] **Environment configured** with strong credentials
- [ ] **Backup strategy** implemented and tested
- [ ] **SSL/HTTPS** configured (if using reverse proxy)
- [ ] **Firewall rules** properly configured
- [ ] **Monitoring** set up (logs, metrics)
- [ ] **Disaster recovery** plan documented
- [ ] **Performance testing** completed
- [ ] **User access** tested and verified

### Post-Deployment

- [ ] **Verify all features** working correctly
- [ ] **Test subdirectory creation** and access
- [ ] **Confirm search functionality** across directories
- [ ] **Validate backup/restore** procedures
- [ ] **Document custom configurations** for your environment
- [ ] **Train users** on subdirectory organization

## Support

### Getting Help

- **Documentation**: Refer to SUBDIRECTORY_SUPPORT.md for feature details
- **Issues**: Report problems at https://github.com/TheTechRun/flatnotes-ttr/issues
- **Community**: Join discussions for usage tips and tricks

### Version Information

- **Current Branch**: `develop`
- **Feature**: Subdirectory support for hierarchical note organization
- **Compatibility**: Backward compatible with existing notes
- **Docker Base**: Python 3.11 slim bullseye

---

**Deployment Status**: Production Ready  
**Branch**: `develop`  
**Last Updated**: 2024-10-30