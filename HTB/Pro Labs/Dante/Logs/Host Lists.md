# Public IP
**PING**
```
#!/bin/bash

subnet="10.10.110"
network="$subnet.0/24"

echo "Scanning hosts in $network"

for host in $(seq 1 254); do
    ip="$subnet.$host"
    ping -c 1 -w 1 $ip > /dev/null
    if [ $? -eq 0 ]; then
        echo "Host $ip is up"
    fi
done
```

**RESULT**
```
$ ./scan-active.sh      
Scanning hosts in 10.10.110.0/24
Host 10.10.110.2 is up
Host 10.10.110.100 is up
```

# Private IP
**Tunnenling**: 
`ssh -i ~/.ssh/id_ed25519 -D 1080 root@10.10.110.100`
```
root@DANTE-WEB-NIX01:~# for i in {1..255} ; do (ping -c 1 172.16.1.$i | grep "bytes from" | cut -d ' ' -f4 | tr -d ':' &); done
```
```
172.16.1.5
172.16.1.10
172.16.1.12
172.16.1.13
172.16.1.17
172.16.1.19
172.16.1.20
172.16.1.100
172.16.1.101
172.16.1.102
```

## **Has Been Owned**:
[] 172.16.1.5
[x] 172.16.1.10
[x] 172.16.1.12
[x] 172.16.1.13
[x] 172.16.1.17
[] 172.16.1.19
[] 172.16.1.20
[] 172.16.1.100
[] 172.16.1.101
[] 172.16.1.102