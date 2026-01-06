<script setup>
import { ref, computed, onMounted, nextTick } from 'vue';
import AOS from 'aos';
import 'aos/dist/aos.css';

// --- 状态管理 ---
const currentFilter = ref('all');
const showLightbox = ref(false);
const currentImage = ref('');
// 添加选中项状态管理
const selectedItem = ref(null);
const showDetail = ref(false);

// --- 核心数据 ---
// --- 仅 milestone（发展历程）核心数据 ---
const timelineData = [
  {
    year: "引言",
    type: "milestone",
    title: "我们需要直面的真相",
    content: "",
    img: "/image/founding.png",
    detail: "不可否认的是，4399 是可耻的抄袭者。当我们谈论童年回忆时，必须清醒地认识到：真正带给我们美好时光的，是那些和我们一样热爱游戏的努力的游戏开发者们。他们用创意和汗水打造了一个个精彩的游戏世界，而 4399 却以 整合资源 的名义，将他人的心血据为己有。\n\n然而，4399 确实成为了一个方便的时代代名词。在那个互联网刚刚兴起、游戏资源极度匮乏的年代，它如同一个游戏杂货铺，将世界各地的游戏汇集在一起，让无数中国孩子第一次接触到了电子游戏的魅力。作为主线，它串联起了整整一代人的青春记忆 —— 那些在微机课上偷偷打开的页面、那些和同桌一起玩《森林冰火人》的午后、那些为了通关《造梦西游》而熬夜的夜晚。\n\n当我们回忆起在 4399 上度过的时光时，我们应该感谢的不是 4399，而是那些真正的游戏开发者。是他们用智慧和汗水创造了一个个精彩的游戏世界，是他们的坚持让我们的童年充满了欢乐。\n\n4399 可以被遗忘，但那些优秀的游戏和伟大的开发者应该被铭记。让我们从 4399 的历史中汲取教训，共同创造一个尊重创新、保护知识产权的游戏环境。只有这样，中国的游戏产业才能真正走向辉煌，中国的开发者才能在世界舞台上绽放光彩。\n\n致敬所有热爱游戏的开发者们！\n\n参考资料\n\n[1] 4399小游戏[四三九九网络股份有限公司旗下在线游戏平台]_百科 https://m.baike.com/wiki/4399%E5%B0%8F%E6%B8%B8%E6%88%8F/1609835?baike_source=doubao\n\n[2] 从网瘾少年到亿万富豪，创建hao123、4399，他是如何做到的?_毒sir财经 http://m.toutiao.com/group/7445661444173054527/?upstream_biz=doubao\n\n[3] 4399小游戏当初的创办历程 - 卢松松博客 https://lusongsong.com/info/post/778.html\n\n[4] 4399的兴衰史：从盗版起家到出海转型 https://www.iesdouyin.com/share/video/7357324840799423780/?region=&mid=7357325339632159542&u_code=0&did=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&iid=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&with_sec_did=1&video_share_track_ver=&titleType=title&share_sign=Gt_kbbKkzv5o2t1sOxzLey.lXhthf9WsRd4Svcs8xvs-&share_version=280700&ts=1766670977&from_aid=1128&from_ssr=1&share_track_info=%7B%22link_description_type%22%3A%22%22%7D\n\n[5] 4399页游辉煌与手游转型困境的兴衰历程 https://www.iesdouyin.com/share/video/7446656588422581513/?region=&mid=0&u_code=0&did=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&iid=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&with_sec_did=1&video_share_track_ver=&titleType=title&share_sign=i7WcYogFBMaWK8ZLTE_tapwbkGRoARSsVsNYJfe7nCg-&share_version=280700&ts=1766670977&from_aid=1128&from_ssr=1&share_track_info=%7B%22link_description_type%22%3A%22%22%7D\n\n[6] 4399:游戏圈打不死的“小强”是怎么炼成的 https://finance.sina.com.cn/jjxw/2024-06-07/doc-inaxwtaa4156183.shtml\n\n[7] \"微机课摸鱼神器\"4399的堕落史:从全民童年回忆到盗版氪金地狱_嗨皮游戏侠 http://m.toutiao.com/group/7490783436131533348/?upstream_biz=doubao\n\n[8] 被国内玩家抛弃的4399，在海外“杀”疯了_钛媒体APP http://m.toutiao.com/group/7405838602581623308/?upstream_biz=doubao\n\n[9] 还 记得 那个 承包 我们 童年 的 4399 吗 ？ # 游戏 # 4399 https://www.iesdouyin.com/share/video/7586602265209834806/?region=&mid=7586602462994189107&u_code=0&did=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&iid=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&with_sec_did=1&video_share_track_ver=&titleType=title&share_sign=KP_7Pjz_sU5lIDFmmzdfUQSx5WNmq2Z_RlLwkDg.cBM-&share_version=280700&ts=1766670977&from_aid=1128&from_ssr=1&share_track_info=%7B%22link_description_type%22%3A%22%22%7D\n\n[10] 「字符无限科技」游戏公司介绍“小游戏赢家”4399小游戏_搜狐网 https://m.sohu.com/a/867343600_121984121/\n\n[11] 游戏 回忆录 ： 童年 神站 4399 小 游戏 — — 如何 发家 又 如何 没落 ？ 童年 最好 的 小 游戏 网站 ~ # 童年 游戏 # 4399 # 游戏 杂谈 # 小 游戏 # 回忆 杀 https://www.iesdouyin.com/share/video/7559835495291768115/?region=&mid=7559835509841775387&u_code=0&did=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&iid=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&with_sec_did=1&video_share_track_ver=&titleType=title&share_sign=9PJRs5hIXMmw1EiqXVs6GgCo0KmMyvS7_uP0TDY6QNk-&share_version=280700&ts=1766670977&from_aid=1128&from_ssr=1&share_track_info=%7B%22link_description_type%22%3A%22%22%7D\n\n[12] 承载 童年 记忆 的 4399 ， 是 怎么 爆 火 起来 的 ？ # 4399 # 童年 回忆 # 游戏 https://www.iesdouyin.com/share/video/7324227570759683380/?region=&mid=7324227863698377484&u_code=0&did=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&iid=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&with_sec_did=1&video_share_track_ver=&titleType=title&share_sign=V3NhlISGNwHuRsenP5jgbuW5VLs3wcc5.fLLz5.nlnU-&share_version=280700&ts=1766670977&from_aid=1128&from_ssr=1&share_track_info=%7B%22link_description_type%22%3A%22%22%7D\n\n[13] 还记得4399吗?它在海外悄悄成了小游戏之王|Morketing_全球营销商业媒体平台 https://www.morketing.com/detail/27685\n\n[14] 4399涉嫌侵犯“地下城与勇士”商标权，上市之路雪上加霜-新锐品牌-好听商标网 https://www.ht.cn/anf-id-11-tid-6-aid-13784\n\n[15] 4399从盗版起家到海外创收转型之路 https://www.iesdouyin.com/share/video/7485293898030501131/?region=&mid=7485294417855646502&u_code=0&did=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&iid=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&with_sec_did=1&video_share_track_ver=&titleType=title&share_sign=5B1d2dhEwgA2luoFcjxDuyYooLjsQSCiANiAVjRlb0k-&share_version=280700&ts=1766670993&from_aid=1128&from_ssr=1&share_track_info=%7B%22link_description_type%22%3A%22%22%7D\n\n[16] 抄 DNF 梦幻 守望 先锋 4399 是 如何 作死 的 # 新 游 鉴赏家 # 4399 # 游戏 # 游戏 杂谈 https://www.iesdouyin.com/share/video/7510920632314400012/?region=&mid=7510921403768818444&u_code=0&did=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&iid=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&with_sec_did=1&video_share_track_ver=&titleType=title&share_sign=CtDxeEMEMvb2Pt.qN6p_ybhcOaBAXSYPMJN5s5mXurE-&share_version=280700&ts=1766670993&from_aid=1128&from_ssr=1&share_track_info=%7B%22link_description_type%22%3A%22%22%7D\n\n[17] 阴阳师维权胜诉获赔16万并捐赠红山森林动物园 https://www.iesdouyin.com/share/video/7356247284947881216/?region=&mid=7285268069843225404&u_code=0&did=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&iid=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&with_sec_did=1&video_share_track_ver=&titleType=title&share_sign=vHkU48A0TIVNQx55Raq3XkpWIovU7Uie2lLaRc297MY-&share_version=280700&ts=1766670993&from_aid=1128&from_ssr=1&share_track_info=%7B%22link_description_type%22%3A%22%22%7D\n\n[18] 依靠盗版外国游戏赚钱的游戏网站，抄袭国内游戏被上诉三回 - 哔哩哔哩 https://m.bilibili.com/opus/420709570111968340\n\n[19] 4399抄袭《守望先锋》遭重锤，赔400万还要向暴雪道歉_搜狐网 https://m.sohu.com/a/357330265_120099893\n\n[20] 4399游戏又被告了!因侵权腾讯《地下城勇士》被罚500万|163_手机网易网 https://www.163.com/dy/article/D6LIDOHU05268D0T.html\n\n[21] 4399公司二审被判赔偿腾讯公司500万元--知识产权--人民网 http://ip.people.com.cn/n1/2019/0612/c179663-31132424.html\n\n[22] 中国保护知识产权网 http://ipr.mofcom.gov.cn/article/gnxw/sf/zz/zzbq/202011/1957130.html\n\n[23] 中华人民共和国上海市浦东新区人民法院民事判决书（2017）沪0115民初77923号(pdf) https://www.hshfy.sh.cn/shfy/gweb2017/flws2pdf.jsp?pa=adGFoPaOoMjAxN6Opu6YwMTE1w/Gz9Tc3OTIzusUmd3N4aD00JndzbGI9w/HKwsXQvvbK6QPdcssPdcssz\n\n[24] 《守望先锋》被侵权?浦东法院:射击类游戏整体画面属“类电作品” – 南昌樊翔知识产权律师团队 http://www.vonlu.com/archives/1645\n\n[25] 滕博会官网4399抄袭守望先锋的游戏叫什么?暴雪起诉4399抄袭守望先锋胜诉_皖西学院 https://hlxy.wxc.edu.cn/s/rPNoozilC2.html\n\n[26] 《仙语》剽窃《梦幻西游》二审被判赔偿1500万元_环球网 http://m.toutiao.com/group/6891888916845036045/?upstream_biz=doubao\n\n[27] 《斗罗大陆》游戏侵权案一审宣判 玄霆公司获赔500万元,知产力,为创新聚合知识产权解决方案 https://www.zhichanli.com/p/1283088919\n\n[28] 被国内玩家抛弃的4399，在海外“杀”疯了_钛媒体APP http://m.toutiao.com/group/7405838602581623308/?upstream_biz=doubao\n\n[29] 从网瘾少年到亿万富豪，创建hao123、4399，他是如何做到的?_毒sir财经 http://m.toutiao.com/group/7445661444173054527/?upstream_biz=doubao\n\n[30] 被90后抛弃的4399，在海外和腾讯、网易抢蛋糕-36氪 https://m.36kr.com/p/2669472088387077\n\n[31] 4399历经抄袭风波与海外逆袭重生之路 https://www.iesdouyin.com/share/video/7571330779888815369/?region=&mid=7571330788910795547&u_code=0&did=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&iid=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&with_sec_did=1&video_share_track_ver=&titleType=title&share_sign=9C4cwLtU_6wrchphyUPimmf8RrdBDm17RFjmuONuX1Y-&share_version=280700&ts=1766671008&from_aid=1128&from_ssr=1&share_track_info=%7B%22link_description_type%22%3A%22%22%7D\n\n[32] 4亿人都在玩的网站又㕛叒被告了!90后的童年回忆要粉碎了?_搜狐网 https://m.sohu.com/a/356637651_355083/\n\n[33] 4399小游戏冒险:小游戏平台的生存困境与破局之道-酷音网 http://news.kuyin.cn/article/432089.html\n\n[34] 四三九九董事长骆海坚:深入用户，新3DMMO研发要注意七要素 https://world.huanqiu.com/article/9CaKrnK65o2\n\n[35] 4399游戏乐园:欢乐汇聚，互动热情，畅享无限_搜狐网 https://m.sohu.com/a/869596707_120991886\n\n[36] 关于4399 https://u.4399.com/hr/guanyu\n\n[37] 4399 和 7 千 7 千 你 选 哪 一个 ？ # 童年 回忆 # 小 游戏 https://www.iesdouyin.com/share/video/7503867800122150203/?region=&mid=7103603759300904967&u_code=0&did=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&iid=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&with_sec_did=1&video_share_track_ver=&titleType=title&share_sign=h0SPIyL5g2328eIWr8Mv8DwtlHuqqHNPRR0.xqMQruo-&share_version=280700&ts=1766671008&from_aid=1128&from_ssr=1&share_track_info=%7B%22link_description_type%22%3A%22%22%7D\n\n[38] GMGC|4399骆海坚:4399游戏盒依托5亿PC用户的突围之道_新浪游戏_手机新浪网 https://games.sina.cn/cyfw/cyxw/2017-11-07/detail-ifynnnsc8630213.d.html?from=wap\n\n[39] 4399董事长骆海坚:4399游戏盒是如何做到日活跃350万的? - GameRes游资网 https://www.gameres.com/787751.html\n\n[40] What Happened To Flash Games? 6 Key Factors Behind Their Rise & Fall https://www.geekextreme.com/what-happened-to-flash-games/\n\n[41] Game development in the dying days of Flash https://shaggydev.com/2024/05/15/flash-development/\n\n[42] 挥别Flash，那个动画和页游奔腾的时代落幕了 https://k.sina.cn/article_1652484947_627eeb53020013hn1.html\n\n[43] 页游的发展经历了哪几个阶段_行行查_行业研究数据库 https://www.hanghangcha.com/hhcQuestion/detail/1294419.html\n\n[44] 中国网页游戏市场评估及投资价值研究报告_搜狐网 https://m.sohu.com/a/877635221_121114988/\n\n[45] flash游戏[基于矢量图的游戏形式之一]_百科 https://m.baike.com/wiki/flash%E6%B8%B8%E6%88%8F/263393?baike_source=doubao\n\n[46] 再见了~Flash!网友集体陷入回忆:《黄金矿工》带来的快乐我还记得……_光明网 http://m.toutiao.com/group/6913701083315438088/?upstream_biz=doubao\n\n[47] Flash停止支持后，那些网页小游戏该怎么办?-虎嗅网 https://m.huxiu.com/article/406457.html\n\n[48] 一家国外平台花了三年时间，抢救了36000款即将消亡的Flash游戏_来自大神圈子_游研社 https://m.ds.163.com/article/5e3bb2847c87224be33a3cff/\n\n[49] flash,游戏-flash游戏swf - ningque9 - 博客园 https://www.cnblogs.com/5455pp/p/18956701\n\n[50] Discontinuation of Flash Player support https://support.innogames.com/kb/ForgeOfEmpires/en_DK/970/Discontinuation-of-Flash-Player-support?query=CHANGE%20email\n\n[51] Flipline工作室|奎因的Q& A第二十八期:Flash的终结 - 哔哩哔哩 https://www.bilibili.com/read/cv13220646\n\n[52] 关于Flash被禁用的通知-8090网页游戏 https://www.8090.com/zzcs3/xinwen/427211.html\n\n[53] Nitrome 基础知识手册 - 哔哩哔哩 https://m.bilibili.com/opus/566235599116256969\n\n[54] Nitrome - 萌娘百科_万物皆可萌的百科全书 https://zh.moegirl.org.cn/Nitrome\n\n[55] Nitrome - NitromeWIKI_BWIKI_哔哩哔哩 https://wiki.biligame.com/nitrome/Nitrome\n\n[56] Nitrome https://nitrome.fandom.com/wiki/Nitrome\n\n[57] Nirtrome公司介绍(中) - 哔哩哔哩 https://www.bilibili.com/read/cv3398053/\n\n[58] 黄金矿工隐藏剧情与真结局揭秘 https://www.iesdouyin.com/share/video/7512092717489376531/?region=&mid=7512093958865029939&u_code=0&did=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&iid=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&with_sec_did=1&video_share_track_ver=&titleType=title&share_sign=KgsX5EDr2lwElJ80rqc.5xTICOV628.fgI8d6eVXNsg-&share_version=280700&ts=1766671045&from_aid=1128&from_ssr=1&share_track_info=%7B%22link_description_type%22%3A%22%22%7D\n\n[59] 黄金矿工系列大结局揭秘与幕后开发故事 https://www.iesdouyin.com/share/video/7372882689591938316/?region=&mid=7372883140785785610&u_code=0&did=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&iid=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&with_sec_did=1&video_share_track_ver=&titleType=title&share_sign=24dB6m8AWyd8TgFvE4E2SbLDjekjjY1NMsVuffOv8c0-&share_version=280700&ts=1766671045&from_aid=1128&from_ssr=1&share_track_info=%7B%22link_description_type%22%3A%22%22%7D\n\n[60] 童年玩的《黄金矿工》，都不是完整版? - Ran的动态 - TapTap https://www.taptap.cn/moment/689987807623512336\n\n[61] 五款4399经典小游戏，你最希望哪款出手游?_4399游戏盒 http://m.toutiao.com/group/6957612888626381316/?upstream_biz=doubao\n\n[62] 黄金矿工 - 萌娘百科_万物皆可萌的百科全书 https://mzh.moegirl.org.cn/%E9%BB%84%E9%87%91%E7%9F%BF%E5%B7%A5\n\n[63] 黄金矿工下载_黄金矿工安卓版下载-4399手机游戏网 http://a.4399.cn/mobile/168669.html\n\n[64] 没有了黄暴游戏的4399，还是你的童年吗?_3DM专栏 http://www.3dmgame.com/original/3742871.html\n\n[65] 宝开游戏公司[美国的游戏开发商和发行商]_百科 https://m.baike.com/wiki/%E5%AE%9D%E5%BC%80%E6%B8%B8%E6%88%8F%E5%85%AC%E5%8F%B8/2824083?baike_source=doubao\n\n[66] 宝开游戏 - 萌娘百科_万物皆可萌的百科全书 https://zh.moegirl.org.cn/%E5%AE%9D%E5%BC%80%E6%B8%B8%E6%88%8F\n\n[67] 宝开:作为一家休闲游戏公司，它的创始人却有着嬉皮士精神_3DM专栏 https://m.3dmgame.com/original/3742101.html\n\n[68] 植物大战僵尸创始人自述:从0到1亿_liuyukuan的技术博客_51CTO博客 https://blog.51cto.com/u_15408625/6412865\n\n[69] 游戏霸主如今频频裁员，植物大战僵尸这些年到底怎么了? | 界面 · 财经号 https://m.jiemian.com/article/1428163.html\n\n[70] 宝开经历的崛起、没落与新生_新浪游戏_手机新浪网 http://games.sina.cn/cyfw/cyxw/2019-12-16/detail-iihnzahi7915736.d.html\n\n[71] 造梦西游4_360应用 https://app.so.com/detail/index?id=3950003&pt=oauth\n\n[72] 造梦西游OL运营归属与玩家怀旧历程解析 https://www.iesdouyin.com/share/video/7221118076689992972/?region=&mid=7221118408505428793&u_code=0&did=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&iid=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&with_sec_did=1&video_share_track_ver=&titleType=title&share_sign=04qvy_nuVGtNzG.66lky41cHBmreOrJqfFaSE87l3Mk-&share_version=280700&ts=1766671049&from_aid=1128&from_ssr=1&share_track_info=%7B%22link_description_type%22%3A%22%22%7D\n\n[73] 造梦无双 - 萌娘百科_万物皆可萌的百科全书 https://mzh.moegirl.org.cn/%E9%80%A0%E6%A2%A6%E6%97%A0%E5%8F%8C\n\n[74] 浅谈造梦西游 - 哔哩哔哩 https://www.bilibili.com/opus/674834702151974964\n\n[75] 洪荒大劫篇孙悟空棍系心法觉醒推荐 悟空觉醒技能推荐_造梦西游online洪荒大劫篇 https://m.news.4399.com/gonglue/zmxy4/miji/700361.html\n\n[76] 造梦西游4游戏下载-造梦西游4游戏安卓手机版下载v3.0.1.4 - 游戏鸟 https://m.youxiniao.com/game/zmxygb22/\n\n[77] 造梦西游OL官方下载-造梦西游OL游戏免费下载-造梦西游OL电脑版-应用宝官网 https://sj.qq.com/myapp/detail.htm?apkCode=830&apkName=com.tencent.tmgp.zmxyol\n\n[78] 从 微机 课 爆款 到 版权 危机 ， 探寻 死神 VS 火影 的 兴衰史 # 死神 VS 火影 # 4399 # 单机 游戏 https://www.iesdouyin.com/share/video/7501271128313269567/?region=&mid=7501271626873309967&u_code=0&did=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&iid=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&with_sec_did=1&video_share_track_ver=&titleType=title&share_sign=EiqWS0aQifDm6xUzAeEYulI53WHtZMXckLroZLVURI8-&share_version=280700&ts=1766671055&from_aid=1128&from_ssr=1&share_track_info=%7B%22link_description_type%22%3A%22%22%7D\n\n[79] 死神vs火影 - 萌娘百科_万物皆可萌的百科全书 https://zh.moegirl.org.cn/%E6%AD%BB%E7%A5%9Evs%E7%81%AB%E5%BD%B1\n\n[80] 《死神VS火影》版本更新与玩家社区发展历程 https://www.iesdouyin.com/share/video/7323551431468911899/?region=&mid=7323551908181101366&u_code=0&did=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&iid=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&with_sec_did=1&video_share_track_ver=&titleType=title&share_sign=c6cmzETvWXWD2NInJAwxHiIagrYm_15mQhVCf.LlmqQ-&share_version=280700&ts=1766671055&from_aid=1128&from_ssr=1&share_track_info=%7B%22link_description_type%22%3A%22%22%7D\n\n[81] 《死神VS火影》十余年发展历程：从Flash爆款到硬核 https://www.iesdouyin.com/share/video/7373318403085339941/?region=&mid=7373318536883751716&u_code=0&did=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&iid=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&with_sec_did=1&video_share_track_ver=&titleType=title&share_sign=kkXUiAemxUl71lZrqNU6vMm5vHMHAurFq24M7sxtlvU-&share_version=280700&ts=1766671055&from_aid=1128&from_ssr=1&share_track_info=%7B%22link_description_type%22%3A%22%22%7D\n\n[82] 《死神VS火影》开发历程与背后的故事 https://www.iesdouyin.com/share/video/7098536043330227486/?region=&mid=0&u_code=0&did=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&iid=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&with_sec_did=1&video_share_track_ver=&titleType=title&share_sign=s.99lEvcB.rgOPAfnICjqn0GuUen63a5tzswnMgE6_0-&share_version=280700&ts=1766671055&from_aid=1128&from_ssr=1&share_track_info=%7B%22link_description_type%22%3A%22%22%7D\n\n[83] 童年回忆杀!《死神VS火影》那个4399的童年 - 哔哩哔哩 https://m.bilibili.com/opus/397676532387501807\n\n[84] 《死神VS火影》经久不衰：原著技能还原与青春记忆的 https://www.iesdouyin.com/share/video/7477885819655687433/?region=&mid=7477885776575990539&u_code=0&did=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&iid=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&with_sec_did=1&video_share_track_ver=&titleType=title&share_sign=A3j4y56nrYAyLLUYJ.9JI7aLEbhIMCF1v6kD1coZWbE-&share_version=280700&ts=1766671055&from_aid=1128&from_ssr=1&share_track_info=%7B%22link_description_type%22%3A%22%22%7D\n\n[85] 魂斗罗[1987年由日本Konami公司推出的横向卷轴射击游戏]_百科 https://m.baike.com/wiki/%E9%AD%82%E6%96%97%E7%BD%97/19473912?baike_source=doubao\n\n[86] 魂斗罗免费下载_魂斗罗 2025最新手机版下载安装 - 233乐园 https://www.233leyuan.com/game-detail/2437661\n\n[87] 《魂斗罗》游戏背景、起源与经典元素解析 https://www.iesdouyin.com/share/video/7266415278328761661/?region=&mid=7266415550459710267&u_code=0&did=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&iid=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&with_sec_did=1&video_share_track_ver=&titleType=title&share_sign=l5KJyz_LrIF_UvYLZ7b5YfE9SY9K8bJRuJoeMrl9E8Y-&share_version=280700&ts=1766671055&from_aid=1128&from_ssr=1&share_track_info=%7B%22link_description_type%22%3A%22%22%7D\n\n[88] 魂斗罗 [Contra] - GameWiki https://wiki.nesbbs.com/index.php/Contra\n\n[89] 魂斗罗 - 游戏懂哥的动态 - TapTap https://www.taptap.cn/moment/580003519105335884\n\n[90] 经典游戏《合金弹头》系列历史与玩法解析 https://www.iesdouyin.com/share/video/7105384859027229960/?region=&mid=7105385080671013669&u_code=0&did=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&iid=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&with_sec_did=1&video_share_track_ver=&titleType=title&share_sign=rOgD7TY5XWdYyvcuteeA0Qu35jWiUGDP7Tbct0iE4h0-&share_version=280700&ts=1766671055&from_aid=1128&from_ssr=1&share_track_info=%7B%22link_description_type%22%3A%22%22%7D\n\n[91] 合金弹头、月华剑士系列_网易新闻 https://www.163.com/game/article/B0JFIM3E00314K8K_4_mobile.html\n\n[92] 4399弹弹堂_百科 https://m.baike.com/wiki/4399%E5%BC%B9%E5%BC%B9%E5%A0%82/4592335?baike_source=doubao\n\n[93] 「字符无限科技」游戏公司介绍“小游戏赢家”4399小游戏_搜狐网 https://m.sohu.com/a/867343600_121984121/\n\n[94] 弹弹堂兴衰史：第七大道的页游崛起与落寞 https://www.iesdouyin.com/share/video/6843709727000562958/?region=&mid=6843709833955379975&u_code=0&did=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&iid=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&with_sec_did=1&video_share_track_ver=&titleType=title&share_sign=Z0nZzX_YIGjGujBvGrADcGrtJQJd8_DrP6KNkf9dQMQ-&share_version=280700&ts=1766671061&from_aid=1128&from_ssr=1&share_track_info=%7B%22link_description_type%22%3A%22%22%7D\n\n[95] 弹弹堂app-官方正版软件2025最新版游戏免费下载-应用宝官网 https://sj.qq.com/appdetail/com.tencent.tmgp.ddtank?from_wxz=1%2Freview%2Fblog%2Fblog\n\n[96] 弹弹堂_网页游戏大全_网页游戏下载_在线玩_QQ游戏官网 https://qqgame.qq.com/app/gamedetail_10985.shtml\n\n[97] 摩尔庄园app-官方正版软件2025最新版游戏免费下载-应用宝官网 https://sj.qq.com/appdetail/com.tencent.tmgp.molenew\n\n[98] 4399 Korea https://thewiki.kr/w/4399%20Korea\n\n[99] 重温闪翼双星：双人合作解谜与隐藏剧情探索 https://www.iesdouyin.com/share/video/7466763359321328906/?region=&mid=7466765289686977289&u_code=0&did=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&iid=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&with_sec_did=1&video_share_track_ver=&titleType=title&share_sign=kN1NnC3J7m_HUm4qp3iAkVQFrPsAZmXxq_wD2DTuJAY-&share_version=280700&ts=1766671061&from_aid=1128&from_ssr=1&share_track_info=%7B%22link_description_type%22%3A%22%22%7D\n\n[100] 《闪翼双星》：童年双人闯关游戏的经典挑战 https://www.iesdouyin.com/share/video/7291238732924013835/?region=&mid=7291238913241402149&u_code=0&did=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&iid=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&with_sec_did=1&video_share_track_ver=&titleType=title&share_sign=Yx3M.nhvvgGFsg5VmFt1scvctDKqo82YFHrpZpNoYD0-&share_version=280700&ts=1766671061&from_aid=1128&from_ssr=1&share_track_info=%7B%22link_description_type%22%3A%22%22%7D\n\n[101] 闪翼双星游戏攻略秘籍_闪翼双星攻略大全_高分技巧_九游手机游戏 https://a.9game.cn/shanyishuangxing/gonglue-0-1/\n\n[102] 《森林冰火人》十二年六代历程终迎大结局 https://www.iesdouyin.com/share/video/7349496555306634505/?region=&mid=7349497140367641371&u_code=0&did=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&iid=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&with_sec_did=1&video_share_track_ver=&titleType=title&share_sign=n8xfKlcml3H7hBCt6bULEk0B4FRgow7SaRnhF9_dz2c-&share_version=280700&ts=1766671061&from_aid=1128&from_ssr=1&share_track_info=%7B%22link_description_type%22%3A%22%22%7D\n\n[103] 森林冰火人竟是兄妹?揭秘童年游戏开发者与其背后的故事_Steam每日精彩 http://m.toutiao.com/group/7000118068047282702/?upstream_biz=doubao\n\n[104] 闪翼双星和闪翼工作室的发展史 - 哔哩哔哩 https://www.bilibili.com/opus/1025558634622353414\n\n[105] 经典双人游戏《森林冰火人》的神殿解谜与童年回忆 https://www.iesdouyin.com/share/video/7301206483742625074/?region=&mid=7301206665028717338&u_code=0&did=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&iid=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&with_sec_did=1&video_share_track_ver=&titleType=title&share_sign=UdQfsY17tGRliGvNV7uQtDPIE_apbCbVcuFqXn8BLRQ-&share_version=280700&ts=1766671061&from_aid=1128&from_ssr=1&share_track_info=%7B%22link_description_type%22%3A%22%22%7D\n\n[106] 4399平台的兴衰历程与中国互联网游戏盗版时代 https://www.iesdouyin.com/share/video/7371012829651995958/?region=&mid=7371012948208126747&u_code=0&did=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&iid=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&with_sec_did=1&video_share_track_ver=&titleType=title&share_sign=OR3pzFphJWui2A3EkmsEmT.fYe9E_8Sm0E8n5SdXI9o-&share_version=280700&ts=1766671073&from_aid=1128&from_ssr=1&share_track_info=%7B%22link_description_type%22%3A%22%22%7D\n\n[107] 4399游戏平台的盗版起家与行业影响 https://www.iesdouyin.com/share/video/7273811040075353398/?region=&mid=7273811222556969765&u_code=0&did=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&iid=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&with_sec_did=1&video_share_track_ver=&titleType=title&share_sign=ZTbvhq1MmfCbTfO7QbCp20ymgL34XziW6CYpT7fM0WI-&share_version=280700&ts=1766671073&from_aid=1128&from_ssr=1&share_track_info=%7B%22link_description_type%22%3A%22%22%7D\n\n[108] 4399游戏平台长期陷抄袭争议并遭多起法律 https://www.iesdouyin.com/share/video/6901871773197929741/?region=&mid=6901871802411240206&u_code=0&did=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&iid=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&with_sec_did=1&video_share_track_ver=&titleType=title&share_sign=N08l3mRBbFgTYdv0ebWNaNuGopLVjm.R.u6TUA.75LQ-&share_version=280700&ts=1766671073&from_aid=1128&from_ssr=1&share_track_info=%7B%22link_description_type%22%3A%22%22%7D\n\n[109] 上海市高级人民法院网--裁判文书 https://www.hshfy.sh.cn/shfy/web/flws_view.jsp?pa=adGFoPaOoMjAxN6Opu6YwMTE1w/Gz9Tc3OTIzusUmd3N4aD0xz\n\n[110] 4亿人都在玩的网站又㕛叒被告了!90后的童年回忆要粉碎了?_搜狐网 https://m.sohu.com/a/356637651_355083/\n\n[111] 关于4399有什么大型网游下架合集 - 哔哩哔哩 https://www.bilibili.com/opus/831570496770277384\n\n[112] 4399游戏平台：从盗版起家到正版转型的发展 https://www.iesdouyin.com/share/video/7309440138017246516/?region=&mid=7309440246415330074&u_code=0&did=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&iid=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&with_sec_did=1&video_share_track_ver=&titleType=title&share_sign=TMXOVjHioFtXj603FIerHfKem2M0bnywJb3Byg0LXA4-&share_version=280700&ts=1766671073&from_aid=1128&from_ssr=1&share_track_info=%7B%22link_description_type%22%3A%22%22%7D\n\n[113] \"微机课摸鱼神器\"4399的堕落史:从全民童年回忆到盗版氪金地狱_嗨皮游戏侠 http://m.toutiao.com/group/7490783436131533348/?upstream_biz=doubao\n\n[114] 开放平台协议 https://open.4399.cn/docs/#/agreement/platform\n\n[115] 95后的童年都被这个网站承包了，但你知道它的底色是盗版和黄暴?_隔夜也很宅 http://m.toutiao.com/group/6969504563493667339/?upstream_biz=doubao\n\n[116] 4399早期盗版游戏起家史 https://www.iesdouyin.com/share/video/7168051299324415244/?region=&mid=7168051325719104293&u_code=0&did=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&iid=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&with_sec_did=1&video_share_track_ver=&titleType=title&share_sign=LiaUkXppvfriCh8sJmClUckkO1UZnbmZzgI4LxLNPKg-&share_version=280700&ts=1766671073&from_aid=1128&from_ssr=1&share_track_info=%7B%22link_description_type%22%3A%22%22%7D\n\n[117] 4399小游戏盗版历史与网页游戏创新之路 https://www.iesdouyin.com/share/video/7057446911934057759\n\n[118] 4399小游戏平台的盗版起家与转型之路 https://www.iesdouyin.com/share/video/6951677914891013383/?region=&mid=0&u_code=0&did=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&iid=MS4wLjABAAAANwkJuWIRFOzg5uCpDRpMj4OX-QryoDgn-yYlXQnRwQQ&with_sec_did=1&video_share_track_ver=&titleType=title&share_sign=9ij_gs2gVDSyJxwvsTPn2LR_yRk4cTDtH9slq61BVuU-&share_version=280700&ts=1766671073&from_aid=1128&from_ssr=1&share_track_info=%7B%22link_description_type%22%3A%22%22%7D\n\n[119] “4399”无法抹去的黑历史!曾葬送了无数游戏制作者的网站!|dnf|地下城|小游戏|游戏_手机网易网 https://www.163.com/dy/article/G3PS38I10532QRIN.html"
  },
    {
    year: "2002–2004",
    type: "milestone",
    title: "草根起源：Hao123 的影子与 4399 的雏形",
    content: "李兴平从网吧管理员起步，自学 HTML，先后制作网址导航（hao123）并出售给百度，随后复制这种集合型网站模式，4399 由此诞生（不同资料显示创立时间为2002或2004年）。",
    img: "/image/founding.png",
    detail: "4399 的故事要从一个叫李兴平的年轻人说起。1999 年，这位只有初中学历的广东兴宁小伙在网吧做网管，为了解决顾客记不住网址的问题自学 HTML，制作了“网址大全”（即 hao123）。\n\n2004 年李兴平以 1190 万人民币加 4 万股百度股票将 hao123 卖给百度，此后复制成功模式推出 ip 查询网站 ip138、音乐播放器 7789 等多款集合性网站，其中最成功的就是 4399 小游戏平台。\n\n关于 4399 的具体创立时间，存在 2002 年与 2004 年 9 月两种说法。\n\n早期 4399 主要通过“资源整合”模式，将互联网上的小游戏破解后直接上线，本质为盗版抄袭，以此迅速聚集用户。"
  },
  {
    year: "2008",
    type: "milestone",
    title: "重要转折：天使投资入股与进军网页游戏",
    content: "2008 年，天使投资人蔡文胜入股 4399，并引入骆海坚负责网页游戏联运，开始正式进军儿童页游与网页游戏市场。",
    img: "/image/2008_invest.png",
    detail: "2008 年是 4399 发展的重要转折点——著名天使投资人蔡文胜通过投资方式入股 4399，并带来了另一位创始人骆海坚负责网页游戏联运业务。\n\n同年，4399 与哇呀呀、淘米合作，引入《时空港》《摩尔庄园》等儿童页游，标志着公司从小游戏汇聚向自主运营/联运页游的战略转型，正式进军网页游戏市场。"
  },
  {
    year: "2009–2015",
    type: "milestone",
    title: "黄金时代：网页小游戏与高速扩张",
    content: "进入 2009 年后，4399 进入快速扩张期，期间财务与用户数据均出现大幅增长，成为国内小游戏代名词。",
    img: "/image/golden_age.png",
    detail: "2009 年起 4399 获得隆领投资 100 万元天使轮融资并快速扩张：2011 年净利润突破 1.16 亿元并启动开发/发行/平台一体化战略；2012 年成立韩国子公司、上线手游平台并举办首届 Flash 开发大赛；2013 年 4399 游戏盒上线，平台月流水突破 1 亿元，净利润增长至 2.49 亿元；2016 年小游戏月独立访问用户（MUA）约 5280 万，平台累计注册用户突破 6 亿。\n\n此阶段从《黄金矿工》到《植物大战僵尸》，从《造梦西游》到《赛尔号》，无数经典游戏通过 4399 进入玩家视野，但平台上 95% 的游戏未经授权，直接扒掉国外游戏 logo 就上线，抄袭争议日益凸显。"
  },
  {
    year: "2014–2017",
    type: "milestone",
    title: "两次 IPO 冲击与内部风波",
    content: "4399 两次冲击上市均失败：2014 年首次被证监会叫停，2017 年第二次冲击又遭实名举报与舆论风波，上市计划两度受挫。",
    img: "/image/ipo_fail.png",
    detail: "4399 曾两次冲击上市但均以失败告终。\n\n2014 年首次上市申请因申请文件不齐备（传闻称未及时提交 2014 年下半年及全年财报）被证监会叫停，背后暴露了复杂的股权结构和管理层内斗问题。\n\n2017 年第二次冲击期间，上海鼎麟股权投资基金的股东蒋和平与李胜利召开“以韭菜的名义”记者发布会，举报 4399 隐瞒重大事实，意图欺骗上市，同时蔡文胜被指控涉嫌偷税漏税（蔡文胜澄清 2013 年已退出管理层），负面消息导致上市计划再次暂停。"
  },
  {
    year: "2015–2017",
    type: "milestone",
    title: "版权纠纷高发：被多家大厂诉讼并判赔",
    content: "随着版权意识抬头，4399 遭遇大量知识产权诉讼并多次败诉，赔偿金额与案件数对公司造成沉重打击。",
    img: "/image/lawsuit.png",
    detail: "2015 年成为 4399 由盛转衰的关键节点，版权问题逐渐成为发展桎梏。\n\n2016 年，4399 因涉嫌侵犯《地下城与勇士》商标权，被判向腾讯赔偿 500 万元，这是游戏商标搜索引擎关键词侵权纠纷诉讼中判赔额最高的案件。\n\n2017 年更是“诉讼年”：网易、暴雪联合起诉《英雄枪战》《枪战前线》涉嫌侵权《守望先锋》，判赔 397 万元；网易起诉《仙语》侵权《梦幻西游》，判赔 1500 万元；腾讯再次就《格斗猎人》侵犯“DNF”商标权一案，判赔 500 万元。\n\n公司招股书中记录的知识产权诉讼多达 23 起，截至 2024 年，相关知识产权判决数量已达 111 起，涉及腾讯、网易、暴雪、盛大、多益网络等多家厂商。"
  },
  {
    year: "2017–2020",
    type: "milestone",
    title: "Flash 停更与版号寒冬，核心生态崩塌",
    content: "Adobe 宣布终止 Flash 支持并在 2020 年停止所有支持；同时国内游戏版号政策收紧，严重冲击以 Flash 游戏为核心的 4399。",
    img: "/image/flash_end.png",
    detail: "2017 年 7 月 26 日，Adobe 公司宣布计划终结 Flash Player 插件，并在 2020 年 12 月 31 日停止所有支持，2021 年 1 月 12 日起开始阻止 Flash 内容运行。\n\n对于依赖 Flash 游戏起家的 4399 来说，这一变化是毁灭性打击——Flash 技术存在严重安全漏洞（2015 年 7 月单月修复 38 个漏洞）和性能问题，已被 HTML5 逐步替代，全球超过 98% 的浏览器不再原生支持 Flash 内容。\n\n此外，2018 年中国暂停发放游戏版号，引发“版号寒冬”，进一步限制了国内新游戏上线与变现路径，4399 第二次冲击上市也在此期间被举报暂停。"
  },
  {
    year: "2018–2024",
    type: "milestone",
    title: "求生与转型：加速出海并取得局部成功",
    content: "面对国内困境，4399 推行出海战略并取得若干成功产品，但国内品牌与用户基础已大不如前。",
    img: "/image/global.png",
    detail: "2018 年版号停发后，中国游戏市场全面鼓励出海，4399 抓住这一机会启动出海战略。\n\n2019 年上线的《奇迹之剑》成功攻破日韩市场，一个月内登上韩国手游畅销榜榜首，2020-2022 年流水分别为 11.1 亿元、11.6 亿元、4.46 亿元，连续两年跻身韩国手游收入 TOP5，是其中唯一的中国游戏。\n\n2022 年上线的 MMORPG《秘境传说：神木遗迹》横扫全球市场，2022 年和 2023 年流水分别为 7.47 亿元、5.31 亿元。\n\n2023 年推出的放置类游戏《菇勇者传说》成为出海新王牌，全球内购收入达到 2.7 亿美元（约 19.6 亿元人民币），2024 年 3 月单月收入 3.93 亿元，占 4399 海外手游业务总收入的 86%。\n\n尽管出海取得成绩，但并未完全弥补国内用户与品牌流失带来的整体下滑。"
  },
  {
    year: "2023",
    type: "milestone",
    title: "日薄西山：用户流失、技术与品牌问题显现",
    content: "到 2023 年，4399 在国内的日均活跃用户首次跌破百万，曾经的流量优势与品牌力已大幅衰减。",
    img: "/image/decline.png",
    detail: "曾经日活跃用户超过 2000 万的 4399，到 2023 年日均活跃用户首次跌破百万。\n\n技术上，随着 Flash 的终结，其核心竞争力彻底丧失；品牌方面，长期的抄袭恶名让越来越多的玩家选择离开；同时手游的兴起让网页游戏失去生存空间，国内市场已进入长期下行态势。"
  },
  {
    year: "1996–2005",
    type: "flash",
    title: "Flash 技术的兴起：从 FutureSplash 到 Adobe 的收购",
    content: "Flash（前身 FutureSplash）被 Macromedia 收购并命名为 Flash，随后于 2005 年被 Adobe 以 34 亿美元收购，技术得到加速发展，彻底改变了网页游戏的表现力。",
    img: "/image/flash_history.png",
    detail: "Flash 的前身叫做 FutureSplash，1996 年 11 月被 Macromedia 收购后改名为 Flash 1.0。\n\n2005 年，Macromedia 被 Adobe 以 34 亿美元收购，Flash 技术得到进一步发展。\n\nFlash 技术的出现彻底改变了网页游戏的面貌——在它之前，网页游戏只能是简单的文字冒险游戏（MUDs），而 Flash 让开发者能够创造出富有图形和流畅动画的游戏内容，为网页游戏的爆发奠定了技术基础。"
  },
  {
    year: "1999–2008",
    type: "flash",
    title: "Flash 游戏的黄金时代（代表性作品与文化中心）",
    content: "以 Tom Fulp 的《Pico's School》为标志的 Flash 游戏时代催生了大量创意独立作品，2007–2008 年更被视为浏览器游戏的巅峰期，Newgrounds 等社区成为独立开发者的展示平台。",
    img: "/image/flash_golden.png",
    detail: "1999 年，Tom Fulp 的《Pico's School》标志着真正 Flash 时代的开始。\n\n到 2007-2008 年，Flash 游戏达到黄金时代，被认为是浏览器游戏的绝对巅峰，诞生了《Trials》（极限摩托车）、《Alien Hominid》（外星原人）、《VVVVVV》、《Canabalt》（涂鸦跳跃）等经典作品。\n\n这些游戏不仅在技术上达到当时最高水准，更展现了独立开发者的创意自由，Newgrounds 等网站成为文化中心，让独立开发者能向全球数百万玩家展示自己的作品。"
  },
  {
    year: "2010–2021",
    type: "flash",
    title: "衰落与终结：安全、性能与替代技术的崛起",
    content: "2010 年代起，Flash 因安全与性能问题开始走下坡路；HTML5 等现代网页技术崛起并取代 Flash，Adobe 在 2017 年宣布 2020 年停止支持，2020 年底正式终止服务并逐步阻止内容运行。",
    img: "/image/flash_end.png",
    detail: "Flash 的辉煌并未持续太久，2010 年代早期开始走下坡路。\n\n安全问题是最大隐患，黑客常攻击 Flash 因其高系统权限，一旦攻破可深入访问用户机器；性能问题也日益严重，占用大量系统资源，在移动设备上表现糟糕，乔布斯曾公开批评其不安全性和资源密集性，拒绝在苹果设备上支持 Flash。\n\n更重要的是技术替代的出现——HTML5 无需插件、移动兼容性更好、资源消耗更低，各大浏览器开始默认屏蔽 Flash 内容。\n\n2017 年 7 月，Adobe 正式宣布将在 2020 年停止 Flash 支持，2020 年 12 月 31 日彻底停止所有支持，2021 年 1 月 12 日起阻止 Flash 内容运行，全球超过 98% 的浏览器已不再原生支持 Flash 内容。"
  },
  {
    year: "2003",
    type: "game",
    title: "《黄金矿工》：被免费的淘金梦",
    content: "美国插画家Dan Glover的原创游戏，被4399未经授权破译搬运，原作者未获任何收益，成为平台经典“免费”游戏。",
    dev: "真正开发者：Dan Glover（GameRival）",
    img: "/image/gold-miner.png",
    detail: "2003 年，学插画的美国大学生 Dan Glover 出于兴趣，在朋友 Malcolm Michaels 运营的 GameRival 网站上发布了《黄金矿工》。\n\n游戏创作灵感来自加利福尼亚淘金热的历史背景，最初 Dan 计划开发捕鱼游戏，后因觉得黄金更具吸引力调整方向，原版完整版售价为 19.95 美元。\n\n4399 未经 GameRival 公司允许，擅自破译并搬运到国内，严重损害制作方权益，中国玩家长期误以为这是 4399 原生游戏，而真正的开发者 Dan Glover 未获得任何收益。"
  },
  {
    year: "2008",
    type: "game",
    title: "《摩尔庄园》：淘米的虚拟社区童话",
    content: "上海淘米网络开发的儿童虚拟社区养成游戏，4399通过联运获得运营权，却常被包装为平台原创产品。",
    dev: "真正开发者：上海淘米网络科技有限公司",
    img: "/image/moer-manor-detail.png",
    detail: "2008 年，《摩尔庄园》与 4399 合作上线，这是一款虚拟社区养成游戏，玩家扮演可爱的小摩尔在庄园里生活、交友，凭借温馨氛围和丰富互动玩法，成为无数 00 后的童年记忆。\n\n4399 通过与淘米的合作获得运营权，借助平台流量让游戏迅速普及，但在宣传中刻意淡化淘米的开发者身份，导致大量小玩家及家长误以为这是 4399 自主开发的游戏，混淆了运营与创作的边界。"
  },
  {
    year: "2009",
    type: "game",
    title: "《植物大战僵尸》：被“原创”的塔防革命",
    content: "美国PopCap Games的现象级作品，4399扒掉logo后上线，让无数中国玩家误以为是平台原创游戏。",
    dev: "真正开发者：PopCap Games（美国）",
    img: "/image/plants-vs-zombies.png",
    detail: "2009 年 5 月 5 日，美国 PopCap Games 公司正式发售《植物大战僵尸》，这款融合塔防、策略和幽默元素的游戏迅速风靡全球。\n\nPopCap 由 John Vechey、Brian Fiete 和 Jason Kapalka 三人于 2000 年创立，创立前 John Vechey 在普渡大学期间开发的彩弹游戏 ARC 吸引众多玩家，三人将其所有权以 10 万美元卖给 Sierra 游戏公司，并用这笔钱创立了 PopCap。\n\n4399 未经授权便将《植物大战僵尸》扒掉 logo 后上线，未给开发者任何分成，让无数中国玩家误以为这是 4399 的“原创”游戏。"
  },
  {
    year: "2009",
    type: "game",
    title: "《森林冰火人》：西班牙的双人解谜经典",
    content: "西班牙开发者Jurana制作的双人合作解谜游戏，4399整合后隐瞒来源，玩家误将兄妹认作情侣。",
    dev: "真正开发者：Jurana（西班牙巴塞罗那，ID：Joliner）",
    img: "/image/forest-ice-fire.png",
    detail: "《森林冰火人》原名《Fiber and Water Go》，由西班牙巴塞罗那的开发者 Jurana（ID 为 Joliner）制作，2009 年以《FOREST TEMPLE》的名字发布在互联网上。\n\n核心玩法是双人合作解谜，玩家需操控代表火元素的男孩和水元素的女孩，利用各自特性通过各种机关和障碍。\n\n有趣的是，很多中国玩家以为两个角色是情侣，实则为兄妹关系。\n\n4399 将这款独立游戏“整合”后，成为平台最受欢迎的双人游戏之一，但真正的开发者 Jurana 却鲜为人知，未获得任何版权收益。"
  },
  {
    year: "2004-2010",
    type: "game",
    title: "Nitrome像素合集：被批量侵权的英国创意",
    content: "英国独立工作室Nitrome的130余款像素精品遭4399整合，《坏蛋冰淇淋》《双刃战士》等成为中国玩家童年记忆。",
    dev: "真正开发者：Nitrome Limited（英国伦敦，2004年成立）",
    img: "/image/nitrome-games.png",
    detail: "Nitrome 由 Mat Annal 和 Heather Stancliffe 于 2004 年 8 月 10 日在英国伦敦创立，以独特的像素艺术风格（灵感来自 8 位和 16 位游戏，特别是日本开发者作品）、创意玩法和高品质制作品质，在全球 Flash 游戏界享有盛誉。\n\n其在官网发布了超过 130 款免费游戏，包括《Bad Icecream》（坏蛋冰淇淋）、《Double Edged》（双刃战士）、《Rubble Trouble》（高楼爆破）、《Twin Shot》（肥猫天使/双箭头）等经典。\n\n这些游戏全部被 4399 无授权整合，而 Nitrome 作为小型独立工作室，面对跨国维权的高昂成本，只能无奈看着作品被侵权。\n\n2024 年，《双箭头》推出 Steam 重置版《Twin Shot Deluxe》，支持 4 人合作与对战。"
  },
  {
    year: "2006-2007",
    type: "game",
    title: "《死神 VS 火影》：剑大的格斗梦",
    content: "中国开发者“剑大”制作的同人格斗游戏，4399直接整合，让玩家误以为是平台官方作品。",
    dev: "真正开发者：剑大（网名“剑jian”，5DPLAY游戏工作室）",
    img: "/image/bleach-vs-naruto.png",
    detail: "作为狂热动漫爱好者，网名为“剑jian”（又称“剑大”）的中国开发者，受《拳皇97》启发，结合《死神》与《火影忍者》的“战力撕逼”话题，创作了同人格斗游戏《死神 VS 火影》。\n\n2006 年左右，剑大以 jjgame.cn 域名成立剑原创游戏世界，2007 年组建 5DPLAY 游戏工作室。\n\n游戏高度还原原作角色技能和特色，成为无数动漫迷的最爱，但 4399 未经授权直接将其“整合”到平台，未给开发者任何回报，还让大量玩家误以为这是 4399 的官方游戏，稀释了原创者的品牌价值。"
  },
  {
    year: "2010",
    type: "game",
    title: "《赛尔号》：淘米的太空冒险传奇",
    content: "上海淘米网络开发的太空冒险收集养成游戏，4399联运后模糊开发者身份，成为儿童游戏爆款。",
    dev: "真正开发者：上海淘米网络科技有限公司",
    img: "/image/seer.png",
    detail: "2010 年左右，上海淘米网络科技有限公司开发的《赛尔号》上线 4399，这款游戏融合太空冒险、精灵收集和养成元素，凭借丰富剧情和策略性玩法，成为淘米继《摩尔庄园》后的又一儿童游戏爆款。\n\n4399 通过与淘米的合作获得运营权，借助平台庞大流量让游戏进一步走红，但在推广中常将其包装为自身重点产品，刻意淡化淘米的开发身份，导致许多小玩家及家长误以为这是 4399 开发的游戏。"
  },
  {
    year: "2010",
    type: "game",
    title: "《闪翼双星》：闪翼工作室的双人冒险",
    content: "中国闪翼工作室开发的横版闯关游戏，4399整合后获得海量曝光，却让开发者失去作品控制权。",
    dev: "真正开发者：中国闪翼工作室",
    img: "/image/shany翼-stars.png",
    detail: "2010 年，中国闪翼工作室发布《闪翼双星之逃离实验室》，游戏以蓝色“闪”和红色“翼”两位主角的双人协作闯关为核心，两个角色各有特殊能力，需密切配合才能通关，迅速在各大小游戏网站走红。\n\n除《闪翼双星》外，闪翼工作室还制作了《全方位动漫明星大乱斗》等知名游戏。\n\n作为中国独立开发者，其作品被 4399“整合”后，虽获得更多曝光，但也失去了对作品的定价权和传播控制权，只能被动接受平台规则。"
  },
  {
    year: "2007-2010",
    type: "game",
    title: "《弹弹堂》：第七大道的抛物线竞技",
    content: "深圳第七大道开发的Q版射击竞技游戏，4399负责运营却模糊权责，成为平台热门联运产品。",
    dev: "真正开发者：深圳第七大道科技有限公司（曹凯团队）",
    img: "/image/dandan-tang.png",
    detail: "《弹弹堂》由深圳第七大道科技有限公司创始人曹凯带领团队制作，曹凯是狂热游戏爱好者，自称玩过 2007 年之前 17173 网站上出现的所有网游，凭借对游戏的热爱和理解，仅用三个月就开发出《弹弹堂》初版。\n\n游戏以抛物线射击（需计算角度、力度和风力）、Q 版画风和实时竞技对战为特色，上线后迅速走红。\n\n4399 负责游戏的运营，但在推广中将其作为平台重点产品，刻意模糊开发者和运营商的界限，让玩家误以为这是 4399 主导开发的游戏，忽视了第七大道的创作贡献。"
  },
  {
    year: "1987-2010",
    type: "game",
    title: "《魂斗罗》《合金弹头》：经典街机的盗版移植",
    content: "两款日本经典街机游戏的Flash移植版被4399整合，从不说明真实来源，误导玩家认知。",
    dev: "真正开发者：《魂斗罗》（科乐美KONAMI）、《合金弹头》（原Nazca公司，现属SNK）",
    img: "/image/classic-arcade.png",
    detail: "《魂斗罗》由日本科乐美（KONAMI）公司于 1987 年推出街机版，1988 年移植到 FC 平台，以施瓦辛格和史泰龙为原型设计角色，横版卷轴射击玩法成为一代经典。\n\n《合金弹头》原开发商是日本 IREM 公司部分成员成立的 Nazca 公司，现属 SNK 公司（该公司还出品《拳皇》系列），以横版射击、丰富的载具系统和幽默元素著称。\n\n4399 上的版本通常是这两款经典街机游戏的 Flash 移植版，但 4399 从不说明真实来源，既未获得版权方授权，也未给开发者任何收益，让年轻玩家误以为这是专门为 4399 开发的游戏。"
  },
  {
    year: "2011",
    type: "game",
    title: "《造梦西游》：4399的原创遮羞布",
    content: "4399旗下造梦工作室开发的2D横版过关动作游戏，成为平台少数真正原创作品，常被用来掩盖侵权事实。",
    dev: "真正开发者：4399造梦工作室（4399自研）",
    img: "/image/zaomeng-xiyou.png",
    detail: "2011 年 4 月 20 日，4399 旗下造梦工作室发行首款《造梦西游》，这是一款 2D 横版过关类动作网页游戏。\n\n后续陆续推出系列作品：《造梦西游 2》《造梦西游 3》《造梦西游 4》（2015 年 1 月 27 日）、《造梦西游 OL》（2013 年 3 月 14 日）、《造梦无双》（2020 年 6 月 24 日）。\n\n该系列以中国神话为背景，融合动作闯关与角色养成元素，凭借精彩玩法获得玩家认可，是 4399 为数不多的真正原创作品。\n\n但讽刺的是，4399 常以这款游戏为宣传亮点，大力推广其“自主研发实力”，却对平台上 95% 以上游戏均为侵权搬运的事实避而不谈，用少数原创掩盖大规模侵权的本质。"
  },
  {
    year: "2010-2020",
    type: "true",
    title: "独立开发者的生存困境：维权之路步履维艰",
    content: "举证难、维权贵、处理慢，国内外独立开发者在4399的“整合”模式下，面临多重侵权困境。",
    img: "/image/developers-dilemma.png",
    detail: "在 4399 的“整合”模式下，真正的游戏开发者面临极其艰难的处境：\n\n举证困难，4399 要求投诉者必须出具完整的游戏版权证明才会处理投诉，过程漫长复杂，许多个人开发者难以满足；\n\n跨国维权成本高昂，国外开发者到中国维权需支付巨额律师费和诉讼费，多数小团队或个人无力承担；\n\n投诉处理缓慢，即使提供证明，4399 的处理也非常拖沓，《闪客快打》的作者花了很长时间、耗费大量精力，才让 4399 下架了前两部作品；\n\n部分开发者为让游戏以“正版”形式出现在 4399 上，被迫接受平台条件，制作专门的“4399 版”来替换盗版版本，丧失作品主导权。"
  },
  {
    year: "2010-2020",
    type: "true",
    title: "中国开发者：维权胜诉与舆论反噬的双重考验",
    content: "《闪客快打》作者胜诉却近乎拖垮自身，《英雄大作战》作者呼吁正版反遭玩家攻击。",
    img: "/image/chinese-developers-rights.png",
    detail: "中国独立开发者虽无语言和文化障碍，但维权之路同样艰难。\n\n《闪客快打》作者陈聘的经历最具代表性，他与 4399 打了多年版权官司，这场持久战几乎拖垮了他，最终胜诉后，4399 仅花钱买下《闪客快打 3》《闪客快打 4》的版权，其余作品全部下架。\n\n更令人无奈的是，《英雄大作战》的作者呼吁玩家支持正版，却反被玩家问候家人，这反映出残酷现实：许多玩家已习惯在 4399 上免费玩游戏，当被告知需要支持正版时，反而攻击原创作者，形成对原创者的二次伤害。"
  },
  {
    year: "2004-2020",
    type: "true",
    title: "国外开发者：信息差与跨文化下的无奈妥协",
    content: "信息不对称、语言文化障碍、法律体系差异，让国外开发者的维权诉求石沉大海。",
    img: "/image/foreign-developers-helpless.png",
    detail: "国外开发者面临的情况更加糟糕：\n\n信息不对称，很多国外开发者根本不知道自己的游戏已被搬运到中国互联网，作品在 4399 上获得巨大流量，而他们本人却一无所知；\n\n文化和语言障碍，即使知晓侵权行为，沟通也极其困难，很多投诉邮件石沉大海；\n\n法律体系差异，不同国家的版权法律体系存在区别，给维权带来额外复杂性。\n\n英国 Nitrome、美国 PopCap Games、西班牙 Jurana 等开发者，均因这些壁垒无法有效维权，只能眼睁睁看着创意成果被无偿掠夺。"
  },
  {
    year: "2004-2020",
    type: "true",
    title: "被破坏的游戏生态：原创与产业的双重倒退",
    content: "4399的侵权模式扼杀原创动力、扭曲玩家认知，导致中国游戏产业长期抄袭成风。",
    img: "/image/game-ecology-damage.png",
    detail: "4399 的“整合”模式对整个游戏生态造成深远负面影响：\n\n原创动力被扼杀，开发者发现作品被随意抄袭却无法维权时，创作热情会严重受挫，导致中国独立游戏开发长期处于低水平徘徊；\n\n玩家认知被扭曲，长期在 4399 上免费玩游戏的玩家，形成“游戏就应该免费”的观念，很难接受为正版游戏付费，这种观念至今仍影响中国游戏市场；\n\n产业发展畸形，4399 的成功让许多人看到“整合”的“商机”，导致中国游戏产业长期缺乏创新，抄袭成风；\n\n国际形象受损，4399 的行为严重损害中国游戏产业的国际形象，让国外开发者对中国市场望而却步。"
  }
];

