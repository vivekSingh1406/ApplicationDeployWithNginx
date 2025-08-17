

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
