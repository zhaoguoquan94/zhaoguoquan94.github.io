---
author: zhaoguoquan
comments: true
date: 2015-03-19 13:45:11+00:00
layout: post
slug: '%e4%bb%8ewilab%e7%bd%91%e7%bb%9c%e6%95%85%e9%9a%9c%e5%ad%a6%e4%b9%a0%e8%ae%a1%e7%bd%91%e7%9f%a5%e8%af%86'
title: 从wilab网络故障学习计网知识[未完成]
wordpress_id: 76
categories:
- 未分类
---

本篇文章总结了关于我们一年来修网遇到的各种坑与学到的计网知识.

坑太大,慢慢填...



=============================

看来懒惰还是战胜了勤奋，wilab的网络真是可以用丧心病狂来形容。我在各种论坛上都有问过类似的问题，有些有结果，有些并没有，这里放一些大家的回答。

=========================

https://social.technet.microsoft.com/Forums/windowsserver/en-US/96f4791b-c2d8-42c1-bbda-59c48b8114a8/how-to-set-up-ipv6-nat-on-windows-server-2012?forum=ipv6


**How to set up ipv6 NAT on windows Server 2012? **




I have one window server 2012 in my lab with 2 NIC. NIC 2 has a public IPV4 address as well as IPV6 address. NIC 1 is connected with a switch, which connects our intranet (192.168.0.1/24). 




I set up a NAT using IPV4 so all traffic in intranet will be sent to this server and then be sent to NIC2's gateway. 




But Google was blocked in China (IPV4) and IPV6 is not affected. So I'm willing to do NAT using IPV6 since there is only one address assigned to my lab and our intranet cannot be connected to NIC2's subnet. What's more,ICS didn't work too.




In linux I could use ip6table (**IPv6 Masquerading**)but I cannot find a solution on Windows. Please help me.




================================================




Hi Grayson,




As far as I know, there is no NAT function for IPv6.




NAT is developed to solve the problem of limited and ever-decreasing availability of public IPv4 addresses.




IPv6 introduces a new addressing model that uses 16-byte addresses expressed in colon-hexadecimal notation rather than the familiar four-byte IPv4 addresses typically expressed in dotted-decimal notation. NAT will no longer be needed in an IPv6 world where every client has a globally routable IP address.




Here is the reference for IPv6:




**IPv6 Technical Reference:**




[https://technet.microsoft.com/en-us/library/cc738109(v=ws.10).aspx](https://technet.microsoft.com/en-us/library/cc738109(v=ws.10).aspx)




Best Regards,




Leo




Please remember to mark the replies as answers if they help and unmark them if they provide no help. If you have feedback for TechNet Support, contact tnmff@microsoft.com.






	
  * Proposed as answer by [Leo HanMicrosoft contingent staff, Moderator](https://social.technet.microsoft.com/profile/leo%20han/?type=forum&referrer=http://social.technet.microsoft.com/Forums/windowsserver/en-US/96f4791b-c2d8-42c1-bbda-59c48b8114a8/how-to-set-up-ipv6-nat-on-windows-server-2012?forum=ipv6) Wednesday, June 10, 2015 9:23 AM

	
  * Marked as answer by [Leo HanMicrosoft contingent staff, Moderator](https://social.technet.microsoft.com/profile/leo%20han/?type=forum&referrer=http://social.technet.microsoft.com/Forums/windowsserver/en-US/96f4791b-c2d8-42c1-bbda-59c48b8114a8/how-to-set-up-ipv6-nat-on-windows-server-2012?forum=ipv6) Thursday, June 11, 2015 2:07 AM




Tuesday, June 02, 2015 1:49 AM