// 点击卡片处理函数
const handleCardClick = (item) => {
  selectedItem.value = item;
  // 用于触发动画的状态切换
  showDetail.value = false;
  // 等待DOM更新后显示详情，触发过渡动画
  nextTick(() => {
    showDetail.value = true;
  });
};

// --- 计算属性：筛选逻辑 ---
const filteredData = computed(() => {
  if (currentFilter.value === 'all') {
    return timelineData;
  }
  return timelineData.filter(item => item.type === currentFilter.value);
});

// --- 方法 ---
const setFilter = (type) => {
  currentFilter.value = type;
  // 切换筛选后刷新动画
  nextTick(() => {
    AOS.refresh();
  });
};

const openLightbox = (src) => {
  currentImage.value = src;
  showLightbox.value = true;
};

const closeLightbox = () => {
  showLightbox.value = false;
};

// --- 生命周期 ---
onMounted(() => {
  AOS.init({
    duration: 800,
    once: false, // 允许滚动回看时重复触发动画
  });
});
</script>

<template>
  <div class="main-content">
    <div class="app-container">
      <header class="hero">
        <div class="hero-content" data-aos="fade-down">
          <h1>4399 编年史</h1>
          <p class="subtitle">盗版发家 · 童年印记 · 兴衰参半</p>
          
          <div class="filter-box">
            <button 
              v-for="btn in [
                { label: '发展历程', value: 'milestone' },
                { label: '技术革命', value: 'flash' },
                { label: '经典游戏', value: 'game' },
                { label: '遗忘英雄', value: 'true' }
              ]"
              :key="btn.value"
              class="filter-btn"
              :class="{ active: currentFilter === btn.value }"
              @click="setFilter(btn.value)"
            >
              {{ btn.label }}
            </button>
          </div>
        </div>
        <div class="bg-grid"></div>
      </header>

      
        <!-- 时间轴容器 -->
        <main class="timeline-container">
          <div 
            v-for="(item, index) in filteredData" 
            :key="index"
            class="card-wrapper"
            :class="index % 2 === 0 ? 'left' : 'right'"
            data-aos="fade-up"
          >
            <div class="dot"></div>
            <div class="card" :class="`type-${item.type}`"@click="handleCardClick(item)">
              <div 
                v-if="item.img" 
                class="card-img-container" 
                @click="openLightbox(item.img)"
              >
                <img :src="item.img" class="card-img" :alt="item.title">
              </div>
              
              <div class="card-body">
                <span class="year-tag">{{ item.year }}</span>
                <h3>{{ item.title }}</h3>
                <p>{{ item.content }}</p>
                <div v-if="item.dev" class="dev-box">
                  🔍 {{ item.dev }}
                </div>
              </div>
            </div>
          </div>
        </main>
              
      

      <div v-if="showLightbox" class="lightbox" @click="closeLightbox">
        <img :src="currentImage" alt="大图预览" @click.stop>
      </div>

      <footer>
        <p>致敬真正的游戏创作者 | <span style="color:#ff6a00">4399</span> 深度调研报告</p>
      </footer>
    </div>
    <!-- 右侧详情区域 -->
        <aside class="detail-panel" :class="{ 'show': showDetail }">
          <transition name="detail-change">
            <div v-if="selectedItem" class="detail-content" :key="selectedItem.year">
              <div class="detail-header">
                <span class="year-tag">{{ selectedItem.year }}</span>
                <h2>{{ selectedItem.title }}</h2>
              </div>
              
              <div class="detail-img">
                <img :src="selectedItem.img" :alt="selectedItem.title">
              </div>
              
              <div class="detail-body">
                <p>{{ selectedItem.detail }}</p>
                <div v-if="selectedItem.dev" class="dev-box">
                  🔍 {{ selectedItem.dev }}
                </div>
              </div>
              
              <button class="close-btn" @click="showDetail = false">×</button>
            </div>
          </transition>

          <!-- <div v-else class="empty-state">
            <p>点击左侧卡片查看详细信息</p>
          </div> -->
        </aside>
  </div>
