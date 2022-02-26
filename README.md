## qinglong增加warp并定时更换ip--仅限国外机器使用

声明：使用本教程前，请提前备份数据，以免造成损失

### 删除青龙

```
docker stop qinglong && docker rm qinglong
```

关于为什么要删除原有的青龙

为保证wireguard能在容器里运行，在启动时需要添加这些参数

--privileged --sysctl net.ipv6.conf.all.disable_ipv6=0 --cap-add net_admin

在安装时选择映射正确的目录，删除原有的青龙不影响青龙的配置（包括脚本库、定时任务等），但可能会影响依赖，可重装后修复依赖

### 安装青龙官方版

```
docker run -dit \
  -v $PWD/ql:/ql/data \
  -p 5700:5700 \
  --name qinglong \
  --hostname qinglong \
  --restart unless-stopped \
  --privileged \
  --sysctl net.ipv6.conf.all.disable_ipv6=0 \
  --cap-add net_admin \
  whyour/qinglong:latest
```

请注意映射目录与你之前使用的映射目录是否一致，否则会导致青龙的配置（包括脚本库、定时任务等）不正确

### 或者安装faker的docker的青龙2.9.3稳定版--仅之前使用faker一键脚本安装亲测稳定青龙版本的人使用

```
docker run -dit \
  -v $PWD/ql:/ql \
  -p 5700:5700 \
  --name qinglong \
  --hostname qinglong \
  --restart always \
  --privileged \
  --sysctl net.ipv6.conf.all.disable_ipv6=0 \
  --cap-add net_admin \
  shufflewzc/qinglong:latest
```

请注意映射目录与你之前使用的映射目录是否一致，否则会导致青龙的配置（包括脚本库、定时任务等）不正确

### 进入容器

```
docker exec -it qinglong bash
```

### 安装warp

```
curl -fsSL git.io/wireguard-go.sh | bash && apk add wireguard-tools curl openrc && wget https://github.com/ViRb3/wgcf/releases/download/v2.2.11/wgcf_2.2.11_linux_amd64 && mv wgcf_2.2.11_linux_amd64 wgcf && chmod +x wgcf && yes | ./wgcf register && ./wgcf generate && mv wgcf-profile.conf /etc/wireguard/wg0.conf && wg-quick up wg0 && curl cip.cc
```

可选择修复依赖，请到

https://thin-hill-428.notion.site/QL-pannel-Dependent-Librai-164400378c7f4a4587f976e89aea1584

### 在青龙面板添加更换ip的定时任务

名称：更换warp的ip

命令：task wg-quick down wg0 && wg-quick up wg0 && curl cip.cc

定时规则：33 */1 * * *

### 在青龙面板添加更换ip的启动，目前没能解决随系统自启wireguard的问题，只能每2分钟执行一次启动wireguard

名称：启动wireguard

命令：task wg-quick up wg0 && curl cip.cc

定时规则：*/2 * * * *
