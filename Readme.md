**Project Name** : EC2 Instance with a Containerized Application

**Project Objective** : Deploy a containerized web application on an AWS EC2 instance using Docker. This project demonstrates launching an EC2 instance, setting up Docker, creating a simple application, and running it in a container.

**Prerequisites** :
1. AWS Account: Sign up at AWS.
2. SSH Client: To connect to the EC2 instance.
3. Basic Knowledge : Familiarity with Docker and Linux commands.
4. Installed Tools :
    - Docker (for local testing, optional)
    - SCP (Secure Copy Protocol) for file transfer.

**Project Steps**

Step 1: Launch an EC2 Instance
1. Log in to the AWS Management Console.
2. Navigate to the EC2 Service.
3. Click Launch Instance and configure:
    - AMI: Select Amazon Linux 2.
    - Instance Type: Use t2.micro (free tier eligible).
    - Security Group: Add an inbound rule to allow SSH (port 22) and HTTP (port 80).
    - Generate or use an existing key pair for SSH access.
4. Click Launch and note the public IP of your instance.

Step 2: Connect to the EC2 Instance
1. Open your terminal and connect using SSH: ssh -i <your-key-file.pem> ec2-user@<instance-public-ip>

Step 3: Install Docker on EC2
1. Update the package manager: sudo yum update -y
2. Install Docker:
    - sudo amazon-linux-extras enable docker
      sudo yum install docker -y
3. Start Docker and enable it to run on startup:
    - sudo service docker start
      sudo systemctl enable docker
4. Add the ec2-user to the Docker group (optional):
- sudo usermod -aG docker ec2-user
- Log out and back in for the changes to take effect.

Step 4: Create and Containerize the Application
1. Create a directory named app on your local system and add the following files:
    - **File 1:** app.js
      A simple Node.js application to return a "Hello World" message:
        - `const http = require('http');
          const server = http.createServer((req, res) => {
          res.writeHead(200, { 'Content-Type': 'text/plain' });
          res.end('Hello World from Docker on EC2!');
          });
          server.listen(80);`
    -  **File 2:** Dockerfile
       Instructions to containerize the application:
        - `FROM node:14
          WORKDIR /usr/src/app
          COPY app.js .
          CMD ["node", "app.js"]`
2. Transfer the app directory to the EC2 instance using scp: scp -i <your-key-file.pem> -r app/ ec2-user@<instance-public-ip>:/home/ec2-user/
3. SSH into the instance and navigate to the app directory: cd /home/ec2-user/app
4. Build the Docker image: docker build -t hello-world-app .
5. Run the Docker container: docker run -d -p 80:80 hello-world-app

Step 5: Verify the Application
1. Open your browser and navigate to: http://<instance-public-ip>
   - You should see the message: "Hello World from Docker on EC2!".

Step 6: Optional Enhancements
1. Push the Docker image to a Docker registry:
    - docker login
    - docker tag hello-world-app <your-dockerhub-username>/hello-world-app
    - docker push <your-dockerhub-username>/hello-world-app
2. Automate the EC2 setup using a shell script (setup-ec2.sh):
    - #!/bin/bash
    - sudo yum update -y
    - sudo amazon-linux-extras enable docker
    - sudo yum install docker -y
    - sudo service docker start
    - sudo systemctl enable docker
    - docker build -t hello-world-app /home/ec2-user/app
    - docker run -d -p 80:80 hello-world-app


