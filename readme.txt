Task 1: Create an Amazon S3 Bucket
Every object in Amazon S3 is stored in a bucket.
In this task, you will create a bucket in Amazon S3 to store your data and your website.

In the AWS Management Console, select the  Services menu, and then select S3 under Storage.

Click Create bucket then configure: Bucket name, enter websiteNUMBER

Replace NUMBER with a random number

Copy the name of your bucket to a text editor

Region: US West (Oregon) us-west-2

For Object Ownership, choose ACLs enabled

Click Create bucket

Every Amazon S3 bucket must have a unique name.

 If you receive an error stating The requested bucket name is not available, then click the top Edit link, change the bucket name and try again until it works.
Once Amazon S3 creates your bucket, the console displays your bucket name in the list of buckets.

 

Task 2: Upload Files
You can now upload files to your bucket. A set of files have been provided for this lab.

Download this file: Sample_files

On your computer, unzip sample-files.zip. It should create a folder with several files.

In the Amazon S3 console, click the website bucket you created.

Click Upload

Click Add files
A file selection dialog box will appear.    

Browse to the sample-files folder that you created from the zip file.

Select all of the files and click Open (for Windows) or Choose (for Mac).

Click Upload
The files will upload to your Amazon S3 bucket.

 

Task 3: Granting Access to the Bucket
Your files have now been uploaded as objects in your Amazon S3 bucket. The term object is used because files are stored in a filesystem but Amazon S3 is actually an object storage system. While it behaves similar to a filesystem on a computer, it actually has many more capabilities.

 Objects in Amazon S3 are private by default. You will now test that this is true.

Click the name of the aws-logo.png object.
Information about the object will appear on the screen. This information includes:

Etag: An MD5 checksum that can be used to confirm that the object was uploaded correctly.

Storage class: Indicates durability and cost of storing the object.

Server side encryption: Whether the object is encrypted while stored on Amazon S3.

Size: Size of the object being stored.

Object URL: A link to the object on the Internet. Notice that it includes the name of your bucket.

Copy the Object URL to your clipboard.

Open a new browser tab, paste the Object URL link and press Enter.
You should receive an Access Denied message. This is good! It indicates that your data remains private on Amazon S3.
However, when deploying a website you will want your data to be fully accessible on the Internet. You can accomplish this by creating a Bucket Policy that defines who can access specific parts of your bucket. Bucket policies can restrict access only to specific directories, from specific IP addresses, during specific times and even only to specific users.

Keep the Access Denied tab open (you will use it again soon).

In the S3 Management Console, at the top of the screen, click Amazon S3.

Click your website bucket.

Click the Permissions tab. The Block public access section will be used change the permission of the object.

In the Block public access (bucket settings) section, Click Edit to change the settings.

Deselect the Block all public access option, and then leave all other options deselected.

 Notice all of the individual options remain deselected. When deselecting all public access, you must then select the individual options that apply to your situation and security objectives. In a production environment, it is recommended to use the least permissive settings possible.

Click Save changes

A dialogue box opens asking you to confirm your changes. Type confirm in the field, and then click Confirm

In the Bucket Policy  panel, choose Edit to open the Policy Editor.

Copy and paste this policy into the Bucket policy editor (but don’t save yet):

