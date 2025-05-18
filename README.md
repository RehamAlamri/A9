1) Go to AWS Console → S3
2) Click “Create Bucket ”
3) Set:
- Name: sad2018-clarusway-assets
- Region: eu-north-1
4) Scroll down and Uncheck the “Block all public access” box
5) Click Create bucket
Enable Website Hosting:
6) Go to your new bucket → Click “Properties”
7) Scroll to “Static Website Hosting” → Click Edit
8) Turn it ON
9) Set:
a. Index document: index.html
10) Save changes
Upload Your Website Files:
11) Go to “Objects” tab
12) Click Upload → Select:
a. index.html
b. logo.png
c. sda.png
13) Complete the upload
Make Files Public:
14) Go to the Permissions tab
15) Scroll to Bucket Policy → Click Edit
16) Paste this code, replacing the bucket name:
{
"Version": "2012-10-17",
"Statement": [
{
"Effect": "Allow",
"Principal": "*",
"Action": "s3:GetObject",
"*/اسم البكت حقك:::Resource": "arn:aws:s3"
}
]
}
17) Click Save
18) Test it
http://sda2018-clarusway-assets.s3-website.eu-north-1.amazonaws.com
19) Run this command in and take a screenshot
curl -I http://sda2018-clarusway-assets.s3-website.eu-north-1.amazonaws.com/
20) Go to AWS → EC2 → Launch Templates → Click Create
Fill in:
a. Name: clarusway-launch-template
b. AMI: Amazon Linux 2
c. Instance Type: t3.micro
21) Go to AWS → IAM → Roles → Create Role(or in the template there is an
option to create one, if you didn’t do and made the template you can still
make a new version of the template with these steps and set it to be
default)
22) Select EC2
Attach policy: AmazonS3ReadOnlyAccess
Name: EC2S3ReadOnlyRole
23) Back in Launch Template:
Scroll to Advanced Details
Under IAM Role, select EC2S3ReadOnlyRole
24) Paste this in user data
#!/bin/bash
yum update -y
yum install nginx -y
systemctl start nginx
systemctl enable nginx
aws s3 cp s3://sda2018-clarusway-assets/index.html
/usr/share/nginx/html/index.html
25) Go to EC2 → Auto Scaling Groups → Click Create
Name: clarusway-asg
Select your Launch Template
26) Network :
Choose default VPC
27) Select all subnets
28) Set Capacity:
Min: 1
Desired: 2
Max: 3
29) Leave scaling policy as "No policy" (manual only)
30) While creating ASG → Create New Load Balancer
31) Type: Application Load Balancer
32) Name: clarusway-alb
33) Listener: HTTP (port 80)
34) Scheme: Internet-facing
35) Choose all subnets from earlier
36) Target group:
a. Name: clarusway-tg
b. Type: Instance
c. Health check path: /
