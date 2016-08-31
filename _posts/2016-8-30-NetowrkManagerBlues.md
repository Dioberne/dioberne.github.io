---
layout: post
title: Network Manager Blues
---

Who needs network connectivity? Not me according to the network-manager service used by Ubuntu. 

I was chatting with friends when suddenly the popup appeared saying “You have been disconnected”. 30 seconds later it appeared again, and again, and again. Needless to say I was non too happy with this arrangement. I checked the logs to see what was up. 

{% highlight bash %}
cat /var/log/* | grep -a enp0s42e3
{% endhighlight %}

This hinted at it being an issue with network manager and dhcp so I set a static IP address in network manager then restarted the service using”

sudo service network-manager restart

This did nothing so I rebooted. Low and behold I had an IP address again! Aaaand then I didn't. Fed up and sure it was network-manager that was the culprit. I then used

{% highlight bash %}
sudo apt-get purge network-manager
{% endhighlight %}

To clean out the trash. This was all well and good, but it begs the question of exactly how one configures a network interface without using GUI front ends to buggy services. The answer lies in /etc/network/interfaces. With a little Googleing and a hefty dose of trial and error I landed on the following configuration:

{% highlight bash %}
auto lo enp0s42e3
iface lo inet loopback

iface enp0s42e3 inet static
	address 192.168.1.57
	netmask 255.255.255.0
	gateway 192.168.1.1
	dns-nameservers 8.8.8.8, 8.8.4.4
{% endhighlight %}

The first line tells it what to configure. The second is a one liner for the loopback interface. The following is the deceleration for a fully working Ethernet interface.  In older versions of Ubuntu the name servers had to be configured in a separate file. Not so with the venerable 16.04. 

If, and only if, you know what you are doing it is not hard to configure network interfaces on Ubuntu 16.04. This experience, while a huge pain in the tuckus, has broadened my knowledge of Linux networking. 
