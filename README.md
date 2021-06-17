# The Cloud
## What is Cloud Computing?
- Cloud computing is the on-demand delivery of IT resources over the Internet with pay-as-you-go pricing. 
- Instead of buying, owning, and maintaining physical data centers and servers, you can access technology services, such as computing power, storage, and databases, on an as-needed basis from a cloud provider.

** Providers of Cloud Services **
- AWS
- Microsoft Azure
- Google cloud

** Who uses cloud services **
- Netflix
- Facebook
- Spotify

** Benefits **
1. Flexibility
2. Robustness
3. Cost-effectiveness
4. Ease of use

## What is AWS?
- AWS = Amazon Web Services
- A cloud computing platform from Amazon who provide services such as
  - **SaaS (Software as a service):** referring to end-user applications (such as email, office 365). You don’t have to think about how the service is maintained or management of underlying infrastructure. You only need to think about how you will use that particular software.  
  - **IaaS (Infrastructure as a service):** provides access to networking features, computers (virtual or on dedicated hardware), and data storage space.
  - **PaaS (Platform as a service):** manages underlying infrastructure (usually hardware and operating systems), you can focus on deployment and management of your applications. You don’t need to worry about resource procurement, capacity planning, software maintenance, patching.


## Create EC2 instance
On AWS you want to be able to deploy a node app, you will do this by creating an EC2 instance.
1. Login to AWS
2. Choose EC2 inside AWS
3. Find Ubuntu Server 16.04 LTS (HVM), SSD Volume Type and select it (must be 64-bit (x86))
4. Under Type column, select `t2.micro`
5. Click `Next: Configure Instance Details`
6. For `Network` leave as default, for `Subnet` select `DevOpsStudents subnet` and for `Auto-assign Public Ip` select enable.
7. Click `Next: Add Storage`
8. Enter amount of storage required (I did 8gb).
9. Click `Next: Add Tags`
10. Add your tags, for `Key` I did Name, for Value I did devops_bootcamp_jaspreet_app
11. Click `Next: Configure Security Group`
12. Click `Create a new security group`, Enter a name I did `devops_bootcamp_securitygroup_jaspreet.
13. Enter the inbound rules: `SSH: Port-22: Source-IP`, next rule `HTTP: Port-80 Source-Anywhere`, Last rule `Custom Rule TCP: Port-3000 : Source-Anywhere`
14. Click `Review and Launch` this will launch your instance.

## Deploy Node App
1. On AWS -> go to instances -> find your instances -> select it ->instance state should be : running -> select connect button
2. Under SSH client column -> in step 3 -> copy `chmod 400 devop_bootcamp.pem` (this is the key file, which we want to change the permissions for so it is readable by the person who owns it)
3. Open Git Bash -> go into `cd ~/.ssh` .ssh folder -> paste command from step 2.
4. Back in SSH client -> in step 4 -> copy the command `ssh -i "devop_bootcamp.pem" user@awsVirtualMachineLink.com` 
Step 4 is basically to SSH into your VM (open connection to server at your IP using your username and password provided by SSH key) using the ssh command given by AWS.
5. Once you have ssh'd into your VM run the following commands:
   - `sudo apt-get update -y`
   - `sudo apt-get upgrade -y`
   - `sudo apt-get install nginx`
   - `sudo apt-get install python-software-properties`
   - `curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -`
   - `sudo apt-get install nodejs -y`
   - `sudo npm install pm2 -g`
   - `cd app`
   - `npm install`
6. Now exit out of your machine by running `exit` 	
7. Need to transfer data from Machine to AWS EC2 instance so run the command `scp -i ~/.ssh/devop_bootcamp.pem -r c/Users/user/app user@vmLink.com:/home/ubuntu/`
   You have your file_name.pem after the -r you have your local directory where app folder is stored, followed by ec2@ec2_ip... link:folowed by path to remote directory.
8. SSH back in -> go into `cd home/ubuntu/app`
9. Run `node app.js`
10. Check status of nginx `systemctl status nginx` make sure it running if not try `systemctl restart nginx`
11. Get your Public IP which is in your instance-> EC2 Instance Connect tab under Public IP
12. Paste IP in browser followed by :3000

## Create a Reverse Proxy
If you want to be able to load the app without the 3000 port follow the steps
1. You should still be SSH into your VM.
2. Go into this folder `cd /etc/nginx/sites-available` run `ls` see if there is an existing file.
3. If default file exits you want to remove it `sudo rm -rf default`
4. Create a new default file with `sudo nano default` within the folder `/etc/nginx/sites-available`
5. Next change the reverse proxy setting to redirect traffic from port 3000 to default port 80
`server {
    listen 80;

    server_name _;

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
 }`
- Replace local host in the http line with your Public IP

## Create provision.sh file in app folder
1. Locate app folder (c/Users/user/app) and go into id `cd app`
2. Create a provision script `sudo nano provision.sh`
3. Copy all the sudo commands from `step 5 of deploy Node App`

