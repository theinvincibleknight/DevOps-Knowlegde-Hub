## Project 2: Deploying a Node.js Application on an EC2 Ubuntu Instance

### Step 1: Connect to Your EC2 Instance

1. **SSH into the Instance:**
   - Open your terminal (or SSH client of choice).
   - Change permissions on your private key file:
     ```bash
     chmod 400 your-key.pem
     ```
   - Connect to your instance using SSH:
     ```bash
     ssh -i your-key.pem ubuntu@your-instance-public-ip
     ```

### Step 2: Install Node.js and npm

1. **Update Package Index:**
   ```bash
   sudo apt update
   ```

2. **Install Node.js:**
   ```bash
   sudo apt install nodejs
   ```

3. **Install npm (Node Package Manager):**
   ```bash
   sudo apt install npm
   ```

### Step 3: Upload Your Node.js Application

1. **Secure Copy (SCP):**
   - Use SCP to upload your application files from your local machine to the EC2 instance:
     ```bash
     scp -i your-key.pem -r /path/to/your/application ubuntu@your-instance-public-ip:/home/ubuntu/
     ```

2. **Navigate to Your Application Directory:**
   ```bash
   cd /home/ubuntu/your-application-directory
   ```

3. **Install Application Dependencies:**
   ```bash
   npm install
   ```

### Step 4: Run Your Node.js Application

1. **Start Your Application:**
   - You can use tools like `pm2` to manage your Node.js application and keep it running:
     ```bash
     npm install -g pm2
     pm2 start your-app.js
     ```

2. **Configure `pm2` to Start on Boot:**
   ```bash
   pm2 startup systemd
   pm2 save
   ```

### Step 5: Set Up a Reverse Proxy (Optional)

1. **Install Nginx (if not already installed):**
   ```bash
   sudo apt install nginx
   ```

2. **Configure Nginx as a Reverse Proxy:**
   - Create a new configuration file for your application:
     ```bash
     sudo nano /etc/nginx/sites-available/your-app
     ```
     Example configuration:
     ```nginx
     server {
         listen 80;
         server_name your-domain.com;

         location / {
             proxy_pass http://localhost:3000;  # Replace with your app's actual port
             proxy_http_version 1.1;
             proxy_set_header Upgrade $http_upgrade;
             proxy_set_header Connection 'upgrade';
             proxy_set_header Host $host;
             proxy_cache_bypass $http_upgrade;
         }
     }
     ```
   
3. **Enable Your Nginx Configuration:**
   ```bash
   sudo ln -s /etc/nginx/sites-available/your-app /etc/nginx/sites-enabled/
   sudo nginx -t  # Test Nginx configuration
   sudo systemctl restart nginx
   ```

### Step 6: Access Your Application

1. **Open your web browser and enter your EC2 instance’s Public IPv4 address or your domain name** (if you set up a domain and DNS).

Your Node.js application should now be accessible via your EC2 instance. Remember to manage security aspects like firewall rules, HTTPS setup (using SSL/TLS certificates), and regular maintenance of your EC2 instance.