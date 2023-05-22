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

#[KR] 店铺抽奖通用活动-加密   KingRan_KR/jd_luck_draw.js
export LUCK_DRAW_URL=""
export LUCK_DRAW_OPENCARD="false"
export LUCK_DRAW_NOTIFY="true"
export LUCK_DRAW_NUM="20"
export LUCK_DRAW_WAIT="1"
export LUCK_DRAW_Number="8"

#[KR] 粉丝互动通用活动-加密   KingRan_KR/jd_wxFansInterActionActivity.js
export jd_wxFansInterActionActivity_activityId=""

#[KR] 超级无线店铺签到-监控版   KingRan_KR/jd_sevenDayjk.js
export COOKIE_NUM="20"
export WX_NOTIFY="true"
export jd_sevenDay_activityUrl=""

#[KR] 生日礼包和会员等级礼包   KingRan_KR/jd_wxMcLevelAndBirthGifts.js
export jd_wxMcLevelAndBirthGifts_activityId=""
export jd_wxMcLevelAndBirthGifts_activityUrl="https://cjhydz-isv.isvjcloud.com"
export jd_wxMcLevelAndBirthGifts_num="20"
export jd_wxMcLevelAndBirthGifts_openCard="false"

#[KR] 加购有礼通用-加密   KingRan_KR/jd_wxCollectionActivity.js
export jd_wxCollectionActivity_activityUrl=""
export jd_wxCollectionActivity_openCard="false"
export jd_wxCollectionActivity_num="20"

#[KR] 关注店铺有礼   KingRan_KR/jd_wxShopFollowActivity.js
export jd_wxShopFollowActivity_activityUrl=""
export jd_wxShopFollowActivity_openCard="false"
export jd_wxShopFollowActivity_num="20"

#[KR] 分享有礼-加密   KingRan_KR/jd_wxShareActivity.js
export jd_wxShareActivity_activityId=""
export jd_wxShareActivity_helpnum="1"

#[KR] 集卡抽奖通用活动   KingRan_KR/jd_wxCollectCard.js
export jd_wxCollectCard_activityId=""

#[KR] 邀请赢大礼   KingRan_KR/jd_prodev.py
export yhypin="需要助力的pin值,必须保证ck在容器里面"
export yhyauthorCode=""

#[KR] 知识超人   KingRan_KR/jd_wxKnowledgeActivity.js
export jd_wxKnowledgeActivity_activityUrl=""
export jd_wxKnowledgeActivity_openCard="false"
export jd_wxKnowledgeActivity_num="20"

#[KR] 购物车锦鲤通用活动   KingRan_KR/jd_wxCartKoi.js
export jd_wxCartKoi_activityId=""

#[KR] 读秒拼手速通用活动   KingRan_KR/jd_wxSecond.js
export jd_wxSecond_activityId=""
export jd_wxSecond_addCart="false"

#[KR] 店铺礼包特效   KingRan_KR/jd_wxShopGift.js
export jd_wxShopGift_activityUrl=""

#[KR] 邀请入会赢好礼（京耕）   KingRan_KR/jd_jinggeng_showInviteJoin.js
export jd_showInviteJoin_activityUrl=""

#[KR] 微定制瓜分福袋-加密   KingRan_KR/jd_wdzfd.js
export jd_wdz_pin="需要助力的pin值,必须保证ck在容器里面"
export jd_wdz_mixnum="0"
export jd_wdz_maxnum="40"
export jd_wdzfd_task="true"
export jd_wdzfd_activityId=""
export jd_wdzfd_activityUrl="https://cjhydz-isv.isvjcloud.com"

#[KR] 微定制瓜分京豆-加密   KingRan_KR/jd_wdz.js
export jd_wdz_pin="需要助力的pin值,必须保证ck在容器里面"
export jd_wdz_mixnum="0"
export jd_wdz_maxnum="40"
export jd_wdz_activityId=""
export jd_wdz_activityUrl="https://cjhydz-isv.isvjcloud.com"

#[KR] 收藏大师-幸运抽奖   KingRan_KR/jd_txzj_lottery.js
export jd_lottery_activityUrl=""

#[KR] 收藏大师-关注商品   KingRan_KR/jd_txzj_collect_shop.js
export jd_collect_shop_activityUrl=""

