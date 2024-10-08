To configure AWS credentials and Steampipe connections based on the examples you provided, here are the steps you need to follow:
 
### 1. **Install AWS CLI and Steampipe**

   - Ensure that you have both the AWS CLI and Steampipe installed on your system.

   - **AWS CLI**: Follow the [installation guide](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html) if not already installed.

   - **Steampipe**: Follow the [installation instructions](https://steampipe.io/downloads).
 
### 2. **Configure AWS SSO**

   - **Login to AWS SSO**:

     - Run the following command to log in to your AWS SSO account:

       ```bash

       aws sso login --profile account_a_with_sso

       ```

   - **Update your AWS credentials file**:

     - Edit your `~/.aws/credentials` or `~/.aws/config` file to include the SSO configuration:

       ```ini

       [account_a_with_sso]

       sso_start_url = https://d-9a672b0000.awsapps.com/start

       sso_region = us-east-2

       sso_account_id = 000000000000

       sso_role_name = SSO-ReadOnly

       region = us-east-1

       ```

   - **Steampipe connection**:

     - Edit the `~/.steampipe/config/aws.spc` file to add the connection:

       ```hcl

       connection "aws_account_a_with_sso" {

         plugin  = "aws"

         profile = "account_a_with_sso"

         regions = ["us-west-2", "us-east-1",  "us-west-1", "us-east-2"]

       }

       ```
 
### 3. **AssumeRole Credentials (No MFA)**

   - **Update your AWS credentials file**:

     - Edit your `~/.aws/credentials` file to include the AssumeRole profiles:

       ```ini

       [cli_user]

       aws_access_key_id = 52

       aws_secret_access_key = F5J8m
 
       [account_a_role_without_mfa]

       role_arn = arn:aws:iam::111111111111:role/spc_role

       source_profile = cli_user

       external_id = xxxxx
 
       [account_b_role_without_mfa]

       role_arn = arn:aws:iam::222222222222:role/spc_role

       source_profile = cli_user

       external_id = yyyyy

       ```

   - **Steampipe connection**:

     - Edit the `~/.steampipe/config/aws.spc` file to add the connections:

       ```hcl

       connection "aws_account_a" {

         plugin  = "aws"

         profile = "account_a_role_without_mfa"

         regions = ["us-east-1", "us-east-2"]

       }
 
       connection "aws_account_b" {

         plugin  = "aws"

         profile = "account_b_role_without_mfa"

         regions = ["us-east-1", "us-east-2"]

       }

       ```
 
### 4. **AWS Profile Credentials**

   - **Update your AWS credentials file**:

     - Edit your `~/.aws/credentials` file to include the profiles:

       ```ini

       [account_a]

       aws_access_key_id = AKS252

       aws_secret_access_key = SH42YMWN8BOIOF5J8m

       region = us-west-2
 
       [account_b]

       aws_access_key_id = AKIS98F

       aws_secret_access_key = Apf938nKLWP9mg4

       ```

   - **Steampipe connection**:

     - Edit the `~/.steampipe/config/aws.spc` file to add the connections:

       ```hcl

       connection "aws_account_a" {

         plugin  = "aws"

         profile = "account_a"

         regions = ["us-east-1", "us-west-2"]

       }
 
       connection "aws_account_b" {

         plugin  = "aws"

         profile = "account_b"

         regions = ["ap-southeast-1", "ap-southeast-2"]

       }

       ```
 
### 5. **Running Steampipe**

   - Start Steampipe and use the connections configured:

     ```bash

     steampipe query

     ```
 
### Notes:

- **SSO**: Remember, if your SSO session expires, you will need to re-authenticate using `aws sso login`.

- **Profile Credentials**: These can be used directly without any additional login if they are already configured.
 
This step-by-step guide should help you configure your AWS credentials and Steampipe connections correctly. Let me know if you need any further clarification!
 
Yes, the steps I provided will allow you to configure multiple AWS accounts and use them in a single terminal session. Here's a summary of how it works:
 
### Overview of Using Multiple AWS Accounts in One Terminal
 
1. **Configure AWS Credentials**:

   - You can have multiple AWS profiles in your `~/.aws/credentials` file.

   - Each profile corresponds to a different AWS account or role.
 
2. **Steampipe Connections**:

   - Each connection in Steampipe can be associated with a different AWS profile.
 
### Example Configuration
 
- **AWS Credentials File (`~/.aws/credentials` or `~/.aws/config`)**:

  - You might have profiles for different AWS accounts, including SSO-based profiles, AssumeRole profiles, and standard profiles.
 
    ```ini

    [account_a_with_sso]

    sso_start_url = https://d-9a672b0000.awsapps.com/start

    sso_region = us-east-2

    sso_account_id = 000000000000

    sso_role_name = SSO-ReadOnly

    region = us-east-1
 
    [account_a_role_without_mfa]

    role_arn = arn:aws:iam::111111111111:role/spc_role

    source_profile = cli_user

    external_id = xxxxx
 
    [account_b_role_without_mfa]

    role_arn = arn:aws:iam::222222222222:role/spc_role

    source_profile = cli_user

    external_id = yyyyy
 
    [account_a]

    aws_access_key_id = AKIA4YFAKEKEYXTDS252

    aws_secret_access_key = SH42YMW5p3EThisIsNotRealzTiEUwXN8BOIOF5J8m

    region = us-west-2
 
    [account_b]

    aws_access_key_id = AKIA4YFAKEKEYJ7HS98F

    aws_secret_access_key = Apf938vDKd8ThisIsNotRealzTiEUwXj9nKLWP9mg4

    ```
 
- **Steampipe Configuration (`~/.steampipe/config/aws.spc`)**:

  - You define multiple connections, each associated with a different profile. This allows you to switch between accounts by specifying the connection.
 
    ```hcl

    connection "aws_account_a_with_sso" {

      plugin  = "aws"

      profile = "account_a_with_sso"

      regions = ["us-west-2", "us-east-1",  "us-west-1", "us-east-2"]

    }
 
    connection "aws_account_a" {

      plugin  = "aws"

      profile = "account_a_role_without_mfa"

      regions = ["us-east-1", "us-east-2"]

    }
 
    connection "aws_account_b" {

      plugin  = "aws"

      profile = "account_b_role_without_mfa"

      regions = ["us-east-1", "us-east-2"]

    }
 
    connection "aws_account_a" {

      plugin  = "aws"

      profile = "account_a"

      regions = ["us-east-1", "us-west-2"]

    }
 
    connection "aws_account_b" {

      plugin  = "aws"

      profile = "account_b"

      regions = ["ap-southeast-1", "ap-southeast-2"]

    }

    ```
 
### Running Steampipe Queries
 
- When you start Steampipe, you can choose which connection (and thus which AWS account) to use by specifying the connection name in your queries.
 
### Switching Between AWS Accounts in the Terminal
 
To switch between AWS accounts, simply change the connection used in your Steampipe queries or commands. Each connection is tied to a different AWS profile, allowing you to manage multiple accounts from the same terminal session without needing to log in and out.
 
### Additional Tips
 
- **Switching Profiles Manually**: If you want to switch profiles manually in a single terminal session (outside of Steampipe), you can set the `AWS_PROFILE` environment variable:

  ```bash

  export AWS_PROFILE=account_a

  ```

  Then run your AWS CLI commands.
 
This setup should meet your requirement to run multiple AWS accounts in one terminal session using Steampipe. Let me know if you have any more questions!
 
Testing
To test the setup and confirm that using multiple AWS accounts in one terminal session is working, follow these steps:
 
### 1. **Verify AWS Profiles Setup**

   - Ensure your AWS credentials file (`~/.aws/credentials` or `~/.aws/config`) is correctly configured with multiple profiles as mentioned earlier.
 
   ```bash

   cat ~/.aws/credentials

   ```
 
   - You should see your profiles listed like this:
 
     ```ini

     [account_a_with_sso]

     sso_start_url = https://d-9a672b0000.awsapps.com/start

     sso_region = us-east-2

     sso_account_id = 000000000000

     sso_role_name = SSO-ReadOnly

     region = us-east-1
 
     [account_a_role_without_mfa]

     role_arn = arn:aws:iam::111111111111:role/spc_role

     source_profile = cli_user

     external_id = xxxxx
 
     [account_b_role_without_mfa]

     role_arn = arn:aws:iam::222222222222:role/spc_role

     source_profile = cli_user

     external_id = yyyyy
 
     [account_a]

     aws_access_key_id = AKIA4YFAKEKEYXTDS252

     aws_secret_access_key = SH42YMW5p3EThXN8BOIOF5J8m

     region = us-west-2
 
     [account_b]

     aws_access_key_id = AKIJ7HS98F

     aws_secret_access_key = Apf939mg4

     ```
 
### 2. **Test AWS CLI with Each Profile**
 
   - Test the AWS CLI using each profile to ensure that the credentials are correctly set up.
 
   ```bash

   aws sts get-caller-identity --profile account_a_with_sso

   aws sts get-caller-identity --profile account_a_role_without_mfa

   aws sts get-caller-identity --profile account_b_role_without_mfa

   aws sts get-caller-identity --profile account_a

   aws sts get-caller-identity --profile account_b

   ```
 
   - This command will return the AWS account ID and user ARN associated with each profile. Ensure that each command returns a different account ID as expected.
 
### 3. **Verify Steampipe Connections**

   - Ensure your Steampipe configuration file (`~/.steampipe/config/aws.spc`) is correctly set up with connections for each profile.
 
   ```bash

   cat ~/.steampipe/config/aws.spc

   ```
 
   - It should look like this:
 
     ```hcl

     connection "aws_account_a_with_sso" {

       plugin  = "aws"

       profile = "account_a_with_sso"

       regions = ["us-west-2", "us-east-1",  "us-west-1", "us-east-2"]

     }
 
     connection "aws_account_a" {

       plugin  = "aws"

       profile = "account_a_role_without_mfa"

       regions = ["us-east-1", "us-east-2"]

     }
 
     connection "aws_account_b" {

       plugin  = "aws"

       profile = "account_b_role_without_mfa"

       regions = ["us-east-1", "us-east-2"]

     }
 
     connection "aws_account_a" {

       plugin  = "aws"

       profile = "account_a"

       regions = ["us-east-1", "us-west-2"]

     }
 
     connection "aws_account_b" {

       plugin  = "aws"

       profile = "account_b"

       regions = ["ap-southeast-1", "ap-southeast-2"]

     }

     ```
 
### 4. **Test Steampipe Queries**
 
   - Launch Steampipe and test queries using the different connections.
 
   ```bash

   steampipe query

   ```
 
   - Run a query against one of the connections:
 
     ```sql

     select account_id, account_name from aws_account;

     ```
 
   - Switch to a different connection and run the same query:
 
     ```bash

     .connect aws_account_b_role_without_mfa

     select account_id, account_name from aws_account;

     ```
 
   - Repeat this for the other connections to verify that each connection returns data from the correct AWS account.
 
### 5. **Check the Active Connection in Steampipe**

   - While in the Steampipe CLI, check which connection is active:
 
     ```bash

     .show

     ```
 
   - This command will show you the current connection name and any other relevant details.
 
### 6. **Test SSO Profile Login**

   - For the SSO profile, ensure you are logged in:
 
     ```bash

     aws sso login --profile account_a_with_sso

     ```
 
   - After logging in, rerun the Steampipe query with the SSO connection:
 
     ```bash

     .connect aws_account_a_with_sso

     select account_id, account_name from aws_account;

     ```
 
### Conclusion:

- If each query returns data from the expected AWS account, and you can switch between connections without issues, the setup is working correctly. You can confirm that using multiple AWS accounts in one terminal session is possible.
 
Let me know if you encounter any issues during testing!
 
 
 
USING WORKSPACE
 
To effectively manage multiple AWS accounts in Steampipe using workspaces, follow these steps:
 
### 1. **Create the Workspaces Configuration File**

   - Open or create the `workspaces.spc` file in your Steampipe configuration directory:
 
     ```bash

     nano ~/.steampipe/config/workspaces.spc

     ```
 
   - Define your workspaces in this file. For example:
 
     ```hcl

     workspace "default" {

       connections = ["aws_account_a", "aws_account_b"]

     }
 
     workspace "acme_dev" {

       connections = ["aws_account_a_with_sso"]

     }
 
     workspace "acme_prod" {

       connections = ["aws_account_a_role_without_mfa", "aws_account_b_role_without_mfa"]

     }

     ```
 
### 2. **Test the Default Workspace**

   - Ensure your default workspace is correctly set up. This workspace will be used if no specific workspace is provided.
 
   ```bash

   steampipe query --snapshot "select * from aws_account"

   ```
 
   - This will query the `aws_account` table using the connections defined in the default workspace.
 
### 3. **Switch Between Workspaces**

   - Test querying with a different workspace by specifying it explicitly:
 
     ```bash

     steampipe query --snapshot --workspace=acme_dev "select * from aws_account"

     ```
 
   - Or set the workspace via the environment variable:
 
     ```bash

     export STEAMPIPE_WORKSPACE=acme_prod

     steampipe query --snapshot "select * from aws_account"

     ```
 
### 4. **Override Workspace Settings**

   - You can override specific settings of the workspace by adding specific arguments to your query. For example:
 
     ```bash

     steampipe query --snapshot \

       --workspace=acme_prod \

       --workspace_database=local \

       "select * from aws_account"

     ```
 
   - This will use the `local` database but still follow the other settings defined in `acme_prod`.
 
### 5. **Test the Behavior of Environment Variables**

   - Test how environment variables interact with the workspace configuration:
 
     ```bash

     export STEAMPIPE_WORKSPACE_DATABASE=acme/dev

     steampipe query --snapshot "select * from aws_account"

     ```
 
   - This will use the `acme/dev` database with other settings from the default workspace.
 
   - Explicitly overriding with the workspace argument:
 
     ```bash

     steampipe query --snapshot --workspace=default "select * from aws_account"

     ```
 
   - This will override any environment variables with the values from the `default` workspace.
 
### 6. **Validate Multiple Account Setup**

   - Ensure that when you switch workspaces, the data returned corresponds to the AWS accounts linked with that workspace.
 
   - Run the following to confirm:
 
     ```bash

     export STEAMPIPE_WORKSPACE=acme_prod

     steampipe query --snapshot "select * from aws_account"

     ```
 
   - Verify the output to ensure it is pulling from the correct accounts as per the workspace configurations.
 
### Conclusion:

By following these steps, you'll be able to manage and query multiple AWS accounts efficiently using Steampipe workspaces. This method allows for flexibility and easier switching between different account setups. Let me know how it goes!
 
DOCUMENTATION ON USING WORKSPACE FEATURE
 
### Why Workspaces Are the Best Solution for Managing Multiple AWS Accounts in Steampipe
 
When working with multiple AWS accounts in Steampipe, using **workspaces** is considered the best solution for several reasons:
 
1. **Isolation of Configuration**:

   - Workspaces allow you to isolate configurations for each AWS account. Each workspace can have its own connection settings, plugins, and regions defined separately. This isolation ensures that there’s no confusion or overlap between accounts when querying data.
 
2. **Explicit Control Over Context**:

   - With workspaces, you explicitly define which AWS account’s configuration should be used by specifying the workspace at query time. This eliminates ambiguity about which account's data is being queried.
 
3. **Avoiding Implicit Errors**:

   - Without workspaces, if you have multiple AWS accounts configured in a single environment, it can be easy to accidentally run queries against the wrong account, especially if you forget to switch profiles or set the correct environment variables. Workspaces prevent this by requiring explicit context for each query.
 
4. **Ease of Management**:

   - Each workspace can be managed independently. For example, if you need to update credentials, add regions, or change other settings, you can do so within the context of a specific workspace without affecting others.
 
5. **Scalability**:

   - As your environment grows and you need to manage more AWS accounts, workspaces provide a scalable way to handle this. You can create a new workspace for each account without worrying about the complexity of managing a shared configuration file.
 
6. **Consistency in Execution**:

   - When using workspaces, you can consistently execute queries with the same command structure, simply changing the workspace as needed. This uniform approach reduces the likelihood of errors and simplifies the execution of automated scripts or CI/CD pipelines.
 
### Example Scenario: Multiple AWS Accounts with Workspaces
 
Imagine you have two AWS accounts, `aws_account_1` and `aws_account_2`, and you want to ensure that data from these accounts is always queried separately, with no overlap or risk of confusion.
 
#### Step-by-Step Configuration
 
1. **Create Workspaces in Steampipe Configuration File**:

   - You define workspaces for each account in the `~/.steampipe/config/workspaces.spc` file.
 
   ```hcl

   workspace "aws_account_1" {

     connection {

       name   = "aws_account_1_conn"

       plugin = "aws"

       profile = "account_1_profile"

       regions = ["us-west-2", "us-east-1"]

     }

   }
 
   workspace "aws_account_2" {

     connection {

       name   = "aws_account_2_conn"

       plugin = "aws"

       profile = "account_2_profile"

       regions = ["us-west-1", "us-east-2"]

     }

   }

   ```
 
2. **Run Queries Using Specific Workspaces**:

   - When you want to run a query against `aws_account_1`, you explicitly specify its workspace:
 
     ```bash

     steampipe query --workspace=aws_account_1 "select * from aws_account;"

     ```
 
   - To query `aws_account_2`, simply switch the workspace:
 
     ```bash

     steampipe query --workspace=aws_account_2 "select * from aws_account;"

     ```
 
3. **Environment Variable Flexibility**:

   - If needed, you can also use environment variables to set the workspace:
 
     ```bash

     export STEAMPIPE_WORKSPACE=aws_account_1

     steampipe query "select * from aws_account;"

     ```
 
     - This approach can be particularly useful in scripts or automated processes where you want to dynamically switch between accounts.
 
4. **Avoiding Overlap**:

   - Since each workspace is entirely separate, there’s no risk of accidentally mixing up data or settings between accounts. Each query is executed within its clearly defined context.
 
### Conclusion
 
Using workspaces in Steampipe is the best solution for managing multiple AWS accounts because it provides clear isolation, control, and scalability. This method ensures that each AWS account is managed separately, reducing the risk of errors and making it easier to scale as you add more accounts or need to execute more complex queries. The PowerPipe team likely recommended this approach because it aligns well with best practices for managing multi-account environments in a clear, maintainable way.
 
=================================================================================================================================================================================================================================
# nginx
 ```
cat  /etc/nginx/nginx.conf
user www-data;
worker_processes auto;
pid /run/nginx.pid;
include /etc/nginx/modules-enabled/*.conf;

events {
    worker_connections 768;
}

http {
    sendfile on;
    tcp_nopush on;
    types_hash_max_size 2048;
    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    # Server block for the proxy
    server {
        listen 80;
        server_name 10.10.30.93;

        # Redirect /rajath/account1/powerpipe to the correct IP address and port
        location /rajath/account1/powerpipe/ {
            proxy_pass http://10.10.30.93:9040;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }

        # Redirect /ct/account2/powerpipe to the correct IP address and port
        location /ct/account2/powerpipe/ {
            proxy_pass http://10.10.30.93:9041;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }

        # Redirect 9043 
        location /Foradian/account3/powerpipe/ {
            proxy_pass http://10.10.30.93:9043;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }

        # Redirect /newpath2/account4/steampipe to the new IP address and port 9196
        location /Foradian/account3/steampipe/ {
            proxy_pass http://10.10.30.93:9196;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
    }
}

 
/opt/powerpipedata/{customer}
==========================================='''''==========================
- Check data peristence on container-
- Test by attaching a data volume to the container and check for persistence of data - 
/opt/powerpipedata/{customer}


sudo docker run -d --name myaccontainer1 \
  --network my-networkOptit123 \
  -p 9033:9033 \
  -p 9194:9194 \
  -p 9040:9040 \
  -e AWS_ACCESS_KEY_ID=AK123 \
  -e AWS_SECRET_ACCESS_KEY=746vw \
  -v /home/user/.steampipe:/home/powerpipe/.steampipe \
  -v /var/log/steampipe:/home/powerpipe/.steampipe/logs \
  powerpipe__steampipe_image
 ```

=========================================================================================================================================================
# docker volume and notes on 3 type of volume


Check data peristence on container
- Test by attaching a data volume to the container and check for persistence of data
 
===================================================================
 Docker provides three main types of mounts: **bind mounts**, **volumes**, and **tmpfs mounts**. Each has its specific use case and advantages depending on your requirements for data persistence, isolation, and performance.

### 1. **Bind Mounts**
   - **Description**: With bind mounts, a file or directory on the host machine is mounted directly into the container. The file or directory must already exist, and Docker does not manage it.
   - **Usage**: Bind mounts are useful when you need to access and modify data on the host from within the container, such as when you want to share data between the host and the container.
   - **Example**:
     ```bash
     docker run -v /path/on/host:/path/in/container my_image
     ```
   - **Pros**:
     - Gives direct access to host file system.
     - Good for sharing configurations or working directories between host and container.
   - **Cons**:
     - No Docker management of files.
     - Less portable (specific to the host's directory structure).

---

### 2. **Volume Mounts**
   - **Description**: Docker volumes are the most common type of mount used for persisting data. Unlike bind mounts, Docker manages volumes, and they are created and stored outside of the container’s writable layer. You don’t need to specify an exact path; Docker abstracts the location.
   - **Usage**: Volumes are ideal for persisting and sharing data between containers or ensuring that data lives beyond the lifecycle of the container.
   - **Example**:
     ```bash
     docker run -v my_volume:/path/in/container my_image
     ```
   - **Pros**:
     - Managed by Docker (easy to back up, move, or delete).
     - Data is stored outside the container’s lifecycle.
     - Can be used across multiple containers.
   - **Cons**:
     - Slightly more overhead compared to bind mounts.
     - Requires Docker management commands for manipulation.

---

### 3. **Tmpfs Mounts**
   - **Description**: Tmpfs mounts are used for storing non-persistent data in memory (RAM). The data disappears after the container stops. It's best for situations where performance is more critical than persistence, and the data doesn't need to be saved.
   - **Usage**: Tmpfs is ideal for caching, storing temporary files, or other use cases where data only needs to exist while the container is running.
   - **Example**:
     ```bash
     docker run --mount type=tmpfs,destination=/path/in/container my_image
     ```
   - **Pros**:
     - Extremely fast (data is stored in memory).
     - No disk I/O overhead.
   - **Cons**:
     - Data is lost when the container stops.
     - Limited by available RAM on the host.

---

### Summary of Use Cases:

- **Bind Mounts**: Use when you need direct access to specific files or directories on the host system. Useful in development environments where you want to share files between the host and the container.
- **Volume Mounts**: Use when you want persistent, isolated storage for your containers. Ideal for production environments where you need long-term data persistence and Docker-managed storage.
- **Tmpfs Mounts**: Use when you need temporary, high-speed storage for data that doesn’t need to persist after the container stops.

---

If you are testing **data persistence**, you should be using **volume mounts** as they ensure that data persists beyond the container lifecycle, unlike **tmpfs mounts** (temporary) or **bind mounts** (less isolated and harder to manage).

====================================================================
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

we can now follow same for oher services
