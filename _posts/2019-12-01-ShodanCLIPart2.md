---
title : "Shodan CLi - Python"
author: Jesse Moore
date: 2019-12-01 08:50:00 +0800
categories: [recon]
tags: [automation]
---

Pre-work:
sudo apt-get update

sudo apt-get upgrade

sudo apt-get install python-setuptools

sudo apt-get install python2.7

sudo apt-get install python-pip

sudo pip install shodan



Code:
Shodan Search

```
import shodan
SHODAN_API_KEY = "putyourownapikeyhereFROMSHODANwebsite"
api = shodan.Shodan(SHODAN_API_KEY)
try:
    #search Shodan
    results = api.search('net:69.91.192.0/24,69.91.193.0/24,69.91.194.0/24,69.91.195.0/24,69.91.196.0/24,69.91.197.0/24,69.91.198.0/24,69.91.199.0/24,69.91.200.0/24,69.91.201.0/24,69.91.202.0/24,69.91.203.0/24,69.91.204.0/24')
 
    #show the results
    #print 'Results found:%s % results'total'
    for result in results['matches']:
        print '%s' % result['ip_str']
        #print result'data'
        #print ''
except shodan.APIError, e:
    print 'Error: %s' % e
```

Use Shodan CLI:
```root@kali:~/shodan# shodan init <PUTYOURSHODANAPIKEYhere>
Successfully initialized
root@kali:~/shodan# shodan download --limit 400 uwbPublic net:69.91.192.0/24,69.91.193.0/24,69.91.194.0/24,69.91.195.0/24,69.91.196.0/24,69.91.197.0/24,69.91.198.0/24,69.91.199.0/24,69.91.200.0/24,69.91.201.0/24,69.91.202.0/24,69.91.203.0/24,69.91.204.0/24
Search query:           net:69.91.192.0/24,69.91.193.0/24,69.91.194.0/24,69.91.195.0/24,69.91.196.0/24,69.91.197.0/24,69.91.198.0/24,69.91.199.0/24,69.91.200.0/24,69.91.201.0/24,69.91.202.0/24,69.91.203.0/24,69.91.204.0/24
Total number of results:    224
Query credits left:     199998
Output file:            COMPPublic.json.gz
 
  [###################################-]   99%  00:00:02
Saved 224 results into file COMPPublic.json.gz
root@kali:~/shodan#
```

```root@kali:~/shodan# shodan download --limit 400 UWB2 net:69.91.205.0/24,69.91.206.0/24,128.208.24.128/25,128.208.50.0/24,128.208.52.0/25,128.208.255.0/24,140.142.14.192/27,140.142.24.192/26,140.142.26.192/26,140.142.158.0/24,140.142.164.0/24
Search query:           net:69.91.205.0/24,69.91.206.0/24,128.208.24.128/25,128.208.50.0/24,128.208.52.0/25,128.208.255.0/24,140.142.14.192/27,140.142.24.192/26,140.142.26.192/26,140.142.158.0/24,140.142.164.0/24
Total number of results:    49
Query credits left:     199996
Output file:            UWB2.json.gz
  [###################################-]   97%  00:00:01
Saved 49 results into file uw2.json.gz
root@kali:~/shodan# ls
```

Shodan CLI using ticks for hostname
```root@kali:~/shodan# shodan download --limit 400 HostnameUWB 'hostname:uwb org:"University of Washington" org:"University of Washington"'
Search query:           hostname:uwb org:"University of Washington" org:"University of Washington"
Total number of results:    108
Query credits left:     199994
Output file:            HostnameUWB.json.gz
  [###################################-]   99%  00:00:01
Saved 108 results into file HostnameUWB.json.gz
root@kali:~/shodan#
```

Shodan CLI to Parse:
```root@kali:~/shodan# shodan parse --fields ip_str,port --separator , uwbPublic.json.gz
69.91.197.32,3283
69.91.197.18,3283
69.91.193.117,500
69.91.202.11,3283
69.91.195.3,3283


root@kali:~/shodan# shodan parse --fields ip_str,port,product,org,os --separator , UWB1.json.gz
69.91.197.32,3283,Apple Remote Desktop,University of Washington,
69.91.197.18,3283,Apple Remote Desktop,University of Washington,
69.91.193.117,500,Microsoft,University of Washington,Windows 8
```

Shodan Host:
See information about the host such as where it's located, what ports are open and which organization owns the IP.

```root@kali:~/shodan# shodan host 69.91.197.32
69.91.197.32
Hostnames:               MAC-30239442.uwb.edu
City:                    Kenmore
Country:                 United States
Organization:            University of Washington
Updated:                 2019-12-11T20:20:48.380125
Number of open ports:    1
 
Ports:
   3283/udp Apple Remote Desktop
root@kali:~/shodan#
```

Shodan Search
```
root@kali:~/shodan# shodan search --fields ip_str,port,hostname,org,os,product net:69.91.192.0/24,69.91.193.0/24,69.91.194.0/24,69.91.195.0/24,69.91.196.0/24,69.91.197.0/24,69.91.198.0/24,69.91.199.0/24,69.91.200.0/24,69.91.201.0/24,69.91.202.0/24,69.91.203.0/24,69.91.204.0/24
 
 
69.91.197.32    3283            University of Washington                Apple Remote Desktop   
69.91.197.18    3283            University of Washington                Apple Remote Desktop   
69.91.193.117   500             University of Washington        Windows 8       Microsoft      
69.91.202.11    3283            University of Washington                Apple Remote Desktop   
69.91.195.3     3283            University of Washington                Apple Remote Desktop   
69.91.203.37    3283            University of Washington                Apple Remote Desktop   
69.91.197.56    80              University of Washington                       
```

Extra Search Grep
```
root@kali:~/shodan# shodan parse --fields ip_str,port,product,os --separator , UWB1.json.gz | grep "Windows 8"
```