#[KR] 收藏大师-关注有礼   KingRan_KR/jd_txzj_collect_item.js
export jd_collect_item_activityUrl=""

#[KR] 收藏大师-加购有礼   KingRan_KR/jd_txzj_cart_item.js
export jd_cart_item_activityUrl=""

#[KR] 加购有礼（lzkj_loreal）   KingRan_KR/jd_lzkj_loreal_cart.js
export jd_lzkj_loreal_cart_url=""

##[KR] 幸运抽奖（lzkj_loreal）   KingRan_KR/jd_lzkj_loreal_draw.js
export jd_lzkj_loreal_draw_url=""

#[KR] 关注有礼（lzkj_loreal）   KingRan_KR/jd_lzkj_loreal_followShop.js
export jd_lzkj_loreal_followShop_url=""

#[KR] 邀请有礼（lzkj_loreal）   KingRan_KR/jd_lzkj_loreal_invite.js
export jd_lzkj_loreal_invite_url=""
export jd_lzkj_loreal_invite_myhelpnum="40"

#[KR] CJ组队瓜分京豆-加密   KingRan_KR/jd_cjzdgf.js
export jd_cjhy_activityId=""
export jd_cjhy_activityUrl="https://cjhydz-isv.isvjcloud.com"

#[KR] CJ每日抢好礼通用活动   KingRan_KR/jd_cjdaily.js
export jd_cjdaily_activityId=""

#[KR] LZ组队瓜分京豆-加密   KingRan_KR/jd_zdjr.js
export jd_zdjr_activityId=""
export jd_zdjr_activityUrl="https://lzkjdz-isv.isvjcloud.com"

#[KR] LZ刮刮乐抽奖通用活动   KingRan_KR/jd_drawCenter.js
export jd_drawCenter_activityId=""
export jd_drawCenter_addCart="true"

#[KR] LZ店铺游戏   gRan_KR/jd_wxgame.js
export jd_wxgame_activityId="b9d33b3f7d824cf499e1870f9a0637bb"
export jd_wxgame_addCart="true"

#[KR] LZ让福袋飞   KingRan_KR/jd_wxUnPackingActivity.js
export jd_wxUnPackingActivity_activityId=""

#[KR] LZ盖楼有礼   KingRan_KR/jd_wxBuildActivity.js
export jd_wxBuildActivity_activityId=""
export jd_wxBuildActivity_num="20"
export jd_wxBuildActivity_openCard="false"

#[KR] 品类联合任务   KingRan_KR/jd_categoryUnion.js
export jd_categoryUnion_activityId=""

#[KR] 品类联合抽奖   KingRan_KR/jd_categoryUnion_draw.js
export jd_categoryUnion_activityId=""

#[KR] JoyJd任务脚本   KingRan_KR/jd_joyjd_open.js
export comm_activityIDList=""  
export comm_endTimeList=""
export comm_tasknameList=""

#[KR] joyjd通用开卡   KingRan_KR/jd_joyopen.js
export JD_JOYOPEN=""

#[KR] 京东抽奖机通用-KR   KingRan_KR/jd_lottery.js
export JD_Lottery=""

#[KR] LZ加购有礼通用活动   KingRan_KR/jd_lzaddCart.js
export jd_lzaddCart_activityId=""

#[KR] JoinCommon开卡专用   KingRan_KR/jd_opencardJBK.js
export opencard_id=""
export opencard_open="true" #开启开卡
export opencard_addCart="true" #开启加购
export opencard_draw="3" #抽奖次数

#[KR] 飞利浦组队分豆-加密   KingRan_KR/jd_teamFLP.js
export jd_teamFLP_activityId=""

#[船长] 店铺特效关注有礼-JK   HarbourJ_HarbourToulu_main/jd_wxShopGift.py
export jd_wxShopGiftId="971e85d5dfd445e1axxxxxxxxxxxx"

#[船长] 关注店铺有礼-JK   HarbourJ_HarbourToulu_main/jd_wxShopFollow.py
export jd_wxShopFollowId=""
export jd_wxShopFollowRunNums="20"

#[船长] 完善信息有礼-JK   HarbourJ_HarbourToulu_main/jd_wxCompleteInfo.py
export jd_wxCompleteInfoId="f3325e3375a14866xxxxxxxxxxxx&1000086192"

