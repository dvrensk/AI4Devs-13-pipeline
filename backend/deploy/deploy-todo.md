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
- [x] Install and configure nginx
  ```bash
  # Install nginx
  sudo apt update
  sudo apt install -y nginx

  # Create nginx configuration
  sudo tee /etc/nginx/sites-available/backend.ai4devs.valevalevale.es << EOF
  server {
      listen 80;
      server_name backend.ai4devs.valevalevale.es;

      location / {
          proxy_pass http://localhost:3010;
          proxy_http_version 1.1;
          proxy_set_header Upgrade \$http_upgrade;
          proxy_set_header Connection 'upgrade';
          proxy_set_header Host \$host;
          proxy_cache_bypass \$http_upgrade;
      }
  }
  EOF

  # Enable the site
  (cd /etc/nginx/sites-enabled && sudo ln -s ../sites-available/backend.ai4devs.valevalevale.es)
  
  # Test configuration
  sudo nginx -t
  
  # Restart nginx
  sudo systemctl restart nginx
  
  # Verify nginx is running
  sudo systemctl status nginx
  ```

- [x] Set up SSL with Let's Encrypt
  ```bash
  # Install Certbot and nginx plugin
  sudo apt update
  sudo apt install -y certbot python3-certbot-nginx

  # Obtain and install certificate
  sudo certbot --nginx -d backend.ai4devs.valevalevale.es
  # Follow prompts:
  # - Enter email for notifications
  # - Agree to terms
  # - Choose whether to share email with EFF
  # - Choose whether to redirect HTTP to HTTPS (recommended: yes)

  # Verify certificate installation
  sudo certbot certificates

  # Test automatic renewal
  sudo certbot renew --dry-run

  # Verify HTTPS is working
  curl -vI https://backend.ai4devs.valevalevale.es/
  ```

  The Certbot will automatically modify the nginx configuration to look something like this:
  ```nginx
  server {
      listen 80;
      server_name backend.ai4devs.valevalevale.es;
      # Redirect all HTTP traffic to HTTPS
      return 301 https://$host$request_uri;
  }

  server {
      listen 443 ssl;
      server_name backend.ai4devs.valevalevale.es;

      # SSL configuration (automatically added by Certbot)
      ssl_certificate /etc/letsencrypt/live/backend.ai4devs.valevalevale.es/fullchain.pem;
      ssl_certificate_key /etc/letsencrypt/live/backend.ai4devs.valevalevale.es/privkey.pem;
      include /etc/letsencrypt/options-ssl-nginx.conf;
      ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem;

      location / {
          proxy_pass http://localhost:3010;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection 'upgrade';
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;

          # Additional security headers
          proxy_set_header X-Real-IP $remote_addr;
          proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
          proxy_set_header X-Forwarded-Proto $scheme;
      }
  }
  ```

  Notes:
  - Certificates auto-renew every 90 days
  - Renewal is handled by a systemd timer
  - Certbot adds secure SSL configuration automatically
  - HTTP traffic is redirected to HTTPS
  - The configuration includes modern SSL settings and security headers

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
- [ ] Set NODE_ENV for PM2
  ```bash
  # Add NODE_ENV to .profile for PM2 to pick up on system startup
  echo 'export NODE_ENV=production' >> ~/.profile
  source ~/.profile  # Apply to current session
  ```

## 4. Deployment Setup

### GitHub Actions
- [x] Create GitHub deploy key:
  - [x] Add to server
    - `ssh-keygen -t ed25519 -C "GitHub deploy key" -f ~/.ssh/id_ed25519_deploykey -N ""`
    - `cat ~/.ssh/id_ed25519_deploykey.pub`
    - Add to `~/.ssh/config`
      ```
      Host github.com
          HostName github.com
          IdentityFile ~/.ssh/id_ed25519_deploykey
          StrictHostKeyChecking accept-new
      ```
  - [x] Add to GitHub repository (Settings -> Deploy keys -> Add deploy key)
- [x] Create server deploy key:
    ```bash
    ssh-keygen -t ed25519 -C "Server deploy key" -f ~/tmpkey -N ""
    less ~/tmpkey
    rm ~/tmpkey
    cat ~/tmpkey.pub >> ~/.ssh/authorized_keys
    chmod 600 ~/.ssh/authorized_keys
    rm ~/tmpkey.pub
    ```
- [x] Add GitHub repository secrets:
  - AWS_HOST = backend.ai4devs.valevalevale.es
  - AWS_USER = ubuntu
  - AWS_SSH_KEY = tmpkey copied from above

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
- [x] Clone repository
  ```bash
  cd /var/www/ats_backend/releases
  git clone git@github.com:dvrensk/AI4Devs-13-pipeline.git $(date +%Y%m%d%H%M%S)
  ```
- [x] Set up symlinks and copy shared files
  ```bash
  cd /var/www/ats_backend
  rm -f current && ln -s releases/$(ls -rt releases | head -n1)/backend current
  tar cf - shared | tar xf - -C current --strip-components=1
  ```
- [x] Install dependencies and build
  ```bash
  cd current
  npm ci
  npm run build
  ```
- [x] Start application with PM2
  ```bash
  # Start the application
  pm2 start dist/index.js --name ai4devs-backend
  
  # Save the process list for startup
  pm2 save
  
  # Verify the process is running
  pm2 status
  ```
- [x] Verify application is running: `curl http://localhost:3010/` should respond with "Hola LTI!"
- [ ] ~~Check SSL certificate generation
- [x] Test domain access

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
* nginx site configuration (see above)
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
