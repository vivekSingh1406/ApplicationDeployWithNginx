

#### Build the React Application
```
cd /path/to/your/react-app
npm run build
```

#### Build the Spring Boot Application
```
cd /path/to/your/spring-boot-app
./mvnw clean package
```
#### Install Nginx
```
sudo apt update
sudo apt install nginx
```
#### Configure Nginx
```
sudo nano /etc/nginx/sites-available/myapp.conf
```
#### Add the following configuration to the file
```
server {
    listen 80;
    server_name your_domain_or_IP;

    location / {
        root /path/to/your/react-app/dist;  # Path to the React build folder
        try_files $uri $uri/ /index.html;   # Handle routing for React
    }

    location /api {  # Assuming your Spring Boot API is prefixed with /api
        proxy_pass http://localhost:8080;  # Change the port if your Spring Boot app runs on a different port
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```
####  Enable the Configuration
```
sudo ln -s /etc/nginx/sites-available/myapp.conf /etc/nginx/sites-enabled/

```
#### Test Nginx Configuration(Before restarting Nginx, test the configuration for any syntax errors)
```
sudo nginx -t
```
#### Restart Nginx(If the test is successful, restart Nginx to apply the changes)
```
sudo systemctl restart nginx
```
#### Run Your Spring Boot Application
```
java -jar /path/to/your/spring-boot-app/target/your-app.jar
```

#### Summary of Commands
```
# Build React app
cd /path/to/your/react-app
npm run build

# Build Spring Boot app
cd /path/to/your/spring-boot-app
./mvnw clean package

# Install Nginx
sudo apt update
sudo apt install nginx

# Create Nginx config
sudo nano /etc/nginx/sites-available/myapp.conf

# (Add the Nginx configuration)

# Enable the configuration
sudo ln -s /etc/nginx/sites-available/myapp.conf /etc/nginx/sites-enabled/

# Test Nginx configuration
sudo nginx -t

# Restart Nginx
sudo systemctl restart nginx

# Run Spring Boot app
java -jar /path/to/your/spring-boot-app/target/your-app.jar
```

#### End to end Deploy

```
server {
    listen 80;
    server_name yourdomain.com www.yourdomain.com; # Replace with your domain or IP
    
    # Gzip compression
    gzip on;
    gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

    # Frontend Configuration
    location / {
        root /var/www/your-react-app/dist; # Path to your built React app
        try_files $uri $uri/ /index.html; # Required for React Router
        index index.html;

        # Cache settings for static assets
        location ~* \.(?:jpg|jpeg|png|gif|ico|css|js|svg|woff2)$ {
            expires 1y;
            add_header Cache-Control "public";
        }
    }

    # Backend API Proxy Configuration
    location /api/ { # All requests to /api get proxied to Spring Boot
        proxy_pass http://localhost:8080/; # Your Spring Boot runs here
        proxy_http_version 1.1;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        
        # Timeout settings
        proxy_connect_timeout 300;
        proxy_send_timeout 300;
        proxy_read_timeout 300;
        send_timeout 300;
    }

    # Block direct access to backend services
    location ~* ^/actuator|^/swagger|^/v2/api-docs {
        deny all;
        return 404;
    }

    # SSL Configuration (optional but recommended)
    # listen 443 ssl http2;
    # ssl_certificate /etc/letsencrypt/live/yourdomain.com/fullchain.pem;
    # ssl_certificate_key /etc/letsencrypt/live/yourdomain.com/privkey.pem;
    # include /etc/letsencrypt/options-ssl-nginx.conf;
    # ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem;
}
```