#[船长] 加购有礼-JK   HarbourJ_HarbourToulu_main/jd_wxCollectionActivity.py
export jd_wxCollectionActivityUrl="https://lzkj-isv.isvjd.com/wxCollectionActivity/activity2/xxx?activityId=xxx"

#[船长] 盖楼有礼-JK   HarbourJ_HarbourToulu_main/jd_wxBulidActivity.py
export jd_wxBulidActivityId=""

#[船长] 生日等级礼包-JK   HarbourJ_HarbourToulu_main/jd_wxBirthGifts.py
export jd_wxBirthGiftsId=""

#[船长] 微定制组队瓜分-JK   HarbourJ_HarbourToulu_main/jd_wdz.py
export jd_wdz_activityId=""

#[船长] 关注有礼-JK   HarbourJ_HarbourToulu_main/jd_shopFollowGift.py
export jd_shopFollowGiftId=""
export jd_shopFollowGiftRunNums="20"

#[船长] 店铺会员礼包-JK   HarbourJ_HarbourToulu_main/jd_shopCollectGift.py
export jd_shopCollectGiftId=""

#[船长] 店铺抽奖-JK   HarbourJ_HarbourToulu_main/jd_dpcj.py
export DPCJID=""

#[船长] jinggeng邀请入会有礼   HarbourJ_HarbourToulu_main/jd_jinggengInvite.py
export jinggengInviteJoin=""

#[船长] 通用开卡-joinCommon系列   HarbourJ_HarbourToulu_main/jd_joinCommon_opencard.py
export jd_joinCommonId=""

#【保护环境】cjhy完善信息   feverrun_my_scripts/jd_cjhy_completeInfoActivity.js
export jd_cjhy_completeInfoActivity_Ids=""
export jd_cjhy_completeInfoActivity_num="20"
export jd_cjhy_completeInfoActivity_openCard="1"

#【保护环境】cjhy每日抢   feverrun_my_scripts/jd_cjhy_daily.js
export jd_cjhy_daily_ids="id1&id2&id3"
export jd_cjhy_daily_num="20"
export jd_cjhy_daily_openCard="1"

#【保护环境】cjhy七日签到   feverrun_my_scripts/jd_cjhy_sevenDay.js
export jd_cjhy_sevenDay_ids="id1&id2&id3"
export jd_cjhy_sevenDay_num="20"
export jd_cjhy_sevenDay_openCard="1"

#【保护环境】cjhy签到有礼   feverrun_my_scripts/jd_cjhy_signActivity.js
export jd_cjhy_signActivity_ids="id1&id2&id3"
export jd_cjhy_signActivity_num="20"
export jd_cjhy_signActivity_openCard="1"

#【保护环境】cjhy加购物车抽奖    feverrun_my_scripts/jd_cjhy_wxCollectionActivity.js
export jd_cjhy_wxCollectionActivityId="id1&id2&id3"
export jd_cjhy_wxCollectionActivity_num="20"
export jd_cjhy_wxCollectionActivity_openCard="1"

#【保护环境】cjhy幸运抽奖     feverrun_my_scripts/jd_cjhy_wxDrawActivity.js
export jd_cjhy_wxDrawActivity_Id="id1&id2&id3"
export jd_cjhy_wxDrawActivity_num="20"
export jd_cjhy_wxDrawActivity_openCard="1"

#【保护环境】cjhy游戏活动      feverrun_my_scripts/jd_cjhy_wxGameActivity.js
export jd_cjhy_wxGameActivity_activityId="Id"
export jd_cjhy_wxGameActivity_num="20"
export jd_cjhy_wxGameActivity_openCard="1"

#【保护环境】cjhy知识超人      feverrun_my_scripts/jd_cjhy_wxKnowledgeActivity.js
export jd_cjhy_wxKnowledgeActivity_activityId="Id"
export jd_cjhy_wxKnowledgeActivity_num="20"
export jd_cjhy_wxKnowledgeActivity_openCard="1"

#【保护环境】cjhy生日礼   feverrun_my_scripts/jd_cjhy_wxMcLevelAndBirthGifts.js
export jd_cjhy_wxMcLevelAndBirthGifts_ids = "id1&id2&id3"
export jd_cjhy_wxMcLevelAndBirthGifts_num="20"
export jd_cjhy_wxMcLevelAndBirthGifts_openCard="1"

