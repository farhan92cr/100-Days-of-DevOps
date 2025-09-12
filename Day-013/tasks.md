# Day 13 - Advanced Firewall Configuration

## Task 1: Port Specific Firewall
Configure iptables for specific application ports

## Task 2: Rate Limiting
Implement connection rate limiting for SSH

## Task 3: Firewall Monitoring
Set up firewall rule monitoring and logging

**OR**
We have one of our websites up and running on our Nautilus infrastructure in Stratos DC. Our security team has raised a concern that right now Apache’s port i.e 8083 is open for all since there is no firewall installed on these hosts. So we have decided to add some security layer for these hosts and after discussions and recommendations we have come up with the following requirements:



1. Install iptables and all its dependencies on each app host.


2. Block incoming port 8083 on all apps for everyone except for LBR host.

