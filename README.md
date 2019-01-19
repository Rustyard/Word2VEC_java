源项目链接：https://github.com/NLPchina/Word2VEC_java

在源项目中做了如下处理：

1.确保语料文本文件是UTF-8编码，附带了语料corpus.txt，训练模型文件model.bin因太大(120M)没有提交，需要自己本地训练(LearnTest.class)，训练时间大概几十分钟。

2.源作者提供的语料是用制表符切割的词组，但是代码是根据空格切割，需要将制表符全部替换成空格。或者修改代码：Learn.java 271行，修改成String[] split = temp.split("[\s　]+");支持同时出现多个半角或全角空格，或制表符分隔。

3.发现一个bug
Word2Vec中2个distance方法中，min = result.last().score; 应该放在resultSize < result.size()块里。
只有当结果数已经大于resultSize，才能将最后一个得分数赋予min，作为以后最小允许得分。结果数不大于resultSize不能赋予给min。

运行Word2VecTest.class，距离最近词，计算词距离，聚类等：

    public static void main(String[] args) throws IOException {
        Word2vec vec = new Word2vec();
        vec.loadJavaModel("model.bin");
        // 距离最近的词
        System.out.println(vec.distance("邓小平"));
        System.out.println(vec.distance("魔术队"));
        System.out.println(vec.distance("过年"));
        System.out.println(vec.distance(Arrays.asList("香港", "澳门")));
        // // 计算词之间的距离
        HashMap<String, float[]> map = vec.getWordMap();
        float[] center1 = map.get("春节");
        float[] center2 = map.get("过年");
        double dics = 0;
        for (int i = 0; i < center1.length; i++) {
            dics += center1[i] * center2[i];
        }
        System.out.println(dics);
        // 距离计算
        System.out.println(vec.analogy("毛泽东", "邓小平", "毛泽东思想"));
        System.out.println(vec.analogy("女人", "男人", "女王"));
        System.out.println(vec.analogy("北京", "中国", "巴黎"));
        // 聚类
        WordKmeans wordKmeans = new WordKmeans(vec.getWordMap(), 50, 50);
        Classes[] explain = wordKmeans.explain();
        for (int i = 0; i < explain.length; i++) {
            System.out.println("--------" + i + "---------");
            System.out.println(explain[i].getTop(10));
        }
    }