#【保护环境】cjhy关注店铺有礼   feverrun_my_scripts/jd_cjhy_wxShopFollowActivity.js
export jd_cjhy_wxShopFollowActivity_activityId=""
export jd_cjhy_wxShopFollowActivity_num="20"
export jd_cjhy_wxShopFollowActivity_openCard="1"

#【保护环境】cjhy店铺礼包      feverrun_my_scripts/jd_cjhy_wxShopGift.js
export jd_cjhy_wxShopGift_ids=""
export jd_cjhy_wxShopGift_num="20"
export jd_cjhy_wxShopGift_openCard="1"

#【保护环境】lzkj七日签到   feverrun_my_scripts/jd_lzkj_sevenDay.js
export jd_lzkj_sevenDay_ids=""
export jd_lzkj_sevenDay_num="20"
export jd_lzkj_sevenDay_openCard="1"

#【保护环境】lzkj签到有礼      feverrun_my_scripts/jd_lzkj_signActivity2.js
export jd_lzkj_signActivity2_ids="id1&id2&id3"
export jd_lzkj_signActivity2_num="20"
export jd_lzkj_signActivity2_openCard="1"

#【保护环境】lzkj盖楼有礼   feverrun_my_scripts/jd_lzkj_wxBuildActivity.js
export jd_lzkj_wxBuildActivity_activityId="Id"
export jd_lzkj_wxBuildActivity_num="20"
export jd_lzkj_wxBuildActivity_openCard="1"

#【保护环境】lzkj加购物车抽奖      feverrun_my_scripts/jd_lzkj_wxCollectionActivity.js
export jd_lzkj_wxCollectionActivityId="id1&id2&id3"
export jd_lzkj_wxCollectionActivity_num="20"
export jd_lzkj_wxCollectionActivity_openCard="1"

#【保护环境】lzkj幸运抽奖   feverrun_my_scripts/jd_lzkj_wxDrawActivity.js
export jd_lzkj_wxDrawActivity_Id=""
export jd_lzkj_wxDrawActivity_num="20"
export jd_lzkj_wxDrawActivity_openCard="1"

#【保护环境】lzkj游戏活动   feverrun_my_scripts/jd_lzkj_wxGameActivity.js
export jd_lzkj_wxGameActivity_activityId=""
export jd_lzkj_wxGameActivity_num="20"
export jd_lzkj_wxGameActivity_openCard="1"

#【保护环境】lzkj知识超人   feverrun_my_scripts/jd_lzkj_wxKnowledgeActivity.js
export jd_lzkj_wxKnowledgeActivity_activityId=""
export jd_lzkj_wxKnowledgeActivity_num="20"
export jd_lzkj_wxKnowledgeActivity_openCard="1"

#【保护环境】lzkj关注店铺有礼   feverrun_my_scripts/jd_lzkj_wxShopFollowActivity.js
export jd_lzkj_wxShopFollowActivity_activityId="20"
export jd_lzkj_wxShopFollowActivity_num="15"
export jd_lzkj_wxShopFollowActivity_openCard="1"

#【保护环境】lzkj店铺礼包   feverrun_my_scripts/jd_lzkj_wxShopGift.js
export jd_lzkj_wxShopGift_ids=""
export jd_lzkj_wxShopGift_num="20"
export jd_lzkj_wxShopGift_openCard="1"

#【保护环境】lzkj_interactsaas加购有礼   feverrun_my_scripts/jd_lzkj_interactsaas_jgyl.js
export jd_lzkj_interactsaas_jgyl_activityId=""
export jd_lzkj_interactsaas_jgyl_num="20"
export jd_lzkj_interactsaas_jgyl_openCard="1"

#【保护环境】lzkj_interactsaas盖楼有礼   feverrun_my_scripts/jd_lzkj_interactsaas_glyl.js
export jd_lzkj_interactsaas_glyl_Ids=""
export jd_lzkj_interactsaas_glyl_num="20"
export jd_lzkj_interactsaas_glyl_openCard="1"

#【保护环境】lzkj_interactsaas日历签到      feverrun_my_scripts/jd_lzkj_interactsaas_rlqd.js
export jd_lzkj_interactsaas_rlqd_Ids=""
export jd_lzkj_interactsaas_rlqd_num="20"
export jd_lzkj_interactsaas_rlqd_openCard="1"

