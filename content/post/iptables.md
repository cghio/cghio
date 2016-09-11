+++
title = "Group rules in iptables"
tags = [ "Linux" ]
+++
使用分组可以方便更新分组下的规则。因为规则先删除再添加，所以可以重复执行这个脚本：

```sh
# remove group and its rules
if iptables-save | grep -q 'MyGroup'; then
  iptables -F MyGroup
  iptables-save | grep -v -- '-j MyGroup' | iptables-restore
  iptables -X MyGroup
fi

# create new group
iptables -N MyGroup

# add rules to the group
iptables -A MyGroup -m state --state ESTABLISHED,RELATED -j ACCEPT
iptables -A MyGroup -p tcp --dport 80 -j ACCEPT
iptables -A MyGroup -p tcp --dport 443 -j ACCEPT
iptables -A MyGroup -p udp --dport 60000:61000 -j ACCEPT
iptables -A MyGroup -p tcp -m state --state NEW --dport 22 -j ACCEPT
iptables -A MyGroup -p icmp --icmp-type "echo-request" -j ACCEPT
iptables -A MyGroup -i lo -j ACCEPT -m comment --comment "allow all loopback traffic"
iptables -A MyGroup -j DROP

# add group to INPUT
iptables -A INPUT -j MyGroup
```
