# Deployment Guide - Paris Africana International School

## Pre-Deployment Checklist

- [ ] Update all school contact information
- [ ] Replace placeholder images with real school photos
- [ ] Update Google Analytics ID in `layout.tsx`
- [ ] Configure email notifications (if needed)
- [ ] Update social media links in `Footer.tsx`
- [ ] Set strong admin password/token
- [ ] Test all forms on staging environment
- [ ] Verify all links work correctly
- [ ] Set up SSL certificate
- [ ] Configure domain and DNS

## Environment Variables for Production

Create `.env.production`:

```env
# Database
DATABASE_URL=postgresql://user:password@prod-db-host:5432/paris_africana_prod

# Optional
NEXT_PUBLIC_GA_ID=G-XXXXXXXXXX
SMTP_USER=your-email@gmail.com
SMTP_PASSWORD=your-app-password
SMTP_HOST=smtp.gmail.com
NEXT_PUBLIC_SITE_URL=https://parisafricana.com
```

## Deployment on Vercel

### Step 1: Prepare GitHub Repository

```bash
git init
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin https://github.com/yourusername/paris-africana.git
git push -u origin main
```

### Step 2: Deploy on Vercel

1. Go to https://vercel.com
2. Click "New Project"
3. Import GitHub repository
4. Select "Paris Africana" repo
5. Configure environment variables:
   - `DATABASE_URL`: PostgreSQL connection string
   - Add any other env vars needed
6. Click "Deploy"

### Step 3: Configure Domain

1. In Vercel dashboard, go to Settings > Domains
2. Add your domain: `parisafricana.com`
3. Update DNS records (usually in domain registrar)
4. Vercel provides instructions for your domain provider

### Step 4: Enable HTTPS

Vercel automatically provides free SSL/TLS certificate.

## Deployment on Self-Hosted Server

### Prerequisites

- Ubuntu 20.04+ or similar Linux distribution
- Node.js 18+
- PostgreSQL 13+
- Nginx (reverse proxy)
- PM2 (process manager)

### Step 1: Server Setup

```bash
# Update system
sudo apt update && sudo apt upgrade -y

# Install Node.js
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt install -y nodejs

# Install PostgreSQL
sudo apt install -y postgresql postgresql-contrib

# Install Nginx
sudo apt install -y nginx

# Install PM2
sudo npm install -g pm2
```

### Step 2: Database Setup

```bash
# Connect to PostgreSQL
sudo sudo -u postgres psql

# Create database and user
CREATE DATABASE paris_africana_prod;
CREATE USER paris_user WITH PASSWORD 'strong_password_here';
ALTER ROLE paris_user SET client_encoding TO 'utf8';
ALTER ROLE paris_user SET default_transaction_isolation TO 'read committed';
ALTER ROLE paris_user SET default_transaction_deferrable TO on;
ALTER ROLE paris_user SET default_transaction_read_committed TO off;
GRANT ALL PRIVILEGES ON DATABASE paris_africana_prod TO paris_user;
\q
```

### Step 3: Application Setup

```bash
# Clone repository
cd /var/www
git clone https://github.com/yourusername/paris-africana.git
cd paris-africana

# Install dependencies
npm install

# Create production env file
nano .env.production
# Add: DATABASE_URL=postgresql://paris_user:password@localhost:5432/paris_africana_prod

# Push database schema
npx drizzle-kit push

# Build application
npm run build

# Start with PM2
pm2 start npm --name "paris-africana" -- start

# Save PM2 configuration
pm2 save
pm2 startup
```

### Step 4: Nginx Configuration

Create `/etc/nginx/sites-available/paris-africana`:

```nginx
upstream paris_africana_backend {
    server 127.0.0.1:3000;
}

server {
    listen 80;
    listen [::]:80;
    server_name parisafricana.com www.parisafricana.com;

    # Redirect HTTP to HTTPS
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    listen [::]:443 ssl http2;
    server_name parisafricana.com www.parisafricana.com;

    # SSL Certificate (Let's Encrypt)
    ssl_certificate /etc/letsencrypt/live/parisafricana.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/parisafricana.com/privkey.pem;

    # SSL configuration
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers HIGH:!aNULL:!MD5;
    ssl_prefer_server_ciphers on;

    # Gzip compression
    gzip on;
    gzip_vary on;
    gzip_min_length 1000;
    gzip_types text/plain text/css text/xml text/javascript application/x-javascript application/xml+rss;

    # Security headers
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-XSS-Protection "1; mode=block" always;

    # Proxy settings
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;

    location / {
        proxy_pass http://paris_africana_backend;
        proxy_read_timeout 90;
    }

    # Cache static assets
    location ~* \.(jpg|jpeg|png|gif|ico|css|js|svg|woff|woff2|ttf|eot)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }

    # Security.txt
    location /.well-known/security.txt {
        return 200 "Contact: security@parisafricana.com\n";
        add_header Content-Type text/plain;
    }
}
```