#【保护环境】lzkj_interactsaas签到   feverrun_my_scripts/jd_lzkj_interactsaas_qrqd.js
export jd_lzkj_interactsaas_qrqd_Ids=""
export jd_lzkj_interactsaas_qrqd_num="20"
export jd_lzkj_interactsaas_qrqd_openCard="1"

#【保护环境】lzkj_interactsaas邀请好友入会   feverrun_my_scripts/jd_lzkj_interactsaas_yqhyrh.js
export jd_lzkj_interactsaas_yqhyrh_activityId=""

#【保护环境】lzkj_interactsaas关注店铺有礼   feverrun_my_scripts/jd_lzkj_interactsaas_gzyl.js
export jd_lzkj_interactsaas_gzyl_activityId=""
export jd_lzkj_interactsaas_gzyl_num="20"

#【保护环境】lzkj_interactsaas关注商品有礼   feverrun_my_scripts/jd_lzkj_interactsaas_gzspyl.js
export jd_lzkj_interactsaas_gzspyl_activityId=""
export jd_lzkj_interactsaas_gzspyl_num="20"

#【保护环境】lzkj邀请入会有礼      feverrun_my_scripts/jd_lzkj_interact_yqrhyl.js
export jd_lzkj_interact_yqrhyl_activityId=""

#【保护环境】入会开卡领取礼包通用   feverrun_my_scripts/jd_card_force.js
export VENDER_ID=""
export OPENCARD_BEAN="10"

#【保护环境】txzj 签到   feverrun_my_scripts/jd_txzj_sign_in.js
export jd_txzj_sign_in_id=""
export jd_txzj_sign_in_num="20"

#【保护环境】店铺抽奖   feverrun_my_scripts/jd_shop_draw.js
export jd_shop_draw_ids=""
export jd_shop_draw_num="20"

#【6dy】CJ知识超人通用活动     jd_cjwxKnowledgeActivity.js
export jd_cjwxKnowledgeActivity_activityId=""
export jd_cjwxKnowledgeActivity_num="20"

#【6dy】CJ关注店铺有礼   jd_cjwxShopFollowActivity.js
export jd_cjwxShopFollowActivity_activityId=""
export jd_cjwxShopFollowActivity_num="20"

#【6dy】完善信息有礼   jd_completeInfoActivity.js
export jd_completeInfoActivity_activityId=""
export jd_completeInfoActivity_activityUrl="https://cjhydz-isv.isvjcloud.com"
export jd_completeInfoActivity_venderId=""
export jd_completeInfoActivity_num="20"
export jd_completeInfoActivity_openCard="false"

#【6dy】电脑配件ID任务jd_computer   jd_computer.js
export computer_activityIdList=""

#【6dy】关注有礼-加密     jd_follow.js
export M_FOLLOW_SHOP_ARGV="12237383_12550303"

#【6dy】LZ店铺通用游戏任务   jd_game.js
export WXGAME_ACT_ID=""

#【6dy】店铺积分换豆   jd_point_exchange.js
export POINT_EXCHANGE_URL=""

#【6dy】邀请好友入会赢好礼-火箭版   jd_prodev.js
export prodevactCode=""
export prodevinvitePin="PIN"
export prodevinviteNum="true"

#【6dy】超级无线店铺签到   jd_sevenDay.js
export LZKJ_SEVENDAY="xxxx,xxxx,xxxxx"
export LZKJ_SIGN="xxxx,xxxx,xxxxx"
export CJHY_SEVENDAY="xxxx,xxxx,xxxxx"
export CJHY_SIGN="xxxx,xxxx,xxxxx"

#【6dy】LZ分享有礼   jd_share.js
export jd_fxyl_activityId="1"
export OWN_COOKIE_NUM="1"
export HELP_COOKIE_NUM="40"

#【6dy】店铺左侧刮刮乐   jd_shopDraw.js
export venderId=""
export jd_shopDraw_activityUrl=""

#【6dy】PKC关注有礼-特效   jd_txgzyl.js
export PKC_TXGZYL=""

#【6dy】邀好友赢大礼   jd_yqhy.py
export jd_inv_authorCode=""
```
## 如有问题请联系我 BY [Axiba](https://t.me/lyc_axiba)
