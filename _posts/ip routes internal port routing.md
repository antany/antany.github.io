#Ref https://serverfault.com/questions/112795/how-to-run-a-server-on-port-80-as-a-normal-user-on-linux

Short answer: you can't. Ports below 1024 can be opened only by root. As per comment - well, you can, using CAP_NET_BIND_SERVICE, but that approach, applied to java bin will make any java program to be run with this setting, which is undesirable, if not a security risk.

The long answer: you can redirect connections on port 80 to some other port you can open as normal user.

Run as root:

# iptables -t nat -A PREROUTING -p tcp --dport 80 -j REDIRECT --to-port 8080
As loopback devices (like localhost) do not use the prerouting rules, if you need to use localhost, etc., add this rule as well (thanks @Francesco):

# iptables -t nat -I OUTPUT -p tcp -d 127.0.0.1 --dport 80 -j REDIRECT --to-ports 8080
NOTE: The above solution is not well suited for multi-user systems, as any user can open port 8080 (or any other high port you decide to use), thus intercepting the traffic. (Credits to CesarB).

EDIT: as per comment question - to delete the above rule:

# iptables -t nat --line-numbers -n -L
This will output something like:

Chain PREROUTING (policy ACCEPT)
num  target     prot opt source               destination         
1    REDIRECT   tcp  --  0.0.0.0/0            0.0.0.0/0           tcp dpt:8080 redir ports 8088
2    REDIRECT   tcp  --  0.0.0.0/0            0.0.0.0/0           tcp dpt:80 redir ports 8080
The rule you are interested in is nr. 2, so to delete it:

# iptables -t nat -D PREROUTING 2
