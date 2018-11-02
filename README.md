# Project 10 - Honeypot

Time spent: **12** hours spent in total

> Objective: Setup a honeypot and provide a working demonstration of its features.

### Required: Overview & Setup

- [ ] A basic writeup (250-500 words) on the `README.md` desribing the overall approach, resources/tools used, findings

This assignment is completed by using Google Cloud Platform. In order to gain access, the user must first set up a google cloud account. Google might prompt to ask for credit/debit card information, but that's for ensuring the action is not done by robots. After installing GCP service on terminal, the MHN and honey firewall rules can be configured, and the VM can be installed. After deploying honeypot accordingly, send it test attacks to see if it can capture them.

- [ ] A specific, reproducible honeypot setup, ideally automated. There are several possibilities for this:
  - After getting access to GCP, initialize gcloud by using the command: `gcloud init`
  - Configure the default region to "s-central1" and default zone to "us-central1-c".
  - Set up MHN firewall rules by using the command: 
  	```
	$ gcloud beta compute firewall-rules create mhn-allow-admin --direction=INGRESS --priority=1000 --network=default --action=ALLOW --rules=tcp:3000,tcp:10000 --source-ranges=0.0.0.0/0 --target-tags=mhn-admin
	```
  - Set up the MHN VM by using the command: 
  	```
  	$ gcloud compute instances create "mhn-admin" --machine-type "f1-micro" --subnet "default" --maintenance-policy "MIGRATE"  --scopes "https://www.googleapis.com/auth/devstorage.read_only","https://www.googleapis.com/auth/logging.write","https://www.googleapis.com/auth/monitoring.write","https://www.googleapis.com/auth/servicecontrol","https://www.googleapis.com/auth/service.management.readonly","https://www.googleapis.com/auth/trace.append" --tags "mhn-admin","http-server","https-server" --image "ubuntu-1404-trusty-v20171010" --image-project "ubuntu-os-cloud" --boot-disk-size "10" --boot-disk-type "pd-standard" --boot-disk-device-name "mhn-admin"
	```
  - Take a note on the external IP address, which might be "35.192.133.42".
  - Do the following command to install the MHN admin application:
	```
	$ sudo apt-get update
	$ sudo apt-get install git -y
	$ cd /opt
	$ rm -rf mhn
	$ sudo git clone https://github.com/threatstream/mhn.git
	$ cd mhn
	$ sudo ./install.sh
	```
  - Set up honeypot firewall rules by using the command:
  	```
	$ gcloud beta compute firewall-rules create mhn-allow-honeypot --direction=INGRESS --priority=1000 --network=default --action=ALLOW --rules=all --source-ranges=0.0.0.0/0 --target-tags=mhn-honeypot
	```
  - Set up the MHN VM by using the command:
  	```
	$ gcloud compute instances create "mhn-honeypot-1" --machine-type "f1-micro" --subnet "default" --maintenance-policy "MIGRATE"  --scopes "https://www.googleapis.com/auth/devstorage.read_only","https://www.googleapis.com/auth/logging.write","https://www.googleapis.com/auth/monitoring.write","https://www.googleapis.com/auth/servicecontrol","https://www.googleapis.com/auth/service.management.readonly","https://www.googleapis.com/auth/trace.append" --tags "mhn-honeypot","http-server" --image "ubuntu-1404-trusty-v20171010" --image-project "ubuntu-os-cloud" --boot-disk-size "10" --boot-disk-type "pd-standard" --boot-disk-device-name "mhn-honeypot-1"
	```

### Required: Demonstration

- [ ] A basic writeup of the attack
  - After the VM and firewall are set up, go to the MHN admin console specified by the external IP address in your browser.
  - On the top bar menu, choose "Deploy."
  - Choose Ubuntu - Dionaea with HTTP for script option. By doing so, a Deploy Command will be generated.
  - Try the Deploy Command inside the honeypot VM. When it is finished, go back to MHN console, and choose "Sensors >> View sensors" from the top bar menu. Now you can see all the honeypot you deployed.
  - Finally, attack the honeypot by doing "nmap" demand, and pass it the IP of the honeypot VM. (If nmap is an unrecognized command, then do the following commmand to install nmap: $ sudo apt-get install nmap). You can check all the attacks detected by the honeypot inside the "Attacks" option on the top bar menu from the MHN console.
- [ ] An example of the data captured by the honeypot
![](https://i.imgur.com/2B70gAF.jpg)
- [ ] A screen-cap of the attack being conducted
![](https://i.imgur.com/RgBtzAV.jpg)