{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadGetObject",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::BUCKET/*"
    }
  ]
}
In the policy, replace BUCKET with the name of your bucket. (Keep the /* in place after your bucket name!)
The policy now Allows anyone (indicated by Principal: *) to download an object (GetObject) from your bucket.

Click Save changes

 If you receive an error when saving, it might be due to a mismatch with your bucket name. Make sure the bucket name in the Resource line matches the name of the bucket your created earlier (shown at the top of the window).

You can now test that the objects are accessible.

Return to the browser tab that previously showed Access Denied.

Refresh the web page.

The AWS logo should appear, proving that your Bucket Policy now grants public access to your Amazon S3 bucket.

Close the browser tab with the logo and return to the tab with the Amazon S3 Management Console.

 

Task 4: Configuring Static Website Hosting
Amazon S3 also has the ability to configure a bucket for Static Website Hosting. This adds several capabilities:

You can define a default page that should be displayed when users access your website but do not specify a particular HTML page

You can define an error page that appears if users navigate to a page that does not exist

You can define redirections to send users to alternate pages at different locations

You will now activate Static Website Hosting and configure it to use the index.html and error.html pages that were included in your upload.

Click the Properties tab at the top of the window.

Under Static website hosting click Edit.

Click  Enable, and then configure:

Index document: index.html (You will need to enter these values even though they are already displayed as hints.)

Error document: error.html

Click Save changes

Scroll to Static website hosting again.

Click the Endpoint link.

This is a link to your new website. It should look like: http://website-XXX.s3-website-us-west-2.amazonaws.com

The index.html page is automatically displayed.

 You will also see an error message on the page. This is okay! You still have some configuration to perform.



Within the error message window, click OK

Next, you can test the error.html page.

Click the address bar in your browser and type some extra characters to go to a page that does not exist, eg: http://website-XXX.s3-website-us-west-2.amazonaws.com/wrong

You will see a the error.html page that says We're sorry. This error page is also known as a 404 page because Error 404 means Page Not Found.



You now have a few final steps to configure your website!

 

Task 5: Enabling Cross-Origin Resource Sharing
Your Amazon S3 bucket can also be accessed programmatically. This allows the objects in your bucket to be shown within web pages without users even knowing that they are coming from Amazon S3.

To demonstrate this concept, the index.html page that you uploaded uses JavaScript to dynamically read the contents of your bucket and display a list of objects to your visitors. To do this, you will need to enable cross-origin resource sharing (CORS). A browser would normally block JavaScript from allowing requests to read the contents of your bucket, but with CORS you can configure the bucket to explicitly permit JavaScript to do this.

To configure the bucket to allow cross-origin requests, you define a CORS configuration, which is an XML document with rules that identify the origins that are permitted to access the bucket, the operations (HTTP methods) it will support for each origin, and other operation-specific information.

 Cross-origin resource sharing (CORS) defines a way for client web applications that are loaded in one domain to interact with resources in a different domain. With CORS support, you can build rich client-side web applications with Amazon S3 and selectively allow cross-origin access to your Amazon S3 resources.

Keep the We're Sorry tab open (you will use it again soon).

Return to your Amazon S3 Management Console tab.

Click the Permissions tab at the top of the page.

Click Edit under Cross-origin resource sharing (CORS)

Paste this policy into the editor:

[
    {
        "AllowedHeaders": [
            "*"
        ],
        "AllowedMethods": [
            "GET"
        ],
        "AllowedOrigins": [
            "*"
        ],
        "ExposeHeaders": [],
        "MaxAgeSeconds": 3000
    }
]
This is a basic and very permissive CORS configuration, but Amazon S3 supports a wide array of configuration options.

Click Save changes

There is one final step required, which is to permit programmatic listing of your bucket.

Navigate to Access Control List (ACL) pane.

Click Edit and configure the following:

For Everyone (public access) enable  List and  Read

Check the box  I understand the effects of these changes on my objects and buckets.

Click Save changes

 A warning will appear, saying This bucket will have public access. This can be ignored because you are intentionally adding public access.

You can now test your application!

Return to the We're Sorry tab in your web browser and click the try visiting our home page link to return to the application.

 If you can't find the tab, click the Endpoint URL shown in the Static website hosting section of the Properties tab.

The site will look something like this:



The page includes documentation for the application and the bottom of the page lists the objects stored in your Amazon S3 bucket.

Understanding the Website
Feel free to explore the source code of the index.html file. It is a static HTML file, but it is made dynamic through the use of several JavaScript plugins and custom JavaScript that displays the contents of an Amazon S3 bucket in an HTML table. The JavaScript includes comments to help you understand the application.

While this lab doesn’t require you to know how to write JavaScript, HTML, or CSS, listed below are a few interesting aspects of the index.html file.

The AWS SDK for JavaScript
The index.html page uses the AWS SDK for JavaScript in the browser to dynamically query the contents of an S3 bucket.

The JavaScript SDK makes it very simple to list the objects in an S3 bucket. The code:

Links to the AWS JavaScript SDK.

Configures the SDK with a default AWS region of us-west-2.

Creates and initializes an Amazon S3 object.

Via the Amazon S3 object, makes an unauthenticated listObjects call to Amazon S3.

Accumulates the resulting object listings.

Calls listObjects repeatedly as needed if there are more results to come.

To keep things simple, the code does not ask for credentials when querying a bucket. Instead, it makes unauthenticated calls to the Amazon S3 API. This means that it will only work against buckets that are publicly-readable. This is why you to set the permissions of your bucket to allow anyone to list the contents of the bucket.

The last point on the list above is worth noting because listObjects will only return 1,000 objects at a time. Due to this, the JavaScript code will continually query a bucket to see if there are more objects to retrieve. The code has been tested on buckets with over 10,000 objects and encountered no issues, but it may overwhelm your browser if you try to display the contents of a bucket with a very large amount of objects (say, over one million objects).

The JavaScript only queries for objects at one level of the bucket at a time. That is, it will only look for objects in the root of your bucket and will not look for objects in folders unless you click into them.
