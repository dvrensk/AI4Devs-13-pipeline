# Deployment Todo List

## 1. AWS Setup

### EC2 Instance
- [x] Create an EC2 instance (t2.micro or t3.micro for testing)
  - Ubuntu Server 24.04 LTS
  - Configure security group:
    - SSH (port 22)
    - HTTP (port 80)
    - HTTPS (port 443)
  - Use existing key pair
  - Note the public IP address (13.60.57.150)

### RDS Setup
- [x] Verify RDS instance is running
  - Endpoint: backend-1.chgeoqqmsntl.eu-north-1.rds.amazonaws.com
  - Region: eu-north-1
- [x] Configure RDS security group
  - Allow inbound PostgreSQL (port 5432) from EC2 security group
- [x] Note database credentials
- [x] Test database connection

### Domain Setup

The domain backend.ai4devs.valevalevale.es will be used for the backend.

- [x] Add A record pointing to EC2 instance
- [x] Wait for DNS propagation (can take up to 48 hours)

## 2. Server Preparation

### Initial Setup
- [x] SSH into the server
  ```bash
  ssh ubuntu@backend.ai4devs.valevalevale.es
  ```
- [x] Update system
  ```bash
  sudo apt update && sudo apt upgrade -y
  ```

### Install Required Software
- [x] Install Node.js 23.x
  ```bash
  # Install nvm (Node Version Manager)
  curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
  source ~/.bashrc
  
  # Install Node.js 23.x
  nvm install 23
  nvm use 23
  nvm alias default 23
  
  # Verify installation
  node --version  # Should show v23.x.x
  ```
- [x] Install PostgreSQL client
  ```bash
  sudo apt install -y postgresql-client
  ```
- [x] Install PM2 for process management
  ```bash
  # Install PM2 globally for the ubuntu user
  npm install -g pm2
  
  # Setup PM2 to start on system boot
  pm2 startup ubuntu
  ```
- [x] Install Traefik
  ```bash
  # Download the latest version (adjust version number as needed)
  curl -LO https://github.com/traefik/traefik/releases/download/v3.4.0/traefik_v3.4.0_linux_amd64.tar.gz

  # Verify the download (compare with checksums from the release page)
  sha256sum traefik_v3.4.0_linux_amd64.tar.gz

  # Extract the binary to a system location
  sudo tar -zxvf traefik_v3.4.0_linux_amd64.tar.gz -C /usr/local/bin

  # Allow the binary to bind to privileged ports
  sudo setcap 'cap_net_bind_service=+ep' /usr/local/bin/traefik

  # Verify the installation and capabilities
  traefik version
  getcap /usr/local/bin/traefik  # Should show cap_net_bind_service+ep
  ```

### Directory Structure
- [x] Create application directory structure
  ```bash
  sudo mkdir -p /var/www/ats_backend
  sudo chown ubuntu:ubuntu /var/www/ats_backend
  cd /var/www/ats_backend
  mkdir -p {releases,shared}
  ```

## 3. Application Setup

### RDS Configuration
- [x] Configure RDS security group
  - Allow inbound PostgreSQL (port 5432) from EC2 security group
  - Note the RDS endpoint: backend-1.chgeoqqmsntl.eu-north-1.rds.amazonaws.com
- [x] Set up database user and password
- [ ] Create database schema
  ```bash
  cd /var/www/ats_backend/current
  npm run prisma:migrate:deploy
  ```

### Environment Configuration
- [x] Create shared environment file
  ```bash
  cd /var/www/ats_backend/shared
  touch .env.production
  ```
- [x] Configure environment variables
  - DATABASE_URL
  - NODE_ENV
  - PORT
  - Other required variables

### Traefik Configuration
- [x] Set up Traefik directories and permissions
  ```bash
  # Create traefik system user and group
  sudo useradd -r -s /bin/false traefik
  
  # Create necessary directories
  sudo mkdir -p /etc/traefik/dynamic
  sudo mkdir -p /var/log/traefik
  
  # Set proper ownership (root for config, traefik for logs)
  sudo chown -R root:root /etc/traefik
  sudo chown -R traefik:traefik /var/log/traefik
  
  # Set proper permissions
  sudo chmod 755 /etc/traefik
  sudo chmod 755 /etc/traefik/dynamic
  ```

- [x] Configure Traefik
  ```bash
  # Copy the configuration file (owned by root)
  sudo touch /etc/traefik/traefik.yml
  sudo chown root:root /etc/traefik/traefik.yml
  sudo chmod 644 /etc/traefik/traefik.yml
  sudo vim /etc/traefik/traefik.yml
  
  # Create empty acme.json for Let's Encrypt
  # This needs to be writable by the traefik user for Let's Encrypt
  # Must use 600 permissions as required by Traefik for security
  sudo touch /etc/traefik/acme.json
  sudo chmod 600 /etc/traefik/acme.json
  sudo chown traefik:traefik /etc/traefik/acme.json
  ```

