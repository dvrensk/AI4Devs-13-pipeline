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
- [ ] Install Docker
  ```bash
  sudo apt install docker.io docker-compose -y
  sudo usermod -aG docker ubuntu
  ```
- [ ] Install Git
  ```bash
  sudo apt install git -y
  ```

### Directory Structure
- [ ] Create application directory
  ```bash
  mkdir -p /home/ubuntu/ai4devs
  cd /home/ubuntu/ai4devs
  ```

## 3. Application Setup

### RDS Configuration
- [ ] Configure RDS security group
  - Allow inbound PostgreSQL (port 5432) from EC2 security group
  - Note the RDS endpoint: backend-1.chgeoqqmsntl.eu-north-1.rds.amazonaws.com
- [ ] Set up database user and password
- [ ] Create database schema
  ```bash
  npx prisma db push
  ```
- [ ] Test database connection

### Docker Configuration
- [ ] Create docker-compose.yml (without local database)
- [ ] Create Dockerfile
- [ ] Create .env file with RDS connection string
- [ ] Create traefik configuration
- [ ] Set up Docker volumes for persistence

### SSL Configuration
- [ ] Configure Traefik for Let's Encrypt
- [ ] Set up email for SSL notifications
- [ ] Test SSL certificate generation

## 4. CI/CD Setup

### GitHub Actions
- [ ] Create GitHub repository secrets:
  - AWS_HOST
  - AWS_USER
  - AWS_SSH_KEY
  - DOCKER_REGISTRY (if using)
  - DOCKER_USERNAME
  - DOCKER_PASSWORD

- [ ] Create GitHub Actions workflow file:
  - [ ] Test workflow
  - [ ] Build workflow
  - [ ] Deploy workflow

### Docker Registry (Optional)
- [ ] Set up Docker Hub or AWS ECR
- [ ] Configure registry credentials
- [ ] Update CI/CD workflow for registry push

## 5. Application Deployment

### First Deployment
- [ ] Clone repository
  ```bash
  git clone <repository-url> /home/ubuntu/ai4devs
  ```
- [ ] Build and start containers
  ```bash
  docker-compose up -d
  ```
- [ ] Verify application is running
- [ ] Check SSL certificate generation
- [ ] Test domain access

### Monitoring Setup
- [ ] Set up basic monitoring
  - [ ] Container health checks
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

* docker-compose.yml
* Dockerfile
* .env (see: dotenv)

### GitHub Actions Workflow

See [github-action-sketch.yml]()

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
