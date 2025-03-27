
Lab for Create a Website on S3

Create an Amazon Simple Storage Service (Amazon S3) bucket.

Create a new AWS Identity and Access Management (IAM) user that has full access to the Amazon S3 service.

Upload files to Amazon S3 to host a simple website for the Café & Bakery.

Create a batch file that can be used to update the static website when you  change any of the website files locally.

Run AWS CLI commands that use IAM and Amazon S3 services.

Deploy a static website to an S3 bucket.

Create a script that uses the AWS CLI to copy files in a local directory to Amazon S3.


These instructions are for Mac/Linux users only. If you are a Windows user, skip ahead to the next task.

Read through the three bullet points in this step before you start to complete the actions, because you will not be able see these instructions when the Details panel is open.

Click on the Details drop down menu above these instructions you are currently reading, and then click Show. A Credentials window will open.

Click on the Download PEM button and save the labsuser.pem file.

Then exit the Details panel by clicking on the X.

Open a terminal window, and change directory cd to the directory where the labsuser.pem file was downloaded.

For example, run this command, if it was saved to your Downloads directory:

cd ~/Downloads
Change the permissions on the key to be read only, by running this command:

chmod 400 labsuser.pem
Return to the AWS Management Console, and in the EC2 service, click on Instances. Check the box next to the instance you want to connect to.

In the Description tab, copy the IPv4 Public IP value.

Return to the terminal window and run this command (replace <public-ip> with the actual public IP address you copied):

ssh -i labsuser.pem ec2-user@<public-ip>
Type yes when prompted to allow a first connection to this remote SSH server.

Because you are using a key pair for authentication, you will not be prompted for a password.


 

Task 2: Configure the AWS CLI
NOTE: Unlike some other Linux distributions that are available through Amazon Web Services (AWS), Amazon Linux instances already have the AWS CLI pre-installed on them.

Update the AWS CLI software with the credentials.

aws configure
At the prompts, enter the following information:

AWS Access Key ID: Click on the Details drop down menu above these instructions, and then click Show. Copy the AccessKey value and paste it into the terminal window.

AWS Secret Access Key: Copy and paste the SecretKey value from the same Credentials screen.

Default region name: us-west-2

Default output format: json

 

Task 3: Create an S3 bucket by using the AWS CLI
Open the AWS CLI documentation for S3api.

Tip: In this activity, you will sometimes use the s3api command, and other times you will use the s3 command. s3 commands are built on top of the operations that are found in the s3api commands.

Use the aws s3api create-bucket command to create a bucket in Amazon S3. The bucket must have a unique name, such as the combination of your first initial, last name, and three random numbers. For example, jsmith256 would be a good bucket name if your name is Jane Smith.

Specify --region us-west-2 because you want to host the website in the AWS Region that is closest to the people who are most likely to access it (in the London area).

You must add

--create-bucket-configuration LocationConstraint=us-west-2 

--object-ownership BucketOwnerPreferred

If the command is successful, you will get a JavaScript Object Notation (JSON)-formatted response with a Location name-value pair, where the value reflects the bucket name.

 

Task 4: Create a new IAM user that has full access to Amazon S3
Using the AWS CLI, create a new IAM user with the name: awsS3user

Tip: Use the AWS CLI documentation as needed to figure out the command you must run. Like the previous command that you ran, you will get a JSON-formatted response if the command is successful.

Create a login profile for the new user by using the following command:

aws iam create-login-profile --user-name awsS3user --password Training123!
Copy AWS the account number. To do this:

In the AWS Management Console you opened earlier in this activity, click on the voclabs/user... drop down menu in the upper right of the screen.

Copy the account number that displays. It will be a 12 digit number with dashes in it.

Still in the drop down menu, choose Sign Out.

Log in to the AWS Management Console as the new awsS3user user. To do this:

In the browser tab where you just signed out of the AWS Management Console, click the Sign in to the Console button. Note: Due to console changes, you may also see Log back in as an option. 

You will be taken to a sign in screen.

Select IAM user

In the text field, enter awsS3user

Click Next.

You should see a new login screen with an Account ID or alias field. The field should already display the account number. If it does not display the account number, paste in the account number that you copied a moment ago (but remove the dashes after you paste it in).

Click Next

For IAM user name, enter awsS3user

For Password, enter Training123!

Click Sign In

In the AWS Management Console, select the  Services menu, and then select S3 under Storage.

The Amazon S3 service page will display with an error message that states Access denied. This behavior is expected because the new user has not been granted any
rights. Leave this page open because you will return to it again soon.