结果：
[毛泽东	0.7644433, 小平	0.6703129, 彭真	0.66716033, 项英	0.6612427, 王震	0.61630136, 精辟	0.6138411, 遗影	0.6100997, 罗炳辉	0.60993606, 无产阶级	0.60723233, 原话	0.60619086, 遗志	0.60150117, 诚如	0.5884211, 曾克	0.5845276, 所说	0.58375937, 与世长辞	0.5836175, 张宝忠	0.58026356, 节选	0.57623154, 南巡	0.5756824, 典藏本	0.5754866, 康克清	0.5742924, 论述	0.5675422, 马克思主义者	0.56722516, 半身像	0.5662199, 草明	0.5655095, 一以贯之	0.56540805, 引述	0.56431895, 以其昏昏	0.5598442, 陈云	0.5578752, 世宗	0.5578731, 任定成	0.5557121, 罗竹风	0.55448216, 忌辰	0.55138385, 帮助者	0.54890114, 力举	0.5488076, 革命家	0.5476286, 列宁	0.54710037, 老一辈	0.5446466, 王平	0.53976685, 何振光	0.539401, 护身	0.5385265]
[奥兰多	0.8990011, 新泽西	0.83124423, 奇才队	0.82303494, 网队	0.6876496, 顾明	0.68449014, 喻广生	0.6766388, 大年初五	0.67316043, 实习生	0.67124707, 佛罗里达州	0.6711269, 刘国强	0.66510504, 利纳雷斯	0.6648634, 郑金发	0.66467416, 寒风料峭	0.6624502, 孙星文	0.6613987, 廿七	0.66014194, 谷利源	0.65965, 孙永明	0.6595951, 辛祥利	0.6593127, 蓝宝石	0.6587766, 秦凤桐	0.6582865, 乔颖	0.656125, 潘家埠	0.6528409, 安卡拉	0.6501446, 刘文国	0.6500238, 马那瓜	0.6496207, 盛世良	0.64810187, 年初四	0.64746207, 时装展	0.64492613, 孟军	0.6446666, 俞俭	0.64461464, 谢湘	0.64448756, 刘世昕	0.6439994, 摩洛哥王国	0.6434583, 科托努	0.64319485, 周健伟	0.6416397, 王波	0.6413159, 阿鲁沙省	0.64103186, 刘永华	0.64082164, 侯嘉	0.64043736, 里斯本	0.6402035]
[在家	0.7423898, 舍不得	0.7376384, 俺	0.7369837, 摆手	0.73432696, 夜餐	0.7341076, 哩	0.7335882, 面条	0.72474533, 雷打不动	0.724103, 沾	0.7240173, 婆婆	0.7221275, 住店	0.7218268, 渴	0.7201695, 高谈阔论	0.7197889, 点儿	0.718748, 蓉蓉	0.71823263, 咸肉	0.71812654, 俺们	0.71790886, 闲不住	0.71683896, 脸面	0.7157444, 白吃	0.7155953, 忙乎	0.715091, 嘴	0.71458715, 来不及	0.7142201, 啥	0.71339524, 过节	0.7130332, 狼吞虎咽	0.71150655, 粥	0.71060145, 爸	0.7104714, 干脆	0.7101893, 娶	0.70979977, 团圆饭	0.70957047, 活儿	0.7085973, 觉着	0.70842355, 挺	0.70738465, 小孩子	0.7071321, 吵架	0.706652, 小时候	0.70644444, 咱	0.70609075, 饱	0.70579404, 晚饭	0.70564157]
[特区	1.2971655, 行政区	1.264345, 基本法	1.1783973, 回归	1.1758714, 捐立	1.1747471, 驻军法	1.1560982, 朱育诚	1.1336008, 集邮展	1.1269801, 宪制	1.1150243, 律政	1.0977309, 协进会	1.0964391, 邮票展	1.0918916, 分社	1.0739815, 荣任	1.0732228, 筹委会	1.0543712, 九九	1.0494627, 碑名	1.0468458, 董建华	1.0403379, 工联	1.0324636, 长官	1.029808, 筹组	1.0183156, 董赵	1.0176637, 裁判官	1.0166798, 崔德祺	1.015684, 萧蔚云	1.0122391, 洪娉	1.0112673, 内地	1.011199, 立法会	0.9969274, 唇齿相依	0.99669534, 事务处	0.9966082, 筹备	0.99481606, 社长	0.99303985, 新建业	0.9918362, 黄帝庙	0.9812778, 姜恩柱	0.97931397, 草委	0.9770124, 工联会	0.97540206, 界	0.97367096, 崇正	0.9725961, 植树日	0.96933734]
0.23460540093913096
[马列主义	0.8983283, 马克思列宁主义	0.80830836, 邓小平理论	0.8022357, 精髓	0.7642797, 领会	0.7109853, 一脉相承	0.70532393, 学好	0.70324695, 毫不动摇	0.6758803, 兴起	0.6730429, 实事求是	0.64967054, 马克思主义	0.64533585, 劝勉	0.6447277, 摆样子	0.639843, 全党	0.6367661, 解放思想	0.6330355, 高潮	0.62906283, 党章	0.6267784, 旗帜	0.6228768, 对不对头	0.61919075, 一以贯之	0.61350095, 试金石	0.60531145, 要义	0.6040544, 坚定性	0.60129267, 学风	0.5938531, 重头之作	0.5924376, 真理性	0.5920566, 列宁主义	0.59156394, 辩证唯物主义	0.5898622, 始终不渝	0.5896301, 头脑	0.5870725, 历史唯物主义	0.58465284, 矛盾论	0.5836085, 唯物辩证法	0.58345115, 宣言书	0.5830879, 群众观	0.58115387, 护身	0.58019125, 宗教观	0.5771083, 战略家	0.5758867, 典鉴	0.57367206, 学习	0.57343537]
[拉斯穆森	0.9037338, 王储	0.89275384, 玛格丽特二世	0.8835953, 大公储	0.87710315, 扎尔姆	0.86597306, 耶尔维德	0.8619052, 施鲍茨	0.86078596, 伊马纳利耶夫	0.85932493, 杰姆	0.85332483, 德阿纳	0.8514118, 容克	0.8504743, 参议长	0.8484147, 德里克	0.84774405, 科克	0.84217864, 马图特斯	0.842163, 川·立派	0.84141976, 韦德里纳	0.84123415, 潘文凯	0.84117925, 巴雷罗	0.8380281, 罗瓦伊纳	0.83330077, 乌叶海亚	0.82908493, 巴达维	0.82776874, 午宴	0.82671976, 韦尔斯	0.8262708, 瓦西里	0.825916, 大公府	0.82539487, 邦德维克	0.82196635, 亨利	0.8197816, 鲁克曼	0.8196084, 阿斯纳尔	0.81918144, 安德鲁斯	0.81616086, 卢森堡	0.8151567, 竹下登	0.81439745, 柯西金街	0.8129252, 唐·麦金农	0.81209975, 李光耀	0.8111263, 因苏尔萨	0.8104, 西里婉瓦妮·玛希顿	0.809832, 阿卜杜拉	0.8095142, 库尔特·比登科普夫	0.80875474]
[法国	0.65258473, 芬兰	0.6525314, 意大利	0.6005615, 华裔	0.6003168, 西班牙	0.60003865, 勋爵	0.59998786, 奥地利	0.59926444, 南非	0.5947455, 言论	0.59242827, 小国	0.59205997, 1853年	0.5858547, 共和国	0.5831874, 汪道涵	0.5821192, 皇家	0.58030707, 顺风吹火	0.5799535, 和裁会	0.5798783, 难度表	0.5785191, 解送	0.57811564, 一超	0.5723516, 澳大利亚	0.56916726, 罗马尼亚	0.56913006, 摩	0.5686452, 巴嘎班迪	0.56781316, 俄国	0.56774235, 白俄罗斯	0.5665278, 达特茅斯	0.5648339, 欧洲	0.5648077, 阁下	0.56396437, 解阵党	0.5619801, 希腊	0.5610834, 经社	0.5609453, 加拿大	0.5599205, 约翰松	0.5587142, 歌剧院	0.5558573, 简·霍金斯	0.55207694, 生命科学会	0.55095696, 波兰	0.5500565, 俄罗斯	0.54944843, 萨维	0.5484437, 马耳他	0.54743433]

