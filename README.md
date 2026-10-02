# OpenClaw-EC2-Amazon-Bedrock-AI---AWS EC2 + VMware SSH Setup
1. Set up VMware Shared Folder

We had the AWS private key on Windows:

bairamov-ai-us-west-2.pem

We configured VMware to share the Windows Downloads folder with the Ubuntu VM.

VMware configuration:

VM Settings
→ Options
→ Shared Folders
→ Always enabled
→ Add
→ Windows Downloads folder

<img width="877" height="512" alt="image" src="https://github.com/user-attachments/assets/c6518d7d-cc7a-4279-9f58-ecc9b47fe83f" />


The shared folder became available inside Ubuntu at:

/mnt/hgfs/Downloads
2. Verify the AWS private key

Inside Ubuntu, we checked:

ls /mnt/hgfs/Downloads

The AWS key was present:

bairamov-ai-us-west-2.pem
3. Mount VMware Shared Folders

The shared folder wasn't initially mounted, so we created the mount directory:

sudo mkdir -p /mnt/hgfs

Then mounted the VMware shared folders:

sudo vmhgfs-fuse .host:/ /mnt/hgfs -o allow_other

After that:

ls /mnt/hgfs

returned:

Downloads
4. Copy the AWS private key

We copied the key from the VMware shared folder into the Ubuntu home directory:

cp /mnt/hgfs/Downloads/bairamov-ai-us-west-2.pem ~/

The key was ultimately available at:

/home/rauf/bairamov-ai-us-west-2.pem
5. Set private-key permissions

AWS SSH requires the private key to have restrictive permissions.

We used:

chmod 400 ~/bairamov-ai-us-west-2.pem

Then verified:

ls -l ~/bairamov-ai-us-west-2.pem

The result showed:

-r-------- 
6. Connect to the EC2 instance

We connected to the Amazon Linux EC2 instance using:

ssh -i ~/bairamov-ai-us-west-2.pem ec2-user@52.36.185.173

The connection was successful.

The server showed:

Amazon Linux 2023

and the prompt changed to:

[ec2-user@ip-172-31-35-217 ~]$

That confirmed we were inside the EC2 server.

7. AWS environment configuration

We configured the AWS environment to use:

AWS_PROFILE=default
AWS_REGION=us-west-2
AWS_DEFAULT_REGION=us-west-2

The AWS profile is:

default

and the AWS region is:

us-west-2
8. Prepare OpenClaw

We then moved on to installing OpenClaw on the EC2 instance.

The installation command we were working with was:

curl -fsSL https://openclaw.ai/install.sh | bash -s -- --no-onboard

We also identified that the correct OpenClaw command is:

openclaw gateway install

not:

openclaw gateaway install
Final Architecture
Windows
   │
   │ Downloads/
   │
   ├── bairamov-ai-us-west-2.pem
   │
   ▼
VMware Shared Folder
   │
   ▼
Ubuntu VM
   │
   ├── /mnt/hgfs/Downloads
   │
   ├── ~/bairamov-ai-us-west-2.pem
   │
   └── SSH
   │
   ▼
AWS EC2
   │
   ├── Amazon Linux 2023
   ├── ec2-user
   ├── us-west-2
   │
   └── OpenClaw