Back in the terminal window, find the AWS managed policy that grants full access to Amazon S3. Run this command to find it:

aws iam list-policies --query "Policies[?contains(PolicyName,'S3')]"
The result set displays policies that have a PolicyName attribute containing the term S3 somewhere in it. Locate the one that grants full access to Amazon S3. You will use it in the next step.

Grant the awsS3user full access to the S3 bucket by using the following command:

aws iam attach-user-policy --policy-arn arn:aws:iam::aws:policy/<policyYouFound> --user-name awsS3user
Replace <policyYouFound> in the command above with the PolicyName value of the appropriate policy that you found by using the command in the previous step.

Return to the AWS Management Console and refresh the browser tab.

The Access denied error should go away, and you should now see the bucket that you created by using the AWS CLI earlier in this activity.

 

Task 5: Extract the files that you need for this activity
Back in the SSH terminal, extract the files that you need for this activity by running the following commands:

cd ~/sysops-activity-files
tar xvzf static-website-v2.tar.gz
cd static-website
Run the ls command to confirm that the files extracted correctly. You should see an index.html file and directories named css and images.

Delete the tar.gz file. Use the Linux rm command, but leave the extracted files.

 

Task 6: Upload files to Amazon S3 by using the AWS CLI
Run the following command to prepare the bucket that you created earlier to function as a website. This process will ensure that index.html will be understood to be the index document.

NOTE: Be sure to replace <my-bucket> in the command below with your actual bucket name.

aws s3 website s3://<my-bucket>/ --index-document index.html
Allow objects in the bucket to be public

aws s3api delete-public-access-block --bucket <my-bucket>
Upload the files to the bucket Replace <my-bucket> with the actual bucket name.

aws s3 cp /home/ec2-user/sysops-activity-files/static-website/ s3://<my-bucket>/ --recursive --acl public-read
Notice that the upload command includes an access control list (ACL) parameter that specifies that the uploaded file should be available to the public to read. It also includes the recursive parameter, which indicates that all files in the current directory on your machine should be uploaded.

Verify that they were uploaded (again, replace <my-bucket> with the actual bucket name):

aws s3 ls <my-bucket>
Back in the AWS Management Console browser window, in the S3 service area, click your bucket name.

Click the Properties tab. In the Static website hosting area, note that Bucket hosting has been enabled, which resulted from running the aws s3 website AWS CLI command.

Click the Bucket hosting link, and then click the Endpoint URL that displays.

Congratulations, you have created a static website that is available to the public for viewing!

 

A business request from Café—Launch a website
cafe scene2

Nikhil showed Sofîa the new static website, and she was impressed. Give yourself a pat on the back—nice job!

Martha and Frank were impressed as well. However, it did not take long before they started requesting changes. Martha requested a change to the background color that appears behind the cookies, coffee, and tarts in the second row of photos. Sofîa, realizing this might just be the first of many change requests, decides to make updating the website into a batch process by using her programming skills. In this last part of the activity, you take on the role Sofîa and work on introducing automation into the process of updating the website.

 

Task 7: Create a batch file to make updating the website easily repeatable
In the terminal window, pull up the history of recent commands by using the following command:

history
Locate the line where you ran the aws s3 cp command. You will put this line in your new batch file soon.

To change directories and create an empty file, run the following commands in the SSH terminal session:

cd ~
touch update-website.sh
Open the empty file in the VI editor.

vi update-website.sh
To enter edit mode in VI, press A.

Add the standard first line of a bash file, and then add the s3 cp line from your history (replace <my-bucket> with your actual bucket name):

#!/bin/bash
aws s3 cp /home/ec2-user/sysops-activity-files/static-website/ s3://<my-bucket>/ --recursive --acl public-read
Write the changes and quit the file by pressing ESC, typing :wq and then pressing ENTER.

Make it an executable batch file.

chmod +x update-website.sh
Open the local copy of the index.html file in a text editor.

vi sysops-activity-files/static-website/index.html
Modify the file as described:

Locate the first line that has the HTML code bgcolor="aquamarine" and change it to bgcolor="gainsboro"

Locate the line that has the HTML code bgcolor="orange" and change it to bgcolor="cornsilk"

Locate the second line that has the HTML code bgcolor="aquamarine" and change it to bgcolor="gainsboro"

Save the changes.

Run your batch file to update the website.

./update-website.sh
NOTE: The command line output should show that the files were copied to Amazon S3.

Return to the browser and refresh the Café page to see the changes to the website.

Congratulations, you just made your first revision to the website!

You now also now have a tool (the script that you created) that makes it easy to push changes from your website source files to Amazon S3.
