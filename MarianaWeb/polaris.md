POLARIS NETWORK SETUP FOR LINUX BOXES
Network Config hints for LINUX
________________________________________
NOTE:
Setting up Linux systems for use on a particular LAN can be done in a variety of ways. The following are suggestions and hints of things to check. But these suggestions may not be the only way, or the best way, to set up your Linux system. Consult your local Linux system expert for things that may be peculiar to your system.
IMPORTANT: Whenever making a change to a system file, always make a copy of the original version of the file that was working before you began reconfiguration. E.g. before editing /etc/hosts, do something like "cp /etc/hosts /etc/hosts.orig".
In the following suggestions and hints, assume that your assigned fully-qualified hostname for your machine is "myhost.mf.espo.nasa.gov", and your assigned IP address is 198.120.56.25. Consult the table of assigned hostnames and IP addresses to find your actual unique values for these two items and use them. The other configuration parameters are shared by all hosts. They are:
Moffett Field

  Netmask: 255.255.255.0
  Broadcast IP address: 192.150.34.255
  Gateway IP address: 192.150.34.100

  DNS Nameserver hosts:
    cloud1.arc.nasa.gov   128.102.154.176
    mx.nsi.nasa.gov       128.102.18.31

Fort Wainwright

  Netmask: 255.255.255.0
  Broadcast IP address: 198.120.56.255
  Gateway IP address: 198.120.56.1

  DNS Nameserver hosts:
    cloud2.polaris.espo.nasa.gov   198.120.56.42
    cloud4.polaris.espo.nasa.gov   198.120.56.43

Barbers Point
 
  Netmask: 255.255.255.0
  Broadcast IP address: 198.120.15.255
  Gateway IP address: 198.120.15.1
 
  DNS Nameserver hosts:
    cloud4.polaris.espo.nasa.gov   198.120.15.43
 
________________________________________
/etc/HOSTNAME
Put your fully qualified hostname in the /etc/HOSTNAME file. E.g. the following single line would be in /etc/HOSTNAME:
myhost.mf.espo.nasa.gov
Other system scripts that run at boot time, or when your system transitions to multiuser mode, may use the contents of the /etc/HOSTNAME file to define or otherwise manipulate the host name for your machine.
________________________________________
/etc/hosts
Minimally put your hostname and its IP address in your /etc/hosts file. E.g.
198.120.56.25    myhost.mf.espo.nasa.gov   myhost  loghost
________________________________________
Dynamic hostname to IP address lookup service
If dynamic hostname-to-IP-address lookup service using the Ames name servers is desired, ensure that the "bind" option is included in the /etc/host.conf file. E.g. /etc/host.conf could contain the following:
order hosts bind
multi on
This will direct the hostname resolver library to first check your /etc/hosts file to resolve a hostname into an IP address, and if not found there, will then use the nameserver listed in the /etc/resolv.conf file.
If "bind" appears in your /etc/host.conf file, then point at the Ames nameservers by including the following in your /etc/resolv.conf file:
domain mf.espo.nasa.gov
nameserver 128.102.154.176
nameserver 128.102.18.31
The first nameserver IP address is a nameserver cloud1.mf.espo.nasa.gov that is expected to be operating on the a LAN for fastest nameserver access. The second IP address is the machine cloud1.arc.nasa.gov at NASA Ames that is expected to maintain the latest hostname vs IP addresses for the espo.nasa.gov LAN. The primary cloud1 machine at Ames will download its hostname vs IP address table to the secondary cloud2 and cloud4 machines on the LAN. The third nameserver entry above is for a general Ames nameserver mx.nsi.nasa.gov.
________________________________________
Static hostname to IP address lookup service
If the static hostname-to-IP-address lookup method is to be used via the /etc/hosts file, ensure the named daemon is not started at boot time, usually in the /etc/rc.d/rc.* files, e.g. /etc/rc.d/rc.inet*. Static hostname-to-IP-address resolution is handled in the usual unix way by placing IP-address vs hostname pairs in the /etc/hosts file. A sample list of local LAN unix /etc/hosts entries is on the POLARIS web pages under the "COMMUNICATION" top-level button.
Your /etc/host.conf file would have the following in it for static hostname-to-IP-address lookup:
order hosts
________________________________________
/etc/rc.d/*
These scripts run at various levels of bringing your Linux system up. There is usually a script named /etc/rc.d/rc.inet1 that controls the basic things associated with your ethernet interface and how tcp/ip addresses get associated with that interface. Check this script for critical configuration parameters definitions that will be unique to the LAN. E.g. the following parameters are usually defined as symbols within rc.inet1, or are defined in another script (such as /etc/net.config) that is called by rc.inet1.
IPADDR="198.120.56.25"
BROADCAST="198.120.56.255"
NETMASK="255.255.255.0"
NETWORK="198.120.56.0"
GATEWAY="198.120.56.1"
IPADDR is your unique IP address assigned IP address for the machine. BROADCAST is the broadcast address for all hosts on the LAN. This is 198.120.56.255 for all hosts on the LAN. NETMASK is the mask whose 1 bits show where the network part of the IP address is. For the LAN, this is 255.255.255.0, and is the same for all hosts on the LAN. NETWORK is the address of the LAN. It is 198.120.56.0. GATEWAY is the address of the router, which is at 198.120.56.1. Be sure to use your assigned IP address for the definition of the IPADDR symbol above. (198.120.56.25 is just the example IP address.) These symbol definitions are the typical ones that are found on many Linux systems, but yours may be different. The important thing is that the commands such as "ifconfig" and "route" that are run inside rc.inet1 should use the proper network addresses and other parameters for the LAN. If you are not sure how this all works, contact your local Linux guru to get help before going on deployment.
________________________________________
routed
Find the line in one of your /etc/rc.d/rc.* files, usually within /etc/rc.d/rc.inet2, where routed is started. Ensure that it will run in cache routing quiet mode. I.e.:
  routed -q
________________________________________
/etc/sendmail.cf
Set the local domain name for email to the one we are using for the LAN by finding the line within /etc/sendmail.cf that begins with "DD", and change that line to:
DDmf.espo.nasa.gov
If you have a /etc/sendmail.cw file, put your LAN hostname in it. E.g.
myhost.mf.espo.nasa.gov myhost
________________________________________
You will need to set your system clock time to the area or operation. Valid timezones are only those in the /usr/share/lib/zoneinfo/ directories.
To have commands such as "date" and "ls -l" use local time, you can set the local time zone in a systemwide way by:
  cd /usr/lib/zoneinfo/
  mv localtime localtime.save
  ln -s US/Pacific localtime
Some systems are set up to define the time zone with the TZ environment variable on a per user basis. If that works better for your users, have them define "TZ to US/Pacific".
E.g. in the csh or tcsh shells:
  setenv TZ "US/Pacific"
or in the sh shell:
  TZ="US/Pacific"
  export TZ
________________________________________
Other things to check
If your machine is going to be a print server, &/or will be spooling to print queues on other machines using the Berkeley network printing system, check your /etc/hosts.lpd and /etc/printcap files to ensure they contain the appropriate names of machines on the LAN.
Some machines may have entries in their cron table or license manager software's database file(s) that are specific to the LAN on which they are operating. You may want to check these for proper.
________________________________________

