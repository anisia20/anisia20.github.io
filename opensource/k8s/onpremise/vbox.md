#### nat network 설정
    Host Network Manager  설정 후

    서버에서 
    ip addr

    root@ubuntu:~# cd /etc/network/
    root@ubuntu:/etc/network# cp interfaces interfaces-181021
    root@ubuntu:/etc/network# vi interfaces
#### 다음과 같이 설정하면 된다.
    auto enp0s3
    iface enp0s3 inet dhcp
    auto enp0s8
    iface enp0s8 inet static
    address 192.168.56.2
    netmask 255.255.255.0
    network 192.168.56.0
    broadcast 192.168.56.255

#### 네트워크 인터페이스 재실행 접속 끈킴
    ifconfig enp0s3 down
    ifconfig enp0s8 down
    ifconfig enp0s3 up
    ifconfig enp0s8 up

#### ubuntu 17 버전 이후
    /etc/netplan/*.yml

#### yml 내용
    network:
        ethernets:
            enp0s3:
                addresses: [10.0.2.10/24]
                gateway4: 10.0.2.1
                dhcp4: no
                optional: true
                nameservers:
                addresses: [8.8.8.8,8.8.4.4]
            enp0s8:
                addresses: [192.168.59.102/24]
                gateway4: 192.168.59.1
                dhcp4: no
                optional: true
                nameservers:
                addresses: [8.8.8.8,8.8.4.4]
        version: 2

    $ netplan apply

