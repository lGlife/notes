### 性能端口分析



2304的接口涉及的应用流程
isp-api
hsa-mbs-fsi
hsa-mbs
hsa-cep-smc-clc
hsa-mba-nes-mdt-base
hsa-cep-pic	
hsa-cep-ivc
hsa-cep-bic
hsa-cep-usc	
hsa-mba-nes-inc-base	
hsa-cep-plc-std
hsa-mba-nes-inc-base
hsa-cep-smc-pms	
hsa-cep-smc-clc

2207的接口涉及的应用流程
isp-api
hsa-mbs-fsi
hsa-mbs  
hsa-cep-smc-clc
hsa-cep-pic
hsa-cep-ivc
hsa-cep-usc
hsa-cep-plc-std
hsa-cep-smc-pms
hsa-mba-dsm
hsa-mba-nes-inc-base





ss -lt



// 统计：各种连接的数量
netstat -n | awk '/^tcp/ {++S[$NF]} END {for(a in S) print a, S[a]}'
netstat -an | awk '/^tcp/ {++y[$NF]} END {for(w in y) print w, y[w]}'



//统计与每台服务器的链接数量
netstat -nat|grep "tcp"|awk ' {print$5}'|awk -F : '{print$1}'|sort|uniq -c|sort -rn



//tcp端口相关分析
netstat -nat|grep -i "20003"|wc -l



server.tomcat.accept-count=1000
server.tomcat.max-connections=20000
server.tomcat.max-threads=1000
server.tomcat.min-spare-threads=100




ip addr del 172.18.40.238/32 dev lo
ip addr add 172.18.40.238/24 dev ens192

ip addr add 172.18.40.238/24 dev lo



ip addr add 172.18.40.238/24 dev ens192



ifconfig eth0:0 10.93.24.65/21 up
ipvsadm -C                    
ipvsadm --set 30 5 60         
ipvsadm -A -t 172.18.40.238:20000 -s wrr -p 20   
ipvsadm -a -t 172.18.40.238:20000 -r 172.18.40.32:20000 -g -w 1 
ipvsadm -a -t 172.18.40.238:20000 -r 172.18.40.33:20000 -g -w 1
ipvsadm -ln



ip addr del 172.18.40.238/32 dev lo


#### lvs服务端处理
ifconfig eth0:0 10.93.24.65/21 up
ipvsadm -C                    
ipvsadm --set 30 5 60         
ipvsadm -A -t 10.93.24.65:20003 -s wrr -p 20   
ipvsadm -a -t 10.93.24.65:20003 -r 10.93.24.33:20003 -g -w 1 
ipvsadm -a -t 10.93.24.65:20003 -r 10.93.24.70:20003 -g -w 1
ipvsadm -ln

ipvsadm -a -t 10.0.0.50:80 -r 192.168.1.51 -m
ipvsadm -a -t 10.0.0.50:80 -r 192.168.1.52:8080 -m   



ipvsadm -C                    
ipvsadm --set 30 5 60         
ipvsadm -A -t 10.93.24.65:16208 -s rr
ipvsadm -a -t 10.93.24.65:16208 -r 10.93.24.33:16208 -m
ipvsadm -a -t 10.93.24.65:16208 -r 10.93.24.70:16208 -m
ipvsadm -ln


ip addr del 10.93.24.65/21 dev eth0
ipvsadm -C




## 后端服务器处理
ifconfig lo:0 72.18.40.238/32 up
route add -host 72.18.40.238 dev lo

cat >>/etc/sysctl.conf<<EOF
net.ipv4.conf.all.arp_ignore = 1
net.ipv4.conf.all.arp_announce = 2
net.ipv4.conf.lo.arp_ignore = 1
net.ipv4.conf.lo.arp_announce = 2
EOF
sysctl -p


#ipvsadm -A -t 10.93.24.65:20003 -s wrr
virtual_server 10.93.24.65 20003 {
    delay_loop 6
    lb_algo rr
    lb_kind DR
    nat_mask 255.255.248.0
    persistence_timeout 50
    protocol TCP
    real_server 10.93.28.37 20003 {
        weight 1
        TCP_CHECK {
            connect_timeout 5
            nb_get_retry 3
            delay_before_retry 3
            connect_port 20003
        }
    }
    real_server 10.93.28.43 20003 {
        weight 1
        TCP_CHECK {
            connect_timeout 5
            nb_get_retry 3
            delay_before_retry 3
            connect_port 20003
        }
    }
}





## VIP修改，加lvs配置

ifconfig eth0:0 172.18.40.222/21 up
ipvsadm -C                    
ipvsadm --set 30 5 60         
ipvsadm -A -t 172.18.40.222:20003 -s wrr -p 20   
ipvsadm -a -t 172.18.40.222:20003 -r 172.18.40.147:20003 -g -w 1 
ipvsadm -a -t 172.18.40.222:20003 -r 172.18.40.148:20003 -g -w 1
ipvsadm -ln


## 147 与 148 后端服务器处理
ifconfig lo:0 172.18.40.222/32 up
route add -host 172.18.40.222 dev lo

cat >>/etc/sysctl.conf<<EOF
net.ipv4.conf.all.arp_ignore = 1
net.ipv4.conf.all.arp_announce = 2
net.ipv4.conf.lo.arp_ignore = 1
net.ipv4.conf.lo.arp_announce = 2
EOF
sysctl -p