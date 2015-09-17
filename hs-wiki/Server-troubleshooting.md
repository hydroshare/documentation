# django_docker_processes
If the process docker container is unable to talk to the "hydroshare" container via HTTP, you may need to update the *iptables* configuration of the host as follows:

First, add the iptable rule:

    -A INPUT -p tcp -m state --state NEW,ESTABLISHED -m tcp --dport 80 -j ACCEPT

before the rule:

    -A INPUT -j REJECT --reject-with icmp-host-prohibited

in /etc/sysconfig/iptables

Then, reload iptables rules:

    service iptables --full-restart
