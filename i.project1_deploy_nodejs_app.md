## Project 1: Deploying a Node.js Application on an EC2 Ubuntu Instance

#### Step 1: Connect to Your EC2 Instance

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

#### Step 2: Prepare Your EC2 Instance

1. **Update the Kernel and Package Repository:**
   ```bash
   sudo apt update
   ```

2. **Check and Install Git:**
   - Verify if Git is installed:
     ```bash
     git --version
     ```
   - If Git is not installed, install it:
     ```bash
     sudo apt install git
     ```

3. **Install Node.js and npm:**
   - Install Node.js:
     ```bash
     sudo apt install nodejs
     ```
   - Verify Node.js installation:
     ```bash
     node -v
     ```
   - Install npm (Node Package Manager):
     ```bash
     sudo apt install npm
     ```
   - Verify npm installation:
     ```bash
     npm --version
     ```

#### Step 3: Clone and Configure Your Node.js Application

1. **Clone Your Project Repository:**
   - Clone the project repository from GitHub (replace URL with your project's URL):
     ```bash
     git clone https://github.com/verma-kunal/AWS-Session.git
     ```

2. **Navigate to Your Project Directory:**
   - Check the cloned directory and move into it:
     ```bash
     cd AWS_Session
     ls  # Verify the contents of the directory
     ```

3. **Create and Configure .env File:**
   - Create a `.env` file:
     ```bash
     touch .env
     ```
   - Edit `.env` file with required environment variables:
     ```plaintext
     DOMAIN="http://localhost:3000"
     PORT=3000
     STATIC_DIR="./client"

     # Stripe API keys
     PUBLISHABLE_KEY="your_publishable_key_here"
     SECRET_KEY="your_secret_key_here"
     ```
   - Save and exit the `.env` file (use `vi` or `nano` to edit).

4. **Install Dependencies:**
   - Install project dependencies using npm:
     ```bash
     npm install
     ```

#### Step 4: Start Your Node.js Application

1. **Run Your Application:**
   - Start your Node.js application:
     ```bash
     npm run start
     ```
   - This will typically start your application on the specified port (e.g., 3000).

#### Step 5: Configure Security Group to Allow Access

1. **Configure Security Group Inbound Rules:**
   - Open AWS Management Console.
   - Navigate to EC2 service.
   - Select your instance, go to "Security" tab, and click on the associated security group.
   - Edit inbound rules to allow traffic on port 3000 (or your application's port) from your IP or `0.0.0.0/0` for testing purposes.

#### Step 6: Access Your Node.js Application

1. **Access Your Application:**
   - Open your web browser.
   - Enter your EC2 instance’s Public IPv4 address or your domain name followed by the port number (e.g., `http://your-public-ip:3000`).

Your Node.js application should now be deployed and accessible from the internet via your EC2 instance.

### Additional Notes:

- Ensure you manage your `.env` file securely as it contains sensitive information.
- Consider setting up a reverse proxy (e.g., Nginx) for production deployments to handle incoming requests and SSL termination.
- Regularly update and secure your EC2 instance and application dependencies to maintain stability and security.