# Configure-Portspoof
How to configure portspoof (Debian)

### What is Portspoof?
Portspoof is a tool that spoofs ports to make it seem like they are open, when in reality they are not. It can slow down an attacker tremendously. It will not only spoof the ports, but will also spoof fake services to make the ports seem more legit. This tool is from Black Hills Information Security (BHIS), and i  have no affiliation, nor have I assisted in he development in the tool. All credit goes to them. That being said, there is not much information out there on how to setup and keep this tool running on your network.

#### Portspoof setup
```bash
sudo git clone https://github.com/drk1wi/portspoof.git
sudo ./configure && make && sudo make install
```

#### Script for Cron
```
#!/bin/bash

/opt/portspoof/src/portspoof -c /opt/portspoof/tools/portspoof.conf -s /opt/portspoof/tools/portspoof_signatures -D
```

#### Setup Cron (have it set for reboot & every 5 minutes because the daemon tends to die sometimes)
```
@reboot              /opt/portspoof/src/start_portspoof.sh
*/5 * * * *          /opt/portspoof/src/start_portspoof.sh
```

#### iptables rule (leave port 22 out so that you will be able to ssh into the machine)
```
sudo iptables -t nat -A PREROUTING -i eth0 -p tcp -m tcp -m multiport --dports 1:21,23:65535 -j REDIRECT --to-ports 4444
```

#### Install iptables-persistent & save rule
```
sudo apt install iptables-persistent
sudo iptables-save > /etc/iptables/rules.v4 (may have to run as root without sudo)
```
After doing this a SNAT policy will need to be setup through your firewall to route traffic to your portspoof device.
For safe measures setup a policy to deny ssh from outside your network to your portspoof device.                                                              You can also segment the device from your network if you wish.