Enable the site:

```bash
sudo ln -s /etc/nginx/sites-available/paris-africana /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx
```

### Step 5: SSL Certificate (Let's Encrypt)

```bash
sudo apt install certbot python3-certbot-nginx -y
sudo certbot certonly --nginx -d parisafricana.com -d www.parisafricana.com
```

## Deployment on Docker

### Dockerfile

```dockerfile
FROM node:18-alpine AS builder

WORKDIR /app

# Copy package files
COPY package*.json ./

# Install dependencies
RUN npm ci

# Copy source code
COPY . .

# Build application
RUN npm run build

# Production stage
FROM node:18-alpine

WORKDIR /app

# Copy package files
COPY package*.json ./

# Install production dependencies
RUN npm ci --omit=dev

# Copy built application
COPY --from=builder /app/.next ./.next
COPY --from=builder /app/public ./public

# Expose port
EXPOSE 3000

# Start application
CMD ["npm", "start"]
```

### Docker Compose

Create `docker-compose.yml`:

```yaml
version: '3.8'

services:
  postgres:
    image: postgres:15-alpine
    environment:
      POSTGRES_DB: paris_africana_prod
      POSTGRES_USER: paris_user
      POSTGRES_PASSWORD: ${DB_PASSWORD}
    volumes:
      - postgres_data:/var/lib/postgresql/data
    ports:
      - "5432:5432"

  app:
    build: .
    environment:
      DATABASE_URL: postgresql://paris_user:${DB_PASSWORD}@postgres:5432/paris_africana_prod
      NODE_ENV: production
    ports:
      - "3000:3000"
    depends_on:
      - postgres
    restart: unless-stopped

volumes:
  postgres_data:
```

Deploy:

```bash
docker-compose up -d
```

## Monitoring & Maintenance

### Set up Logging

```bash
# PM2 logs
pm2 logs paris-africana

# View error logs
tail -f /var/log/nginx/error.log
```

### Monitor Server Health

```bash
# Check disk space
df -h

# Check memory
free -h

# Check CPU
top

# Database backup
pg_dump paris_africana_prod | gzip > backup_$(date +%Y%m%d).sql.gz
```

### Auto-Backups

Create backup script `/home/ubuntu/backup.sh`:

```bash
#!/bin/bash
pg_dump $DATABASE_URL | gzip > /backups/db_backup_$(date +%Y%m%d_%H%M%S).sql.gz

# Keep only last 30 days
find /backups -name "db_backup_*.sql.gz" -mtime +30 -delete
```

Add to crontab:

```bash
crontab -e
# Add: 0 2 * * * /home/ubuntu/backup.sh
```

## Performance Optimization

### Enable Caching

Update `next.config.ts`:

```typescript
export default {
  images: {
    minimumCacheTTL: 60 * 60 * 24 * 365, // 1 year
  },
  compress: true,
  poweredByHeader: false,
};
```

### CDN Setup (Cloudflare)

1. Go to https://cloudflare.com
2. Add your domain
3. Update nameservers at your registrar
4. Enable caching and optimization features

## Monitoring & Analytics

### Google Analytics

1. Add your Google Analytics ID to `.env.production`
2. Update the script in `layout.tsx`

### Error Tracking (Sentry - Optional)

Install Sentry:

```bash
npm install @sentry/nextjs
```

Configure in `next.config.ts` and add monitoring.

## Security Best Practices

### Update Dependencies

```bash
npm audit
npm update
```

### Configure Firewall

```bash
sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw enable
```

### Change Admin Credentials

Update the admin token in the code and database immediately.

### Database Security

```bash
# Create dedicated database user
ALTER USER paris_user WITH PASSWORD 'very_strong_password';

# Restrict connections
sudo nano /etc/postgresql/13/main/postgresql.conf
# Set: listen_addresses = 'localhost'
```

## Post-Deployment

1. **Test all pages**: Verify homepage, admissions, forms work
2. **Test forms**: Submit test admissions and contact inquiries
3. **Check analytics**: Ensure Google Analytics is tracking
4. **Monitor logs**: Check server logs for errors
5. **SSL certificate**: Verify HTTPS is working
6. **Email notifications**: Set up admin notifications
7. **Backups**: Verify database backups are working

## Troubleshooting

### Application won't start

```bash
pm2 logs paris-africana
npm run build
pm2 restart paris-africana
```

### Database connection error

```bash
# Check PostgreSQL is running
sudo systemctl status postgresql

# Check connection string
cat .env.production | grep DATABASE_URL
```

### High server load

```bash
# Check processes
top

# Monitor database
sudo -u postgres psql -d paris_africana_prod -c "SELECT * FROM pg_stat_statements;"

# Optimize queries with indexes
```

## Support

For deployment issues:
- Check logs: `pm2 logs`
- Verify environment variables
- Test database connection
- Check Nginx configuration
- Monitor server resources

---

**Last Updated**: 2024
**Version**: 1.0.0