</template>

<style>
/* 全局重置 */
body {
  margin: 0;
  padding: 0;
  font-family: 'Noto Sans SC', sans-serif;
  background-color: #eef2f5;
  background-image: radial-gradient(#dce1e6 1px, transparent 1px);
  background-size: 20px 20px;
  color: #333;
}
</style>

<style scoped>
/* 主内容区布局 */
.main-content {
  display: flex;
  width: 97vw;
  margin: 0 auto;
}
/* 变量定义 */
.app-container {
  --primary: #ff6a00;
  --red: #e74c3c;
  --blue: #3498db;
  --yellow: #f1c40f;
  --card-bg: rgba(255, 255, 255, 0.95);
  width: 50%;
}

/* 头部样式 */
.hero {
  width: 100%;
  background: linear-gradient(135deg, #2d3436 0%, #000000 100%);
  color: white;
  padding: 80px 20px;
  text-align: center;
  position: relative;
  overflow: hidden;
  box-shadow: 0 4px 20px rgba(0,0,0,0.3);
}

.hero h1 {
  font-size: 3.5rem;
  margin: 0;
  letter-spacing: 2px;
  text-shadow: 0 0 10px rgba(255, 106, 0, 0.5);
}

.subtitle {
  font-size: 1.2rem;
  opacity: 0.8;
  margin-top: 10px;
}

/* 筛选按钮 */
.filter-box {
  margin-top: 30px;
  display: flex;
  justify-content: center;
  gap: 15px;
  flex-wrap: wrap;
}

.filter-btn {
  background: rgba(255,255,255,0.1);
  border: 1px solid rgba(255,255,255,0.2);
  color: white;
  padding: 8px 24px;
  border-radius: 50px;
  cursor: pointer;
  backdrop-filter: blur(5px);
  transition: 0.3s;
}

.filter-btn:hover, .filter-btn.active {
  background: var(--primary);
  border-color: var(--primary);
  transform: translateY(-2px);
}

/* 时间轴容器 */
.timeline-container { 
  max-width: none;
  margin: 50px auto;
  position: relative;
}

.timeline-container::after {
  content: '';
  position: absolute;
  width: 6px;
  background: #e0e0e0;
  top: 0; bottom: 0; left: 50%;
  margin-left: -3px;
  border-radius: 3px;
}

/* 卡片布局 */
.card-wrapper {
  width: 49.5%;
  padding: 10px 50px;
  position: relative;
  box-sizing: border-box;
}

.left { left: 0; text-align: right; }
.right { left: 50.5%; text-align: left; }

/* 详情面板样式 */
.detail-panel {
  width: 48%;
  padding: 50px;
  background-color: var(--card-bg);
  border-left: 1px solid #eee;
  box-shadow: -5px 0 25px rgba(0,0,0,0.05);
  overflow-y: auto;
  min-height: calc(100vh - 220px); /* 减去头部和底部高度 */
  position: fixed;
  right: 0;
  top: 0;
  height: 100vh; /* 占满整个视口高度 */
  opacity: 0;
  transform: translateX(20px);
  transition: all 0.5s cubic-bezier(0.4, 0, 0.2, 1);
  pointer-events: none; /* 隐藏时不响应事件 */
  margin: 0 1vw;
}

/* 显示详情面板的动画状态 */
.detail-panel.show {
  opacity: 1;
  transform: translateX(0);
  pointer-events: auto;
}

/* 详情内容样式 */
.detail-content {
  max-width: 800px;
  margin: 0 auto;
}

.detail-header .year-tag {
  font-size: 1rem;
  padding: 6px 16px;
}

.detail-header h2 {
  font-size: 2rem;
  margin: 15px 0 30px;
  color: #2c3e50;
}

.detail-img {
  width: 100%;
  border-radius: 12px;
  overflow: hidden;
  margin-bottom: 30px;
  box-shadow: 0 5px 15px rgba(0,0,0,0.1);
}

.detail-img img {
  width: 100%;
  height: auto;
  object-fit: cover;
  transition: transform 0.3s ease;
}

.detail-img img:hover {
  transform: scale(1.02);
}

.detail-body p {
  font-size: 1.1rem;
  line-height: 1.8;
  color: #444;
  margin-bottom: 25px;
  white-space: pre-line;
}

.detail-body .dev-box {
  margin-top: 30px;
  padding: 15px;
  font-size: 1rem;
}

/* 关闭按钮 */
.close-btn {
  position: fixed;
  top: 20px;
  right: 20px;
  width: 40px;
  height: 40px;
  border-radius: 50%;
  background: rgba(0,0,0,0.7);
  color: white;
  border: none;
  font-size: 1.5rem;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.3s ease;
  z-index: 100;
}

.close-btn:hover {
  background: var(--primary);
  transform: rotate(90deg);
}

/* 空状态样式 */
.empty-state {
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  color: #999;
  font-size: 1.2rem;
  text-align: center;
  padding: 20px;
}



/* 响应式适配 */
@media (max-width: 1024px) {
  .main-content {
    flex-direction: column;
  }
  
  .timeline-container, .detail-panel {
    width: 100%;
  }
  
  .detail-panel {
    min-height: auto;
    border-left: none;
    border-top: 1px solid #eee;
  }
  
  .detail-header h2 {
    font-size: 1.5rem;
  }
  .timeline-container::after { left: 30px; }
  .card-wrapper { width: 100%; padding-left: 70px; padding-right: 20px; text-align: left; }
  .card-wrapper.left, .card-wrapper.right { left: 0; }
  .left .dot, .right .dot { left: 13px; right: auto; }
}

/* 卡片样式 */
.card {
  background: var(--card-bg);
  border-radius: 16px;
  box-shadow: 0 10px 30px rgba(0,0,0,0.08);
  overflow: hidden;
  transition: all 0.3s ease;
  border-top: 5px solid #ccc;
  text-align: left; /* 强制内容左对齐 */
}

.card:hover {
  transform: translateY(-8px);
  box-shadow: 0 15px 40px rgba(0,0,0,0.15);
}

/* 类型颜色条 */
.card.type-game { border-top-color: var(--primary); }
.card.type-controversy { border-top-color: var(--red); }
.card.type-business { border-top-color: var(--blue); }
.card.type-milestone { border-top-color: var(--yellow); }

/* 图片 */
.card-img-container {
  width: 100%;
  height: 180px;
  overflow: hidden;
  cursor: zoom-in;
  background: #f0f0f0;
  border-bottom: 1px solid #eee;
}

.card-img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  transition: 0.5s;
}

.card:hover .card-img { transform: scale(1.1); }

/* 内容区 */
.card-body { padding: 25px; }

.year-tag {
  background: #333;
  color: #fff;
  padding: 4px 12px;
  border-radius: 4px;
  font-weight: bold;
  font-size: 0.85rem;
  display: inline-block;
  margin-bottom: 10px;
}

.card h3 {
  margin: 10px 0;
  font-size: 1.4rem;
  color: #2c3e50;
}

.card p {
  color: #666;
  line-height: 1.6;
  font-size: 0.95rem;
  margin-bottom: 15px;
}

/* 开发者框 */
.dev-box {
  background: #fff8e1;
  border-left: 4px solid #ffc107;
  padding: 12px;
  border-radius: 0 8px 8px 0;
  font-size: 0.9rem;
  color: #8d6e63;
}

/* 圆点 */
.dot {
  width: 24px;
  height: 24px;
  background: white;
  border: 5px solid var(--primary);
  border-radius: 50%;
  position: absolute;
  top: 30px;
  z-index: 2;
  box-shadow: 0 0 0 4px rgba(255, 106, 0, 0.2);
}

.left .dot { right: -17px; }
.right .dot { left: -17px; }

/* 灯箱 */
.lightbox {
  position: fixed;
  top: 0; left: 0;
  width: 100%; height: 100%;
  background: rgba(0,0,0,0.85);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
  backdrop-filter: blur(5px);
}

.lightbox img {
  max-width: 90%;
  max-height: 90%;
  border-radius: 8px;
  box-shadow: 0 0 20px rgba(0,0,0,0.5);
  animation: popIn 0.3s ease;
}

@keyframes popIn {
  from { transform: scale(0.8); opacity: 0; }
  to { transform: scale(1); opacity: 1; }
}

footer {
  text-align: center;
  padding: 40px;
  color: #999;
  font-size: 0.9rem;
}
</style>