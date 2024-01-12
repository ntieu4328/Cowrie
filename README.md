<h1>Cowrie Honeypot</h1>

<h2>Description</h2>
The project consists of creating an AWS Ubuntu server to run a honeypot called Cowrie. Installing the Cowrie honeypot onto a server allows you to see if someone accesses the honeypot. It will also allow you to see what commands or programs a threat actor executes to gain information in real-time.
<br />


<h2>Languages and Utilities Used:</h2>

- <b>Bash</b> 
- <b>AWS</b>

<h2>Environments Used:</h2>

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

Drag and drop the pair key into Kali Linux.

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
We will be using git to install certain repositories, so we will have to install it:
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
python3 -m venv cowrie-env
source cowrie-env/bin/activate
python -m pip install --upgrade pip
python -m pip install --upgrade -r requirements.txt
```
<br/>
<b>Change Cowrie config files:</b>

Go into the folder with the config file:
```bash
cd etc
```
copy the .dist config file to a .cfg file:
```bash
cp cowrie.cfg.dist cowrie.cfg
```
The cowrie.cfg.dist can be overwritten by upgrades while the cowrie.cfg will not. This is why we copy the cowrie.cfg.dist and name the copy cowrie.cfg.

Go into terminal to edit the cowrie.cfg file:
```bash
nano cowrie.cfg
```
Change hostname to a different name of your choosing. Scroll down to [telnet] and change false to true.

<br/>
<br/>
<b>Reroute SSH and Telnet to different ports:</b>

Go back to root user:
```bash
exit
```
Reroute traffic on port 22 (SSH) to port 2222:
```bash
sudo iptables -t nat -A PREROUTING -p tcp --dport 22 -j REDIRECT --to-port 2222
```
Reroute traffic on port 23 (Telnet) to port 2223:
```bash
sudo iptables -t nat -A PREROUTING -p tcp --dport 23 -j REDIRECT --to-port 2223
```

</br>
<b>Start Cowrie:</b>

Go back into Cowrie user:
```bash
sudo su - cowrie
```
Go into folder to start cowrie:
```bash
cd cowrie
```
Start cowrie:
```bash
bin/cowrie start
```

</br>
<b>Watch log file in real time:</b>

Go to cowrie log folder:
```bash
cd var/log/cowrie
```
Read the log file:
```bash
tail -f cowrie.log
```

</br>
<b>Connect threat actor to cowrie honeypot:</b>

Now we will pretend that the other virtual machine we made is a threat actor trying to gain access to our network. After the threat actor scans the network and finds that there is a server that has SSH and Telnet open they try to connect to it. It would look like this:

![cowrie threat actor](https://github.com/ntieu4328/Cowrie/assets/156137990/ec93b6c9-f334-4b92-8380-cee32896c09c)

You can see that the threat actor SSH's into the Cowrie server as root and then runs ls to see if there's any information that they can extract. On our end we can see what the threat actor is doing live on our Cowrie honeypot server. It would look like this:

![Threat actor observation](https://github.com/ntieu4328/Cowrie/assets/156137990/8abeef1a-0dd8-4497-ad4d-1c8dd7647b12)

You can see when the threat actor SSH's into the Cowrie server and what commands they run.
