If you have two users on the same Linux VM and you want each user to manage different AWS accounts, you can configure AWS profiles separately for each user. Here’s how you can do it:
1. Create Two Users on the Same Linux VM:
If you haven't already created the two users, you can do so with the following commands:
bashCopy codesudo adduser user1
sudo adduser user2
2. Configure AWS Profiles for Each User:
Each user will have their own home directory, so they can configure their AWS CLI independently.
For user1:
Switch to user1:
Configure AWS for user1:
 
This will create the AWS credentials file in /home/user1/.aws/credentials and /home/user1/.aws/config.
Export the Profile for user1:
For user2:
Switch to user2:
Configure AWS for user2:
 
This will create the AWS credentials file in /home/user2/.aws/credentials and /home/user2/.aws/config.
Export the Profile for user2:
3. Using the Profiles:
Now, when user1 runs AWS commands, they will use the account1 profile, and user2 will use the account2 profile.
For user1:
bashCopy codeaws s3 ls --profile account1
For user2:
bashCopy codeaws s3 ls --profile account2
4. Maintaining Separate Environments:
Each user will have separate environments. If user1 exports AWS_PROFILE=account1, it will only affect user1's session. Similarly, user2 can export AWS_PROFILE=account2 without affecting user1's session.
5. Switching Between Users:
You can switch between the users using su:
Switch to user1:
Switch to user2:
Each user's AWS configuration remains separate, allowing them to manage different AWS accounts without interfering with each other.
6. Persistent Configuration:
If you want the AWS profile to be automatically set when a user logs in, you can add the export command to the user's .bashrc or .zshrc file.
For user1:
bashCopy codeecho 'export AWS_PROFILE=account1' >> ~/.bashrc
For user2:
bashCopy codeecho 'export AWS_PROFILE=account2' >> ~/.bashrc
 
bashCopy codesu - user2
bashCopy codesu - user1
bashCopy codeexport AWS_PROFILE=account2
bashCopy codeaws configure --profile account2
bashCopy codesu - user2
bashCopy codeexport AWS_PROFILE=account1
bashCopy codeaws configure --profile account1
bashCopy codesu - user1
 
 