- [x] Create systemd service for Traefik
  ```bash
  sudo tee /etc/systemd/system/traefik.service << EOF
  [Unit]
  Description=Traefik Edge Router
  Documentation=https://docs.traefik.io
  After=network-online.target
  Wants=network-online.target systemd-networkd-wait-online.service

  [Service]
  Type=simple
  User=traefik
  Group=traefik
  ExecStart=/usr/local/bin/traefik --configfile=/etc/traefik/traefik.yml
  Restart=on-failure
  RestartSec=5
  LimitNOFILE=1048576

  [Install]
  WantedBy=multi-user.target
  EOF

  # Reload systemd and enable Traefik
  sudo systemctl daemon-reload
  sudo systemctl enable traefik
  sudo systemctl start traefik
  ```

- [ ] Verify Traefik is running
  ```bash
  # Check service status
  sudo systemctl status traefik
  
  # Check logs
  sudo journalctl -u traefik -f
  ```

## 4. Deployment Setup

### GitHub Actions
- [ ] Create GitHub repository secrets:
  - AWS_HOST
  - AWS_USER
  - AWS_SSH_KEY

- [ ] Create GitHub Actions workflow file:
  - [ ] Test workflow
  - [ ] Deploy workflow
    - Connect to server
    - Pull latest code
    - Install dependencies
    - Build application
    - Run database migrations
    - Update symlinks
    - Restart application

## 5. Application Deployment

### First Deployment
- [ ] Clone repository
  ```bash
  cd /var/www/ats_backend/releases
  git clone <repository-url> $(date +%Y%m%d%H%M%S)
  ```
- [ ] Set up symlinks
  ```bash
  cd /var/www/ats_backend
  ln -s releases/$(ls -t releases | head -n1) current
  ```
- [ ] Install dependencies and build
  ```bash
  cd current
  npm ci
  npm run build
  ```
- [ ] Start application with PM2
  ```bash
  # Start the application
  pm2 start dist/index.js --name ai4devs-backend
  
  # Save the process list for startup
  pm2 save
  
  # Verify the process is running
  pm2 status
  ```
- [ ] Verify application is running
- [ ] Check SSL certificate generation
- [ ] Test domain access

### Monitoring Setup
- [ ] Set up basic monitoring
  - [ ] PM2 monitoring
  - [ ] Application logs
  - [ ] SSL certificate expiration monitoring
  - [ ] RDS performance monitoring

## 6. Backup Strategy

### Database Backup
- [ ] Configure RDS automated backups
- [ ] Set up backup retention policy
- [ ] Test backup and restore process
- [ ] Document RDS backup procedures

### Configuration Backup
- [ ] Backup environment files
- [ ] Document backup process

## 7. Security Measures

### Server Security
- [ ] Configure firewall (UFW)
  ```bash
  sudo ufw allow ssh
  sudo ufw allow http
  sudo ufw allow https
  sudo ufw enable
  ```
- [ ] Set up fail2ban
  ```bash
  sudo apt install fail2ban -y
  sudo systemctl enable fail2ban
  sudo systemctl start fail2ban
  ```

### Application Security
- [ ] Review and secure environment variables
- [ ] Configure rate limiting
- [ ] Set up security headers
- [ ] Secure RDS connection

## 8. Maintenance Tasks

### Regular Maintenance
- [ ] Set up log rotation
- [ ] Configure system updates
- [ ] Set up monitoring alerts
- [ ] Monitor RDS performance

### Documentation
- [ ] Document deployment process
- [ ] Create runbook for common issues
- [ ] Document backup and restore procedures
- [ ] Document RDS maintenance procedures

## 9. Testing

### Deployment Testing
- [ ] Test application functionality
- [ ] Verify SSL certificate
- [ ] Test database connections
- [ ] Verify backup process
- [ ] Test RDS failover (if configured)

### Load Testing
- [ ] Basic load testing
- [ ] Verify resource usage
- [ ] Check response times
- [ ] Monitor RDS performance under load

## 10. Cost Optimization

### AWS Optimization
- [ ] Set up AWS Budget alerts
- [ ] Configure instance scheduling (if needed)
- [ ] Review and optimize resource usage
- [ ] Monitor RDS costs

## Required Files

* .env.production (see: dotenv)
* traefik.yml
* PM2 ecosystem file

## Notes
- Keep all sensitive information in environment variables
- Regularly update dependencies and security patches
- Monitor server resources and logs
- Set up alerts for critical issues
- Document all custom configurations
- Regular backup testing
- Keep deployment documentation up to date
- Monitor RDS performance and costs
- Ensure RDS security group is properly configured
- Consider using AWS Secrets Manager for database credentials