[秋千=0.2601873044657985, 茉莉花=0.268988150194561, 水龙吟=0.2721230309117786, 张仃=0.2834801185221423, 王文娟=0.28464754799242087, 傣家=0.2854919333828694, 朱自清=0.2883923026078534, 琵琶=0.2892423785909166, 范瑞娟=0.2906152875910273, 毕加索=0.2919789526862848]
--------1---------
[白吃=0.23737928865421054, 落泪=0.24289685186285226, 摇头=0.24490210069204132, 忍不住=0.2468656393995392, 点儿=0.24714962795596573, 台词=0.24757936799953306, 哼=0.24775027603141098, 小时候=0.25603812452118113, 鬼=0.25658183169405824, 歇=0.2567685426941182]
--------2---------
[密不可分=0.46866972899292136, 产业性=0.4755216185581901, 推动力=0.47996565782741407, 后发优势=0.4868793235730209, 必由之路=0.5036651223127251, 必要条件=0.5040777213309253, 基本矛盾=0.5071746489819589, 发展观=0.5089693867500564, 形式化=0.5099916953882135, 能动=0.5146665036327969]
--------3---------
[维德=0.2658624295319356, 海星=0.2720511516148747, 招商局=0.2784621762569095, 丰喜=0.2787145119222103, 华能=0.29179254424904344, 同联=0.2926590565236715, 先科=0.29300156934233357, 肥业=0.2988339986613793, 海门=0.2994841136054808, 稻香村=0.3024258574215821]
--------4---------
[0．32=0.11115048370534164, 2．15=0.12824740312505156, 10．23=0.13084218923553692, 0．21=0.13662921957038066, 0．38=0.13746151036589538, 0．31=0.14018535684002487, 1272·35=0.14289167623384458, 0．22=0.14316327373321158, 2．54=0.1438753455787171, 3742·50=0.14392622978230918]
--------5---------
[各组=0.3692260547141979, 第23=0.3700027918265647, 第二十三=0.3937956804630858, 台湾省=0.39580816745064973, 周绍铮=0.3974474834848536, 召集人=0.40044359649909467, 赫苏斯·皮雷斯=0.40054082327687013, 秘书处=0.4099080859241513, 2196=0.4261758748906743, 台港澳侨=0.428123364497607]
--------6---------
[16．2亿=0.1997375415007383, 10．1亿=0.20054853125435557, 1．3亿=0.20375457265879682, 1．6亿=0.22036479292933908, 69亿=0.2232672812859846, 1·5亿=0.2240588266175081, 1．2亿=0.22975353318842995, 3．4亿=0.2317203174130329, 53亿=0.23181621627734472, 1．8亿=0.23328103982278492]
--------7---------
[nm=0.16341152476385723, 苟且偷安=0.16346317575789726, 幻想国=0.1683902354475144, 爱丽丝=0.17366420098030488, 17．4万=0.17635987589611668, 历险地=0.18389111534708347, 灰姑娘=0.18629726659909496, 滥杀=0.18954052467030655, 汪塘=0.19085753226287827, 票台=0.19117537036393628]
--------8---------
[1797年=0.9858405663810395, 盘行=0.987263169702203, 木栅=0.9877468409092138, 放长线钓大鱼=0.9878496515076183, 工程咨询界=0.9879872702056645, 此=0.9885720169817827, 这个=0.9887322389804467, 半梦半醒=0.9888815472726975, 某省=0.988958828817033, 决胜局=0.9890090683169888]
--------9---------
[冰棍=0.3467378097743179, 烂=0.3549429217941319, 衣=0.35756606768862653, 保管费=0.36168602179236586, 铝排=0.36633740859848274, 铜排=0.3687597361209667, 狗=0.36974863547717973, 撬=0.3757661854216394, 密封=0.3804050764363751, 火车票=0.38166570792773413]
--------10---------
[督促=0.43384474044258603, 奖惩=0.4340108684155197, 通力=0.46828595787960936, 农轩=0.4893875375617398, 专卖=0.4916846992886512, 齐抓共管=0.49558186252589564, 追究制=0.496350673087278, 规章=0.49852921305065934, 公检法=0.4991330427540861, 守则=0.5027652124510027]
--------11---------
[意蕴=0.3417870252518611, 现实性=0.3452767185207666, 合理性=0.3453502269835198, 感召力=0.3593844380884139, 旺盛期=0.36685069587713315, 主潮=0.36853413320788064, 本性=0.3710703317832794, 张扬=0.3727370021901959, 人性=0.3743039758351614, 丰富性=0.3762024550796035]
--------12---------
[50亿=0.19676955531326895, 180亿=0.1976527003245785, 270亿=0.20132464383586196, 170亿=0.20418745744635203, 1300亿=0.20755986710209662, 1400亿=0.20908399426220003, 一千三百九十九亿=0.21394602193312906, 150亿=0.21641850665270185, 1万亿=0.2195723750729075, 200亿=0.22228895998857467]
--------13---------
[刘玉勋=0.09839706438889095, 萧关根=0.09983017131288818, 刘裕国=0.10314681367634648, 张建玲=0.10477687540911607, 索研=0.10763596895547778, 郑盛丰=0.1080597481757195, 吕志星=0.10818761411411026, 张铁柱=0.10887107841466559, 罗茂城=0.11067154267765389, 韩振军=0.11198132435251296]
--------14---------
[核定=0.34533945628753315, 主机=0.3639204178233549, 转账=0.36655678080944143, 全额=0.36874929148224567, 营业税=0.3695443326998884, 承兑=0.3769182530502624, 自营=0.3782900091384853, 返还=0.38055030119798516, 保管=0.3895058955093873, 分红=0.3895116555106597]
--------15---------
[上证B股=0.19660842519486865, 深证B股=0.20182879530304376, 罗沛霖=0.20870829367131805, 张维=0.22004329416408197, 严恺=0.22338020608619757, 宋树友=0.22900706582265684, 黎振强=0.23024424471992688, 邵象华=0.2318590828122069, 何美华=0.23198818290398115, 2·12=0.23220945770979995]
--------16---------
[徐向前=0.3443698336487174, 彭德怀=0.3677627206291678, 花篮=0.37480863432430156, 贺龙=0.38228394202404825, 敬献=0.38810872083040326, 粟裕=0.3900049243653131, 祖居=0.3915204251256732, 公墓=0.39457728825551897, 南方局=0.39520292681990377, 刘伯承=0.39575710738211134]
--------17---------
[曲艺=0.3052472854234498, 从军记=0.3442744883393134, 展演=0.3446005079101129, 作品集=0.34617778591286985, 越剧=0.3467101114157032, 张乐平=0.34685142380073075, 器乐=0.34823960332249015, 文丛=0.3575158319699434, 诗集=0.3641721397417257, 魔术=0.36431963289373925]
--------18---------
[一板一眼=0.3061378006457147, 白玉=0.324520685443602, 簋=0.324596351224411, 东街=0.330190940979719, 载货=0.332225722735803, 钢板=0.3334544886664723, 化粪池=0.33367241282541826, 泵站=0.33418351694389836, 香粳=0.33564776302847577, 野鸭=0.3357869826334099]
--------19---------
[豪爽=0.2682263290984708, 悦耳=0.2697623338561046, 怒吼=0.2756513070434936, 绚丽=0.27649521180113357, 诗句=0.2768721223786734, 挑逗=0.27796235991828766, 怀旧=0.27837751473582273, 动听=0.2785903604357127, 音符=0.28071548637007027, 星空=0.2823079870481511]
--------20---------
[在意=0.3982092147244876, 要紧=0.40187962451956594, 赶时髦=0.43382130198375535, 虚晃一枪=0.44015856637036066, 坏事=0.4403429810595014, 终究=0.4403766332336174, 放过=0.4438912703370619, 未必=0.4440687363035778, 偷懒=0.45182215498920986, 千篇一律=0.4531789115656668]
--------21---------
[黄运成=0.2562555058690652, 艺途=0.29356549074312, 张贵剑=0.2954791623259787, 王建华=0.30302230674832686, 霍巴特=0.30514609692198746, 10．63=0.3134678679623666, 施氏鲟=0.31672633508912895, 鳇=0.31712024942835804, 阿朵=0.3253555272321762, 顾伟=0.325565932080937]
--------22---------
[猴=0.4669268204657856, 脱落=0.48013999934744334, 变异=0.4897893588520006, 无助=0.49151112470479275, 羊毫=0.49867500192819136, 伤病=0.502586951133031, 清晰可见=0.5099771459394251, 大脑=0.5129132065093902, 恕=0.5138502534872771, 坍塌=0.5146035188441914]
--------23---------
[待岗=0.43560158736393895, 26000=0.44990190702635857, 特困=0.45946624058160523, 3557=0.46202979239989556, 待业=0.46881985912882895, 在岗=0.4689017456175293, 8．25万=0.46941241861562233, 2．3万=0.46989074202968184, 7985=0.4759862357574357, 17582=0.4785053250362723]
--------24---------
[非价格=0.4250249261889385, 稀缺=0.4265865119014567, 畸形=0.43392882791779996, 辐射力=0.4423530085203085, 流入量=0.4447847926486246, 周期性=0.44625218588261917, 供大于求=0.4465856945265798, 抵消=0.4494929240873091, 脆弱性=0.4505453440531295, 危机四伏=0.45663442772791996]
--------25---------
[欧安组织=0.40976171574125075, 盟国=0.42046080924215445, 各党=0.4238724051474494, 中立=0.4324280619782712, 要员=0.4333703679470471, 施压=0.4357009175916744, 核政策=0.43928177271290014, 反对派=0.4395915937194843, 阿塞拜疆=0.4428280984286399, 现政府=0.44584934917429564]
--------26---------
[主治医师=0.25850324798128455, 巴中南江县=0.25968620951010735, 海北=0.26215457916374874, 中医科=0.26363083806179866, 王可胜=0.2652380977789264, 黄再军=0.27293432980008037, 黄风湿=0.27421692536461073, 于振海=0.2788985888240454, 主任医师=0.28010875993495965, 王金春=0.2860634555309929]
--------27---------
[胡光宝=0.22733370124753094, 方家区=0.23030502543980091, 孙英=0.23688387592653282, 张廷翰=0.23726513201910504, 李志坚=0.23914347160558913, 张丁华=0.24040476372957897, 许永跃=0.24471019258130866, 袁守芳=0.2457787381651675, 刘忠德=0.24659959441844137, 秦玉琴=0.2484859627058058]
--------28---------
[开发型=0.40068547691032563, 机具=0.410625633278471, 节能=0.4237510986814854, 工厂化=0.42508508630974795, 畜禽=0.44014537288583355, 微电子=0.4465932425458544, 组装=0.44875359556232386, 规模化=0.4515489656439087, 通信网=0.4521378499025559, 修订版=0.45265925783288274]
--------29---------
[扣押=0.31088260354985187, 羁押=0.31289732482762744, 取保=0.3472247666371331, 候审=0.347933495433157, 辩护人=0.3493520335344904, 被害人=0.35268103609249124, 改判=0.35318604100449136, 案卷=0.3559828193071377, 保外就医=0.35803281191895175, 被告人=0.3597591868791876]
--------30---------
[讲师=0.3008984144109959, 系主任=0.31648526085255213, 副教授=0.3334515143604797, 研究生院=0.3405272463540996, 物理系=0.3463549683308642, 数学系=0.34961970833794, 地理系=0.35180556204370195, 清水茂=0.3558982194846294, 东方学系=0.3584832925036423, 暨南=0.3593549491661694]
--------31---------
[到访=0.21424742385836026, 李家忠=0.21761784696641984, 伊马纳利耶夫=0.2224429429578585, 爵士=0.2297004289771003, 伽马=0.23036552734430416, 潘文凯=0.236376961260504, 于武真=0.23776939153367493, 舍拉利·海鲁拉耶夫=0.2401759947242289, 王储=0.2430971714901684, 因苏尔萨=0.2437763321749884]
--------32---------
[阿妈=0.3234093304370731, 大儿子=0.32521679063839404, 杜养富=0.32581599461788, 含泪=0.32990808988902387, 李峙=0.3310923305888842, 洗衣=0.3318464733449602, 大嫂=0.3334222229171342, 大娘=0.33361114389840907, 右臂=0.33733214247665444, 跟前=0.3404176493115685]
--------33---------
[马来西亚队=0.2168648938709481, 古巴队=0.23581440339386006, 季军=0.2385613801470754, 新加坡队=0.2426882082444175, 力克=0.24337015731353517, 庞卫国=0.24623724063077645, 杨影=0.2472080633920566, 阿尔比=0.24862335092028331, 斯托伊科=0.24991583683303986, 荷兰队=0.25097770004737185]
--------34---------
[容器=0.2752366084750957, 化学纤维=0.27785299932574503, PFBC=0.2787464621399067, 挖掘机=0.28324601030544727, 互感器=0.29257840914536803, 13．=0.29730895432882676, 焊装=0.29939475250014347, 秘方=0.30552625136865785, 增压=0.3102735761773925, 特用=0.3149687575850635]
--------35---------
[钱江=0.35696786148378123, 陈飞=0.36870693287426093, 丁仁贵=0.37056077838826695, 任珑=0.379330181137119, 李景录=0.38715075456241266, 周确=0.39230320407285646, 李家杰=0.3931276518748703, 电视报=0.39969322732173396, 丁波=0.4020485433072807, 初中组=0.4040500823880109]
--------36---------
[23523．48=0.21987101058190106, 2316．25=0.23291730202353733, 2631=0.24492280115716053, 22111·26=0.24796002894756652, 15230=0.2484832781274502, 23413=0.2532815946979454, 277483=0.25372758837966103, 87553．57=0.25384207033084927, 8224．92=0.25385507434917187, 3003．94=0.2541820659468925]
--------37---------
[无私奉献=0.4178556842703236, 楷模=0.43657461398868724, 光明磊落=0.44693718799400806, 博大胸怀=0.448220621382454, 奋发进取=0.4483883152129841, 鞭策=0.45247454784756025, 昂扬=0.457055772351036, 无私无畏=0.45995538651277457, 博大精深=0.4621350131616033, 进取=0.4621371778087173]
--------38---------
[足足=0.3992991290552921, 四五=0.4048570125393596, 钟头=0.4190070409900919, 八九=0.4249108529692798, 六七=0.4250185229334953, 颠簸=0.4266944753572043, 三四=0.4281295194486159, 袁志彬=0.4302918841540304, 五六=0.43494591658161097, 盗窃罪=0.4408645950754142]
--------39---------
[少丽=0.4606757464581719, 凝神=0.48770090367041696, 老父亲=0.490267761224203, 孙晓明=0.492397772681727, 敖然=0.4966012464344526, 黄粱梦镇=0.5068282344569042, 加塔耶娃=0.5069012087956537, 咱俩=0.5080868473296043, 知心=0.5105906314307265, 祖父母=0.5107704495180769]
--------40---------
[拉链=0.4198953840799098, 扇=0.42724755312820406, 练就=0.43373799950231273, 桩=0.4350095478250484, 摞=0.4359395843873064, 匹=0.4369687212254121, 桔黄=0.45679745786531, 枝=0.4601365988942234, 串=0.4633083340105628, 洼=0.46415822036599685]
--------41---------
[闪=0.18279563525202136, 青冈林=0.19635628562278296, 清泉=0.2080904182982488, 怒放=0.21624559620255468, 狮子=0.21710596987521313, 雪挂=0.21885424092007, 小鸟=0.22179029824064145, 晶莹=0.22315451958285593, 林子=0.22600696409165266, 斑斑=0.22676941364144554]
--------42---------
[26％=0.2205045734103338, 31％=0.22404991316936673, 17％=0.2274975089940554, 14％=0.23330669009581229, 22％=0.24114364735665106, 2．8％=0.2454527211577755, 9·7％=0.24774044667063322, 15％=0.25041226386085214, 9．7％=0.253777380547195, 1．7％=0.2556812439701499]
--------43---------
[十一月=0.3958168324863891, 元月=0.40038463236720645, 1953年=0.4104669779357124, 上旬=0.4138392104326387, 1976年=0.41670177207317816, 1924年=0.4190529596213537, 1969年=0.4228986795529295, 1942年=0.4342893003854411, 十月=0.43716329772980345, 1965年=0.43781056814657404]
--------44---------
[鄱阳湖=0.3787647025574472, 平原=0.41870403759116925, 本旬=0.4196083631045568, 中北部=0.4315765861411833, 黄淮海=0.4362536168707294, 西北部=0.45396077514216343, 川西=0.45468444057227053, 阴有小雨=0.4562143827590468, 江汉=0.45954248924368457, 前中期=0.4669646734738535]
--------45---------
[32万=0.2390894647756312, 65万=0.25400746269105684, 郝建平=0.2573221441734663, 16万=0.26874191235401046, 张育宾=0.2730476780535298, 36万=0.2767749956364227, 380万=0.276965118672198, 1·2万=0.27789954075224443, 裴双喜=0.2799743451809693, 4．5万=0.28624257264299047]
--------46---------
[穷亲=0.38449939618678197, 扶贫帮困=0.4216584947311586, 巡诊=0.4328485978214758, 文明村=0.4389068468818884, 十星级=0.4412694975833118, 帮带=0.44906454877608615, 厂矿=0.45044206916088747, 文化站=0.4556831230197247, 慰问品=0.4589366982093708, 责任区=0.46354811391067585]
--------47---------
[高文华=0.04791468522259379, 王素银=0.051945778260375164, 谢希德=0.05397994391141708, 李京淑=0.054869164424126815, 冯淬=0.05638785159187165, 沈桂芳=0.05681788009821176, 杨耀忠=0.056951246849621384, 高素华=0.05717668196718151, 罗棣庵=0.057510477939009874, 李鹏飞=0.058776749412739804]
--------48---------
[观测仪=0.6452558477285351, 安民=0.6513319584343849, 玉泰=0.6515626209000835, 席正山=0.6546090306028987, 乳制品厂=0.6566322639701689, 次氯酸钠=0.6576678846638444, 乳白=0.6578869356264647, 盐碱化=0.659510153655666, 类风湿性=0.6619935148950873, 争食=0.66367896406787]
--------49---------
[237=0.35729262567944886, 4．0=0.3578793692168425, 71=0.36389666479411886, 6．67=0.36643154913245723, 69=0.3700212141710928, 104=0.37287714196373223, 大项=0.37654549168207296, 一百四十二=0.3791900172355902, 105=0.3806851737644763, 108=0.3829455765985171]



