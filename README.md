<h1>Cowrie Honeypot</h1>

<h2>Description</h2>
The project consists of creating an AWS Ubuntu server to run a honeypot called Cowrie. Install the Cowrie honeypot onto a server allows you to see if someone accesses the honeypot. It will also allow you to see what commands or programs a threat actor executes to gain information in real-time.
<br />


<h2>Languages and Utilities Used</h2>

- <b>Bash commands</b> 
- <b>AWS</b>

<h2>Environments Used </h2>

- <b>Ubuntu</b>
- <b>Linux</b>
- <b>Parrot</b>

<h2>Walk-through:</h2>

<b>Create the AWS server using this guide:</b>

Guide: [AWS server]()

My settings:
  - Application and OS Images: Ubuntu Server 20.04 LTS (HVM), SSD Volume Type
  - Key Pair: Use the key that you create and download
  - Network Settings: Allow SSH traffic from --> My IP
  - Configure Storage: Up to 30 GB

*These settings will allow you to run the AWS Server for free!!!
<br/>
<br/>
<br/>
<b>Create 2 virtual machines:</b>

Guide: [Create Virtual Machine Using VirtualBox]()

One virtual machine will be used to configure Cowrie.
  - Kali Linux

The other one will be used to pretend to be a threat actor and SSH into the Cowrie Honeypot.
  - Parrot OS

*You can use any Ubuntu OS you want.
<br/>
<br/>
<br/>
<b>Connect to AWS Server:</b>

Drag and dropped the pair key into Kali Linux.

Open the terminal and go into the directory that the pair key is in:
```bash
cd /home/user/Downloads
```

On the AWS dashboard click the instance --> Connect --> under the SSH client tab --> copy the Example ssh code.

Should look something like this:
```bash
ssh -i "key.pem" ubuntu@ec2-18-117-125-88.us-east-2.compute.amazonaws.com
```

When you run a brand new server you always want to update it:
```bash
sudo apt-get update
```

We will be using git to install certain repositories, so we will have to install it.
```bash
sudo apt install git
```
<br/>
<b>Set up Cowrie:</b>

Go into root:
```bash
sudo su
```

Install support for Python virtual environments and dependencies:
```bash
sudo apt-get install git python3-virtualenv libssl-dev libffi-dev build-essential libpython3-dev python3-minimal authbind virtualenv python3-venv
```

Add cowrie user:
```bash
sudo adduser --disabled-password cowrie
```

Go into Cowrie user:
```bash
sudo su - cowrie
```

Clone Cowrie repository:
```bash
git clone http://github.com/cowrie/cowrie
```

Go into Cowrie folder:
```bash
cd cowrie
```

Setup virtual environment, activate it, and download packages:
```bash
python -m venv cowrie-env
source cowrie-env/bin/activate
python -m pip install --upgrade pip
python -m pip install --upgrade -r requirements.txt
```
Change Cowrie config files so
