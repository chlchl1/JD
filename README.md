# 阿西吧の监控库
# 前言

此项目作为个人日常线报监控专用,纯收集,无审查,由于只在自己的青龙里使用，所以脚本不是很完善，若有不足的地方请提出。

# 部署方法

## 容器化部署

在 Linux 服务器上部署，首先安装 docker (若已安装请忽略此步):

```shell
# 一键安装docker
curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun

# 查看docker版本
docker -v

# 设置开机启动
systemctl enable docker

# 启动
systemctl start docker
```

之后部署auto-proxy-pool代理池：

```shell
# 创建文件夹
mkdir -p /root/proxypool

# 进入文件夹
cd /root/proxypool

# 创建容器
docker run -itd -v "$(pwd)":/run/data -p 8899:8080 --restart=always --name=auto-proxy-pool mzzsfy/auto-proxy-pool

# 修改配置文件
vim proxy.yml
```
键盘按下i进入编辑模式,小白填写代理池api地址即可,写入最简配置(务必看注释):
##  [星空代理 (最低8R/1W个IP,签到星币免费兑IP)](http://www.xkdaili.com/?ic=6a9sesfq)
##  [携趣网络 (最低10R/2W个IP,每日免费送0.1W测试IP)](https://www.xiequ.cn/)
```shell
changeRequest:
  - hostRegex: (.+\.jd\.com)|(.+\.isvjd\.com)|(.+\.isvjcloud\.com) #正则表达式
    proxy: proxy
upstream:
  proxy: #自定义名字
    template: '{{$x := regexFindAll "\\d{1,3}(\\.\\d{1,3}){3}:\\d{2,5}" . -1}}{{range $s := $x}}{{printf "http://%s" $s}}{{end}}'
    apiUrl: #这里是填写代理池api地址,格式：txt,换行\n,提取1个(此行双引号后有一个空格 #号前要有一个空格)
    checkRetryNumber: 2 #检测代理次数，超过次数则获取新代理
    apiRetryNumber: 5 #上游重试次数，超过次数则采用直连
    checkUrl: http://jd.com/,http://baidu.com/,http://bilibili.com/ #检测代理是否有效，不用改
    lifecycle: -1 #自动失效时间，-1为不自动失效，单位秒
    maxSize: 2 #保留几个上游
    checkTimeout: 2s #单个check超时时间
    checkRetryWait: 500ms #重试check前等待时间
    requestInterval: 1000ms #请求时间间隔，在这个时间内只会请求一次api
```
键盘按下Esc然后键入:wq后Enter保存

```shell
# 重启auto-proxy-pool容器
docker restart auto-proxy-pool

# 查看auto-proxy-pool容器控制台输出
docker logs -f auto-proxy-pool
```
如果日志输出正常则表明代理池运行成功，每次修改代理池配置文件都要重启代理池才会生效哦！

## 已在每个脚本开头添加auto-proxy-pool代理池调用代码
```shell
require("global-agent/bootstrap");
global.GLOBAL_AGENT.HTTP_PROXY="http://172.17.0.1:8899";
```
## 在青龙NodeJs处安装以下两个依赖
```shell
global-agent
https-proxy-agent
```
# 拉库
```shell
ql repo https://github.com/LYC-axiba/JD-Monitoring-library.git "jd_" "" "^jd[^_]|USER|function|sendNotify|magic|h5sts"
```
以下是我整理这个仓库的配置文件，顺序从上到下对应着此库的脚本，M脚本我设置跑的每个容器前20个CK,除了M脚本外如果不想跑20个号，可以复制以下内容，把35替换成你执行的数量,把pin值改成你自己的
```shell
#[MJ] M幸运抽奖   walle1798_WALL.E/m_jd_wx_luckDraw.js
export M_WX_LUCK_DRAW_URL=""

#[MJ] M组队瓜分   walle1798_WALL.E/m_jd_wx_team.js
export M_WX_TEAM_URL=""

#[MJ] M关注有礼无线   walle1798_WALL.E/m_jd_wx_shopGift.js
export M_WX_SHOP_GIFT_URL=""

#[MJ] M分享有礼   walle1798_WALL.E/m_jd_wx_share.js
export M_WX_SHARE_URL=""

#[MJ] M读秒手速   walle1798_WALL.E/m_jd_wx_secondDraw.js
export M_WX_SECOND_DRAW_URL=""

#[MJ] M等级/生日礼包   walle1798_WALL.E/m_jd_wx_levelBirth.js
export M_WX_LEVEL_BIRTH_URL=""

#[MJ] M知识超人   walle1798_WALL.E/m_jd_wx_knowledge.js
export M_WX_KNOWLEDGE_URL=""

#[MJ] M关注抽奖   walle1798_WALL.E/m_jd_wx_followDraw.js
export M_WX_FOLLOW_DRAW_URL=""

#[MJ] M无线游戏   walle1798_WALL.E/m_jd_wx_game.js
export M_WX_GAME_URL=""

#[MJ] M完善有礼   walle1798_WALL.E/m_jd_wx_completeDraw.js
export M_WX_COMPLETE_DRAW_URL=""

#[MJ] M集卡抽奖   walle1798_WALL.E/m_jd_wx_collectCard.js
export M_WX_COLLECT_CARD_URL=""

#[MJ] M老虎机抽奖   walle1798_WALL.E/m_jd_wx_centerDraw.js
export M_WX_CENTER_DRAW_URL=""

#[MJ] M盖楼领奖   walle1798_WALL.E/m_jd_wx_buildDraw.js
export M_WX_BUILD_DRAW_URL=""

#[MJ] M加购有礼   walle1798_WALL.E/m_jd_wx_addCart.js
export M_WX_ADD_CART_URL=""

#[MJ] M关注有礼   walle1798_WALL.E/m_jd_follow_shop.js
export M_FOLLOW_SHOP_ARGV=""

#[MJ] M收藏有礼   walle1798_WALL.E/m_jd_fav_shop_gift.js
export M_FAV_SHOP_ARGV=""

#[MJ] M粉丝红包   walle1798_WALL.E/m_jd_fans_redPackt.js
export M_FANS_RED_PACKET_URL=""

#[MJ] M打豆豆   walle1798_WALL.E/m_jd_wx_dadoudou.js
export M_WX_DADOUDOU_URL=""
```
## 如有问题请联系我 BY [Axiba](https://t.me/lyc_axiba)
