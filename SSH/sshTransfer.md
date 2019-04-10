ssh -R 10022:10.39.38.5:22 192.168.1.3 -i /ansible/kp-ztlghltq

Host jump
  HostName 10.39.38.5
  Port 10022
  User root
   IdentityFile /ansible/kp-ztlghltq
Host 192.168.1.3
  HostName  192.168.1.3
  Port 22
  User root
  IdentityFile  /ansible/kp-ztlghltq
  ProxyCommand ssh -i /ansible/kp-ztlghltq -p 10022 [root@10.39.38.5](mailto:root@10.39.38.5) -W %h:%p
Host  192.168.1.2
  HostName 192.168.1.2
  Port 22
  User root
   IdentityFile /ansible/kp-ztlghltq
  ProxyCommand ssh -i /ansible/kp-ztlghltq  -p 10022 [root@10.39.38.5](mailto:root@10.39.38.5) -W %h:%p