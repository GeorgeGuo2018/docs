# LB插件原型验证之 路由器配EIP静态路由方式

## k8s集群中配置
> 1，集群中只需要起个LB类型的service，起service的yaml文件中，只需指定EIP即可。例子如文件夹中myweb-svc.yaml 和 rc.yaml
> 2，pod所在node节点上配置如下路由规则，以捕获eip方式的服务请求(该node配lo规则后，该node上将不能通过eip访问service)
> ip route replace local 0/0 dev lo table 100
> ip rule add to 139.198.14.0/24 lookup 100

> ip route replace default via 192.168.1.12 dev eth0 table 101
> ip rule add from 139.198.14.0/24 lookup 101

## 模拟交换机上配置
- 1，在青云云平台上申请一个内部绑定的公网IP，点击分配给该机器即可（不需要按说明文档里说的再给新加的网卡配ip、路由等操作）

- 2, 在这台机器上加访问外网的路由：ip route replace default dev eth1

- 3, 在这台机器上添加service eip 到 node ip路由：
> ip route replace 139.198.177.227 via 192.168.1.7 dev eth0

- 4, 打开机器上的端口转发并关闭包过滤
> sysctl -w net.ipv4.ip_forward=1
> sysctl -w net.ipv4.conf.all.rp_filter=0
> sysctl -w net.ipv4.conf.eth1.rp_filter=0
> sysctl -w net.ipv4.conf.eth0.rp_filter=0
