TODO:
- Check data peristence on container- Rajath
- Test by attaching a data volume to the container and check for persistence of data - Rajath
/opt/powerpipedata/{customer}
- Push dockerfile to optitRepo - Bharath
- Push powerpipe,steampipe container image to registry - Bharath
- Create bash script to orchestate the new account setup - Bharath
- Take input - Cloud credentials, CSP, ModName, CustomerName
- Spinup the new container running on port which is available (9000 -> 9999)
- Install the required packages and start services
- Validate the credentials are correct
- Update NGINX configurations for the new account and restart NGINX
- Report Analysis - Rajath and Srikanth
- Prioritise the alarms
- Format the report to be shared with Customer
Use OauthProxy for authenticating using github id

Check data peristence on container
- Test by attaching a data volume to the container and check for persistence of data


pipeline stages for setting up Powerpipe and Steampipe for a specific customer:
 
AWS Credentials Validation: Verifies AWS credentials.
Port Availability Check: Ensures specified ports are free.
Docker Network Creation: Creates a Docker network if it doesn’t exist.
Docker Container Execution: Runs a Docker container with specified settings.
Module Initialization and Installation: Initializes and installs modules inside the container.
Service Startup: Starts Powerpipe and Steampipe services inside the container.
Configuration File Update: Appends configuration blocks to nginx.conf.
Nginx Reload: Reloads Nginx to apply new configurations.
 
Cleanup:
Whenever we to to delete the container   , from jenkins job  with single parameter  container name   and we need to trigger this job and  cleanup 
Below are the pipeline stages  to do cleanup
Retrieve Network Information: Retrieves network ID associated with the container.
Stop Docker Container: Stops the Docker container.
Delete Docker Container: Deletes the Docker container.
Delete Docker Network: Deletes the Docker network if no longer in use.
Update NGINX Configuration: Updates Nginx configuration and reloads it

/opt/powerpipedata/{customer}
==========================================='''''==========================
use below step "stop and remove running container and then restart by adding -v tag like given below."

sudo mkdir -p /opt/powerpipedata/capitalmind
ls /opt/powerpipedata/capitalmind


sudo docker run -d --name myaccontainer1 \
--network aws_account1_network \
-p 9194:9194 \
-p 9040:9040 \
-e AWS_ACCESS_KEY_ID= \
-e AWS_SECRET_ACCESS_KEY= \
-e AWS_REGION=us-east-1 \
-v /opt/powerpipedata/capitalmind:/opt/powerpipedata/capitalmind \
pp-sp-img


docker exec -it myaccontainer1 /bin/bash

#Check Data Persistence:

#Enter the container:

docker exec -it myaccontainer1 /bin/bash
Write some data inside /opt/powerpipedata/capitalmind within the container:

echo "Test data for persistence" > /opt/powerpipedata/capitalmind/testfile.txt

#Restart the Container:

#Stop the container:

docker stop myaccontainer1
Start the container again:

docker start myaccontainer1

#Verify Data Persistence:

#Re-enter the container:

docker exec -it myaccontainer1 /bin/bash
Check if the data is still there:

cat /opt/powerpipedata/capitalmind/testfile.txt
===================================================================

we can now follow same for foradian,capitalmind.

=============================================================
we can also use
Volume Mounts: Use when you want persistent, isolated storage for your containers. Ideal for production environments where you need long-term data persistence and Docker-managed storage
"docker run -v my_volume:/path/in/container my_image"

but if container gets deleted, the data will also be gone

or bind
"docker run -v /path/on/host:/path/in/container my_image"
we're using bind mounts (-v /path/on/host:/path/in/container) because it provides direct access to the host's file system, allowing you to easily persist and share data between the host and container. This approach is practical for your current use case of testing data persistence.

but not tmpf because
Tmpfs Mounts: Use when you need temporary, high-speed storage for data that doesn’t need to persist after the container stops.

6 September 2024 14:08
sudo docker network create foradian_network
sudo docker run -d --name tt \
--network foradian_network \
-p 9196:9196 \
-p 9043:9043 \
-e AWS_ACCESS_KEY_ID= \
-e AWS_SECRET_ACCESS_KEY= \
-e AWS_REGION=ap-south-1 \
powerpipe__steampipe_image


docker ps

sudo docker exec -it foradian /bin/bash

powerpipe --version
steampipe --version
mkdir -p /home/powerpipe/mod
cd /home/powerpipe/mod
powerpipe mod init
powerpipe mod install github.com/turbot/steampipe-mod-aws-compliance
steampipe query "select * from aws_s3_bucket;"
nohup steampipe service start --port 9196 > steampipe.log 2>&1 &
nohup powerpipe server --port 9043 > powerpipe.log 2>&1 &


To test from hosted machine :
Steampipe: http://localhost:<Mapped-Port>
Powerpipe: http://localhost:9041(or whatever port is assigned)

=============================================================
1. Create a Persistent Volume
A persistent volume allows you to store data that won't be lost when the container stops or is removed.

docker volume create my_persistent_volume
2. Create a Dockerfile for the Testing Container
You can create a Dockerfile to define your container. Here’s a simple example using Ubuntu:

# Dockerfile
FROM ubuntu:latest

# Install basic tools
RUN apt-get update && apt-get install -y curl vim

# Create a test directory to store data
RUN mkdir /test_data

# Set working directory
WORKDIR /test_data
3. Build the Docker Image
Build the image from the Dockerfile:

docker build -t my-testing-container .
4. Run the Container with the Persistent Volume
Use the following command to run the container and mount the persistent volume:

docker run -d --name testing_container \
-v my_persistent_volume:/test_data \
my-testing-container
In this example, the volume my_persistent_volume is mounted to /test_data in the container.

5. Access the Container
You can access the running container to verify the volume is working:

docker exec -it testing_container /bin/bash
Inside the container, any data written to /test_data will be stored in the persistent volume.

6. Verify Persistent Data
You can stop and remove the container, and the data in the volume will still persist:

docker stop testing_container
docker rm testing_container
Then, run a new container and check the data in /test_data to verify it’s still there.

