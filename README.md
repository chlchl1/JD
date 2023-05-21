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
以下是我整理这个仓库的配置文件，顺序从上到下对应着此库的脚本，M脚本我设置跑的每个容器前35个CK,除了M脚本外如果不想跑35个号，可以复制以下内容，把35替换成你执行的数量,把pin值改成你自己的
```shell
#入会开卡领取礼包   jd_OpenCard_Force.js
export VENDER_ID=""
export OPENCARD_BEAN="10"
export OPENCARD_QZ="true"

#品类联合任务   jd_categoryUnion.js
export jd_categoryUnion_activityId=""

#品类联合抽奖   jd_categoryUnion_draw.js
export export jd_categoryUnion_activityId=""

#CJ每日抢好礼通用活动   jd_cjdaily.js
export jd_cjdaily_activityId=""

#cjhy加购物车抽奖   jd_cjhy_wxCollectionActivity.js
export jd_cjhy_wxCollectionActivityId=""
export jd_cjhy_wxCollectionActivity_num="35"

#cjhy幸运抽大奖通用活动   jd_cjhy_wxDrawActivity.js
export jd_cjhy_wxDrawActivity_Id="xxx"
export jd_cjhy_wxDrawActivity_num="35"
export jd_wxDrawActivity_openCard="1"

#CJ知识超人通用活动   jd_cjwxKnowledgeActivity.js
export jd_cjwxKnowledgeActivity_activityId=""
export jd_cjwxKnowledgeActivity_num="35"

#CJ关注店铺有礼   jd_cjwxShopFollowActivity.js
export jd_cjwxShopFollowActivity_activityId=""
export jd_cjwxShopFollowActivity_num="35"

#CJ组队瓜分京豆-加密   jd_cjzdgf.js
export jd_cjhy_activityId=""
export jd_cjhy_activityUrl="https://cjhydz-isv.isvjcloud.com"

#完善信息有礼   jd_completeInfoActivity.js
export jd_completeInfoActivity_activityId=""
export jd_completeInfoActivity_activityUrl="https://cjhydz-isv.isvjcloud.com"
export jd_completeInfoActivity_venderId="1000120162"
export jd_completeInfoActivity_num="35"
export jd_completeInfoActivity_openCard="false"

#电脑配件ID任务jd_computer   jd_computer.js
export computer_activityIdList=""

#店铺抽奖-JK   jd_dpcj.py
export DPCJID="shopId1&shopId2"

#LZ刮刮乐抽奖通用活动-加密    jd_drawCenter.js
export jd_drawCenter_activityId=""
export jd_drawCenter_addCart="true"

#关注有礼-加密   jd_follow.js
export M_FOLLOW_SHOP_ARGV="12237383_12550303"

#LZ店铺通用游戏任务   jd_game.js
export WXGAME_ACT_ID=""

#jinggeng邀请入会有礼   jd_jinggengInvite.py
export jinggengInviteJoin="9e80809282a4bdc90182ab254c7e0a12&1000121005"

#邀请入会赢好礼（京耕） jd_jinggeng_showInviteJoin.js
export jd_showInviteJoin_activityUrl=""

#通用开卡-joinCommon系列   jd_joinCommon_opencard.py
export jd_joinCommonId="2b870a1a7450xxxxxxxxxxxxx&1000000904"

#jd_joyjd_open通用ID任务，多个活动用@连接，任务连接https://jdjoy.jd.com/module/task/v2/doTask
export comm_activityIDList="af2b3d56e22d43afa0c50622c45ca2a3"  
export comm_endTimeList="1639756800000"
export comm_tasknameList="京东工业品抽奖"

#JOY通用开卡活动   jd_joyopen.js
export JD_JOYOPEN="ID"

#京东抽奖机通用   jd_lottery.js
export JD_Lottery="id"

#店铺抽奖通用活动-加密   jd_luck_draw.js
export LUCK_DRAW_URL="https://cjhy-isv.isvjcloud.com/wxDrawActivity/activity?activityId=f3db41c8744e4e2c8895cf4dcfebde78"
export LUCK_DRAW_OPENCARD="false"
export LUCK_DRAW_NOTIFY="true"
export LUCK_DRAW_NUM="35"
export LUCK_DRAW_WAIT="1"
export LUCK_DRAW_Number="6"

#LZ加购有礼   jd_lzaddCart.js
export jd_lzaddCart_activityId="活动ID"

#lzkj_interactsaas关注商品有礼  jd_lzkj_interactsaas_gzspyl.js
export jd_lzkj_interactsaas_gzspyl_activityId="1650506450210635778"
export jd_lzkj_interactsaas_gzspyl_num="35"

#lzkj_interactsaas关注店铺有礼   jd_lzkj_interactsaas_gzyl.js
export jd_lzkj_interactsaas_gzyl_activityId="活动id"
export jd_lzkj_interactsaas_gzyl_num="35"

#lzkj_interactsaas加购有礼  jd_lzkj_interactsaas_jgyl.js
export jd_lzkj_interactsaas_jgyl_activityId="1651486708477661186"
export jd_lzkj_interactsaas_jgyl_num="35"
export jd_lzkj_interactsaas_jgyl_openCard="1"

#lzkj_interactsaas签到   jd_lzkj_interactsaas_qrqd.js
export jd_lzkj_interactsaas_qrqd_Ids="活动id1&活动id2&活动id3..."
export jd_lzkj_interactsaas_qrqd_num="35"
export jd_lzkj_interactsaas_qrqd_openCard="1"

#lzkj_interactsaas日历签到  jd_lzkj_interactsaas_rlqd.js
export jd_lzkj_interactsaas_rlqd_Ids="1650767100680138753"
export jd_lzkj_interactsaas_rlqd_num="35"
export jd_lzkj_interactsaas_rlqd_openCard="1"

#lzkj_interactsaas邀请好友入会   jd_lzkj_interactsaas_yqhyrh.js
export jd_lzkj_interactsaas_yqhyrh_activityId="活动id"

#加购有礼（lzkj_loreal）  jd_lzkj_loreal_cart.js
export jd_lzkj_loreal_cart_url=""

#幸运抽奖（lzkj_loreal）  jd_lzkj_loreal_draw.js
export jd_lzkj_loreal_draw_url="

#关注有礼（lzkj_loreal）  jd_lzkj_loreal_followShop.js
export jd_lzkj_loreal_followShop_url=""

#邀请入会有礼（lzkj_loreal）  jd_lzkj_loreal_invite.js
export jd_lzkj_loreal_invite_url=""
export jd_lzkj_loreal_invite_myhelpnum="35"

#lzkj签到有礼    jd_lzkj_signActivity2.js
export jd_lzkj_signActivity2_ids = "id1&id2&id3"
export jd_lzkj_signActivity2_num="35"
export jd_lzkj_signActivity2_openCard="1"

#lzkj加购物车抽奖   jd_lzkj_wxCollectionActivity.js
export jd_lzkj_wxCollectionActivityId="xxx"
export jd_lzkj_wxCollectionActivity_num="35"

#lzkj幸运抽大奖通用活动    jd_lzkj_wxDrawActivity.js
export jd_lzkj_wxDrawActivity_num="35"
export jd_wxDrawActivity_openCard="1"

#lzkj店铺礼包    jd_lzkj_wxShopGift.js
export jd_lzkj_wxShopGift_ids="id1&id2&id3"
export jd_lzkj_wxShopGift_num="35"
export jd_lzkj_wxShopGift_openCard="1"

#JoinCommon开卡专用   jd_opencardJBK.js
export opencard_id="9362c2185afd4c09b67558ae29102eff"
export opencard_open="true"
export opencard_addCart="true"
export opencard_draw="3"

#店铺积分换豆   jd_point_exchange.js
export POINT_EXCHANGE_URL="https://cjhy-isv.isvjcloud.com/mc/wxPointShopView/pointExgBeans?venderId=xxxxxxxx"

#邀请好友入会赢好礼  jd_prodev.js
export prodevactCode="活动id"
export prodevinvitePin="自己的pin"
export prodevinviteNum="true"

#邀请赢大礼  jd_prodev.py
export yhypin="需要助力的pin值"  
export yhyauthorCode="活动ID"

#超级无线店铺签到   jd_sevenDay.js
export LZKJ_SEVENDAY="xxxx,xxxx,xxxxx"
export LZKJ_SIGN="xxxx,xxxx,xxxxx"
export CJHY_SEVENDAY="xxxx,xxxx,xxxxx"
export CJHY_SIGN="xxxx,xxxx,xxxxx"

#超级无线店铺签到-监控版   jd_sevenDayjk.js
export COOKIE_NUM="35"
export jd_sevenDay_activityUrl""

#LZ分享有礼   jd_share.js
export jd_fxyl_activityId=""
export OWN_COOKIE_NUM="5"
export HELP_COOKIE_NUM="30"

#店铺左侧刮刮乐   jd_shopDraw.js
export venderId=""
export jd_shopDraw_activityUrl=""

#店铺抽奖   jd_shop_draw.js
export jd_shop_draw_ids="shopId&shopId&shopId"
export jd_shop_draw_num="35"

#组队分豆-飞利浦  jd_teamFLP.js
export jd_teamFLP_activityId='活动ID'

#PKC关注有礼-特效   jd_txgzyl.js
export PKC_TXGZYL=""

#收藏大师-加购有礼   jd_txzj_cart_item.js
export jd_cart_item_activityUrl="活动链接"

#收藏大师-关注有礼   jd_txzj_collect_item.js
export jd_collect_item_activityUrl="活动链接"

#收藏大师-关注商品   jd_txzj_collect_shop.js
export jd_collect_shop_activityUrl="活动链接"

#收藏大师-幸运抽奖   KingRan_KR/jd_txzj_lottery.js
export jd_lottery_activityUrl="活动链接"

#txzj 签到  jd_txzj_sign_in.js
export jd_txzj_sign_in_id="id"
export jd_txzj_sign_in_num="35"

#微定制瓜分京豆-加密   jd_wdz.js
export jd_wdz_pin="pin值"
export jd_wdz_mixnum="0"
export jd_wdz_maxnum="35"
export jd_wdzfd_task="true"
export jd_wdz_activityId="活动ID"
export jd_wdz_activityUrl="https://cjhydz-isv.isvjcloud.com"

#微定制瓜分福袋   jd_wdzfd.js
export jd_wdz_pin="pin值"
export jd_wdz_mixnum="0"
export jd_wdz_maxnum="35"
export jd_wdzfd_task="true"
export jd_wdzfd_activityId="活动ID"
export jd_wdzfd_activityUrl="https://cjhydz-isv.isvjcloud.com"

#LZ盖楼有礼   jd_wxBuildActivity.js
export jd_wxBuildActivity_activityId=""
export jd_wxBuildActivity_num="35"

#购物车锦鲤通用活动   jd_wxCartKoi.js
export jd_wxCartKoi_activityId=""

#集卡抽奖通用活动   jd_wxCollectCard.js
export jd_wxCollectCard_activityId=""

#加购有礼通用-加密   jd_wxCollectionActivity.js
export jd_wxCollectionActivity_activityUrl=""
export jd_wxCollectionActivity_num="35"

#完善信息有礼-JK  jd_wxCompleteInfo.py
export jd_wxCompleteInfoId="f3325e3375a14866xxxxxxxxxxxx&1000086192"

#粉丝互动通用活动-加密  jd_wxFansInterActionActivity.js
export jd_wxFansInterActionActivity_activityId=""

#知识超人   jd_wxKnowledgeActivity.js
export jd_wxKnowledgeActivity_activityUrl=""
export jd_wxKnowledgeActivity_openCard="false"
export jd_wxKnowledgeActivity_num="35"

#生日礼包和会员等级礼包 jd_wxMcLevelAndBirthGifts.js
export jd_wxMcLevelAndBirthGifts_activityId="8ddbbc616d71450cb296e17bec0bdc08"
export jd_wxMcLevelAndBirthGifts_activityUrl="https:#cjhydz-isv.isvjcloud.com"
export jd_wxMcLevelAndBirthGifts_num="35"
export jd_wxMcLevelAndBirthGifts_openCard="false"

#读秒拼手速通用活动   jd_wxSecond.js
export jd_wxSecond_activityId=""
export jd_wxSecond_addCart="false"


#分享有礼-加密  jd_wxShareActivity.js
export jd_wxShareActivity_activityId="a249edc8d5e243cbb93d8c078fe6955b"
export jd_wxShareActivity_helpnum="1"

#关注店铺有礼   KingRan_KR/jd_wxShopFollowActivity.js
export jd_wxShopFollowActivity_activityUrl=""
export jd_wxShopFollowActivity_openCard="false"
export jd_wxShopFollowActivity_num="35"

#店铺礼包特效   jd_wxShopGift.js
export jd_wxShopGift_activityUrl=""

#LZ让福袋飞通用活动  jd_wxUnPackingActivity.js
export jd_wxUnPackingActivity_activityId=""

#M老虎机抽奖  jd_wx_centerDraw.js
export M_WX_CENTER_DRAW_URL=""

#LZ店铺游戏   jd_wxgame.js
export jd_wxgame_activityId="b9d33b3f7d824cf499e1870f9a0637bb"
export jd_wxgame_addCart="true"

#邀好友赢大礼   jd_yqhy.py
export jd_inv_authorCode=""

#LZ组队瓜分京豆-加密   jd_zdjr.js
export jd_zdjr_activityId=""
export jd_zdjr_activityUrl="https://lzkjdz-isv.isvjcloud.com"

#M粉丝红包    m_jd_fav_shop_gift.js
export M_FANS_RED_PACKET_URL=""

#M收藏有礼   m_jd_fav_shop_gift.js
export M_FAV_SHOP_ARGV=""

#M关注有礼   m_jd_follow_shop.js
export M_FOLLOW_SHOP_ARGV="12237383_12550303"

#M加购有礼   m_jd_wx_addCart.js
export M_WX_ADD_CART_URL=""

#M盖楼领奖   m_jd_wx_buildDraw.js
export M_WX_BUILD_DRAW_URL=""

#M老虎机抽奖  jd_wx_centerDraw.js
export M_WX_CENTER_DRAW_URL=""

#M集卡抽奖   m_jd_wx_collectCard.js
export M_WX_COLLECT_CARD_URL=""

#M完善有礼  m_jd_wx_completeDraw.js
export M_WX_COMPLETE_DRAW_URL=""

#M打豆豆   m_jd_wx_dadoudou.js
export M_WX_DADOUDOU_URL=""

#M关注抽奖  m_jd_wx_followDraw.js
export M_WX_FOLLOW_DRAW_URL=""

#M无线游戏  m_jd_wx_game.js
export M_WX_GAME_URL=""

#M知识超人   m_jd_wx_knowledge.js
export M_WX_KNOWLEDGE_URL=""

#M等级/生日礼包   m_jd_wx_levelBirth.js
export M_WX_LEVEL_BIRTH_URL=""

#M幸运抽奖  m_jd_wx_luckDraw.js
export M_WX_LUCK_DRAW_URL=""

#M读秒手速  m_jd_wx_secondDraw.js
export M_WX_SECOND_DRAW_URL=""

#M分享有礼  m_jd_wx_share.js
export M_WX_SHARE_URL=""

#M组队瓜分  m_jd_wx_team.js
export M_WX_TEAM_URL=""

#M关注有礼无线   m_jd_wx_shopGift.js
export M_WX_SHOP_GIFT_URL=""
```
## 如有问题请联系我 BY [Axiba](https://t.me/lyc_axiba)
