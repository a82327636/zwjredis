lvs已经被内置在linux中，内核模块是ipvs(不能直接操作它，只能系统调用).如果想使用必须使用一种外壳程序(yum install ipvsdam -y)



// 通过windows VMware 配置一个lvs负载，已经两个后端服务(这里使用lvs的dr模型，mac地址欺骗因为是同一个局域网。如果不在同一个局域网需要使用tun模型，通过通道进行ip转换)

环境：三台虚拟服务机
node1: 192.168.196.130
node2: 192.168.196.132
node3: 192.168.196.133

将node1设置为lvs服务器
1: ifconfig eth0:2 192.168.196.100/24  (通过eth0网卡，新增一个子接口 192.168.196.100)
   ifconfig eth0:2 down(去除)
   

2: 配置网络协议（隐藏IP等设置）
node2与node3

cd /proc/sys/net/ipv4/conf    (找到对应的网卡，如eth0 或者 ens33)

cd ens33 (可以进入 cd all 将所有的网络接口全部修改)

ls

(找到 arp_ignore,arp_announce)

echo 1 > arp_ignore (修改arp_ignore中的值，不要使用vi arp_ignore 内核不能这样修改)
cat arp_ignore
echo 2 > arp_announce


3: 配置隐藏IP
ifconfig lo:2 192.168.196.100 netmask 255.255.255.255 （新增一个环回子接口，也就是一个网卡，ip是192.168.196.100,注意这里的4个255就是为了不产生网络号(这里的网络号就是192.168.196.100 而不是 192.168.196.*)，导致192.168.196.*的网络包也被环回网卡接收，永远发不出。）
