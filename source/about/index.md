---
layout: "about"
title: "About"
date: 2020-06-02 10:00:00
description: "个人简介"
header-img: "img/about-bg.jpg"
comments: true
---


>姓名：高明
>生日：1995/02/19
>学历：硕士研究生
>地区：上海
>邮箱：qywtgm950120@foxmail.com
>技能：Java后端开发，Python开发，自然语言处理及数据分析
>博客：https://gaoming95.github.io

- **教育经历**

| 时间                | 学历   | 学校     | 专业             |
| ------------------- | ------ | -------- | ---------------- |
| `2017.09 - 2020.06` | 硕士   | 上海大学 | 计算机应用技术   |
| `2013.09 - 2017.06` | 本科   | 湖北大学 | 计算机科学与技术 |
| `2014.09 - 2017.06` | 双学位 | 武汉大学 | 经济学           |

- **工作经历**

  - `2020/06/02-至今`	**上海哔哩哔哩科技有限公司**	**高级开发工程师** [*全职*]
    
    B站指数，建站工具，效果广告，创意中心
    
  - `2019/06/12-2019/9/12`	**华为科技有限公司**	**人工智能工程师** [*实习*]

    Xbot智能助手，领域知识图谱问答系统

- **项目经历**

  - **B站指数项目**`2021/01/10-至今`

    项目后端负责人，负责项目的框架搭建，产品需求迭代，以及线上问题跟进

    项目背景：B站指数是对标百度指标、头条算数指数的数据平台，盘点B站内容热点、品牌及UP主排名，帮助市场了解B站，反映品牌在行业中的位置和变化趋势，2021/09/10内测，2021/10/20灰度，2021/12/20上线

    网站地址：https://trends.bilibili.com 未公测，且外网只披露部分功能

    项目内容：

    1. 数据生产，同步各渠道数据从Hive表到[ElasticSearch](https://gaoming95.github.io/2020/07/15/ElasticSearch/)集群和[MongoDB](https://gaoming95.github.io/2020/06/08/mongodb/)集群
    2. 用户管理，内网支持RBAC用户权限管理，外网支持Mid权限点管理
    3. 数据检索，支持内容检索、品牌检索、UP主检索、关键字搜索
    4. 数据干预，运营后台支持黑名单词库管理，排行榜管理，品牌管理，UP主管理等
    4. 风控管理，支持实时敏感词生效
    
    项目技术：
    
    1. Springboot，[Web系统框架](https://gaoming95.github.io/2020/06/22/Web%E7%B3%BB%E7%BB%9F%E6%9E%B6%E6%9E%84%E5%88%86%E5%B1%82/)-五层架构
    2. [ElasticSearch](https://gaoming95.github.io/2020/07/15/ElasticSearch/)，[MongoDB](https://gaoming95.github.io/2020/06/08/mongodb/)，[Mysql](https://gaoming95.github.io/2020/06/04/Mysql%E8%AE%BE%E8%AE%A1%E8%A7%84%E8%8C%83/)，[Redis](https://gaoming95.github.io/2020/08/06/Redis/)
    3. [Cat](https://gaoming95.github.io/2021/08/26/Cat/)，XXL-JOB定时任务，Databus（Kafka），[Redisson](https://gaoming95.github.io/2021/08/25/Redisson/)，Durid，[QueryDsl](https://gaoming95.github.io/2020/07/01/QueryDSL/)，Kibana+Logstash+ES
    4. [Spring Http Invoker](https://gaoming95.github.io/2020/09/03/Spring%20HTTP%20Invoker/)，Grpc
    
    项目特点：
    
    1. 面向所有B站用户，高QPS，高吞吐量，保持线上稳定
    2. ES集群+MongoDB，ES集群存储索引数据，硬件资源有限
    3. 平衡ES集群压力和应用服务器计算压力，将部分计算任务放在应用服务器中，降低ES集群压力，调整应用服务器垃圾回收器G1
    4. 索引按月、按年分拆，别名管理，提升查询速率
    5. 权限点支持，支持内网RBAC接口级别权限，支持外网mid接口级别权限
    5. 首页数据分页缓存，延迟双删减少缓存一致间隙

    项目成果：
    
    1. 线上稳定，主页搜索接口QPS达8000，其他接口QPS均超过3000
    2. 运营品牌报告，增加品牌营收（相关提升点不宜披露）

  ****

  - **高能建站项目**`2020/06/02-至今`

    项目后端负责人，负责产品需求迭代，以及线上问题跟进

    项目背景：商业广告自建站能力，支持代理与广告主制作落地页用户广告投放，提升B站广告点击率和转化率

    网站地址：http://cm.bilibili.com/art/bricks/#/site 需广告主登录

    项目内容：

    1. 支持代理和广告主创建、发布、管理H5落地页，原生落地页和小程序落地页
    2. 支持落地页组件级别设计
    3. 支持从模板创建落地页，落地页优选，根据创意元素自动生成落地页
    4. 营销组件管理，支持表单、抽奖等组件
    5. 支持落地页点击、转化信息披露，热门模板以及优秀落地页展示

    项目技术：

    1. Spring，SpringMVC，MyBatis，[Web系统框架](https://gaoming95.github.io/2020/06/22/Web%E7%B3%BB%E7%BB%9F%E6%9E%B6%E6%9E%84%E5%88%86%E5%B1%82/)-五层架构
    2. ClickHouse，[MongoDB](https://gaoming95.github.io/2020/06/08/mongodb/)，[Mysql](https://gaoming95.github.io/2020/06/04/Mysql%E8%AE%BE%E8%AE%A1%E8%A7%84%E8%8C%83/)，[Redis](https://gaoming95.github.io/2020/08/06/Redis/)
    3. Cat，JOOQ，XXL-JOB定时任务，RabbitMQ，[Redisson](https://gaoming95.github.io/2021/08/25/Redisson/)，Druid，Kibana+Logstash+ES，[QueryDsl](https://gaoming95.github.io/2020/07/01/QueryDSL/)
    4. [Spring Http Invoker](https://gaoming95.github.io/2020/09/03/Spring%20HTTP%20Invoker/)，Grpc，线程池

    项目难点：

    1. 在线落地页开放接口，支持8000QPS，保持线上稳定
    2. 落地页配置层级复杂，落地页类型较多，整体逻辑经多次迭代维护成本大

    项目成果：

    1. 在线单日落地页900万请求
    2. 功能丰富，支持组件级别的落地页制作，广告用户使用率80%
    3. 建站落地页覆盖率65%

  ****

  - **效果广告项目**`2021/04/01-至今`

    项目后端参与者，负责产品的部分需求迭代，以及部分线上问题跟进

    项目背景：商业效果广告项目，商业广告投放入口

    网站地址：https://cm.bilibili.com/ad/#/home 需要广告主或代理登录

    项目内容：

    1. 数据展示：展示广告主创意信息，点击、消费、CPC价格等，便于广告主了解创意投放效果，做出投放调整
    2. 推广：计划、单元、创意创建、设置、发布、启动与暂停
    3. 报表内容：多维度内容对比，展示创意投放效果，展点消数据，转化数据
    4. 账户管理：应用包管理，品牌logo，资质管理
    5. 账户资金：账户资金管理与交易明细

    项目技术：

    1. Spring，SpringMVC，MyBatis
    2. ClickHouse，[MongoDB](https://gaoming95.github.io/2020/06/08/mongodb/)，[Mysql](https://gaoming95.github.io/2020/06/04/Mysql%E8%AE%BE%E8%AE%A1%E8%A7%84%E8%8C%83/)，[Redis](https://gaoming95.github.io/2020/08/06/Redis/)
    3. Cat，JOOQ，XXL-JOB定时任务，RabbitMQ，[Redisson](https://gaoming95.github.io/2021/08/25/Redisson/)，Druid，Kibana+Logstash+ES，[QueryDsl](https://gaoming95.github.io/2020/07/01/QueryDSL/)
    4. [Spring Http Invoker](https://gaoming95.github.io/2020/09/03/Spring%20HTTP%20Invoker/)，Grpc，线程池

  ****

  - **三连创意项目**`2020/06/02-至今`

    项目后端负责人，负责产品需求迭代，以及线上问题跟进

    项目背景：围绕创意点击率的提升，支持代理与广告主更高效地制作创意素材，并传达B站广告创意制作的方法论与高质案例。2021/10/27内测，2020/11/4灰度，2020/11/23上线

    网站地址：https://sanlian.bilibili.com 部分功能需要广告主登录

    项目内容：

    1. 模板制图工具，支持图层级别的调整设计，支持PSD在线解析
    2. 视频GIF制作工具，支持帧级别的调整设计
    3. 作品管理，支持模板编辑、同步投放、历史等操作
    4. 资产管理，支持查看投放素材库中所有创意
    5. 热门广告，热门视频，灵感洞察，我的收藏，支持广告主创意灵感共享，支持内容模块管理

    项目技术：

    1. Springboot，[Web系统框架](https://gaoming95.github.io/2020/06/22/Web%E7%B3%BB%E7%BB%9F%E6%9E%B6%E6%9E%84%E5%88%86%E5%B1%82/)-五层架构
    2. ClickHouse，[Mysql](https://gaoming95.github.io/2020/06/04/Mysql%E8%AE%BE%E8%AE%A1%E8%A7%84%E8%8C%83/)，[Redis](https://gaoming95.github.io/2020/08/06/Redis/)
    3. Cat，XXL-JOB定时任务，Databus（kafka），[Redisson](https://gaoming95.github.io/2021/08/25/Redisson/)，HikariCP，Kibana+Logstash+ES
    4. [Spring Http Invoker](https://gaoming95.github.io/2020/09/03/Spring%20HTTP%20Invoker/)，Grpc，线程池

    项目成果：

    1. 展示B站优秀创意素材，提升广告主素材创作能力
    2. 汇总广告创意投放素材内容，提供统一管理渠道，优化投放中心整体逻辑，提升广告主创建创意能力

  ****

  - **Xbot智能助手项目**`2019/06/12-2020/09/12`

    项目参与者，负责智能助手的框架设计、实体识别和槽位识别模型的训练和部署，以及相关测试

    该项目为部门线上项目，运营商在使用公司产品时，由于功能的复杂性，希望能够减少用户的使用难度。在产品右侧提供智能助手服务，智能助手支持帮助用户执行相关操作。

    根据用户提问的语义信息自动下发任务，设计并实现了基于深度学习的意图识别模型和槽位识别模型。

    意图识别模型识别用户问题意图，基于文本分类任务，将问题识别为系统定义的某一项业务

    槽位识别模型识别用户问题关键实体，基于命名实体识别，将完成任务所需的实体识别出来，其中还包括实体链接等

    意图准确率94%，槽位识别F1值为87%

  ****

  - **领域知识图谱问答系统**`2019/06/12-2020/09/12`

    项目参与者，负责部分实体、关系、属性的定义和AI答案抽取模型的设计

    该项目为部门预研项目，解决部门文档数量多但检索效率差问题。该项目分为知识图谱和AI答案抽取两部分进行。

    方案一知识图谱部分，通过人工或半人工方式定义实体、关系、属性等，抽取文档中的图谱三要素存入图数据库，利用图数据库的图检索能力，可以对图谱的三要素作简单查询。

    方案二AI答案抽取部分，通过关键词或关键实体对文档构建倒排索引，对用户提问抽取关键字或者关键实体，找到对应文档，基于机器阅读理解的答案抽取模型，在候选段落中返回答案。

  ****

  - **精准医疗-中国十万人基因组计划项目**`2018/01/01-2020/04/06`

    项目参与者，负责中文电子病历的命名实体研究，模型的搭建，论文撰写

    该项目由哈尔滨工业大学牵头，课题组参与中文病历命名实体识别方法研究，为了加快Bi-LSTM网络提取病历文本特征，改进了基于ID-CNN模型进行病历实体提取，提出了一种Attention-based ID-CNNs-CRF模型用于命名实体识别，该模型融合单词嵌入、字符嵌入、词性嵌入和位置嵌入的词向量作为模型输入。其次，堆叠4个膨胀卷积块提取全局语义信息，之后，结合注意力机制增强对局部语义信息的感知，最后利用CRF获取最优的标签序列，模型效果和预测速度均优于现有的Bi-LSTM-CRF模型，研究成果[*An attention-based ID-CNNs-CRF model for named entity recognition on clinical electronic medical records*](https://link.springer.com/chapter/10.1007/978-3-030-30493-5_25)，发表于[*ICANN2019*](https://e-nns.org/icann2019/)（*CCF C*类会议），第一作者 

  ****

  - **互联网公开信息采集课题研究**`2021/07/01-2021/11/01`

    项目负责人，负责项目的整体方案设计，进度控制，代码编写，测试，交付等

    与上海资信有限公司合作，共同开发一款数据采集系统用于支持企业贷款安全风控信息分析，主要采集国家企业信息信息公示系统，国家专利系统、被执行人等系统中企业、专利、被执行人信息。该系统分为数据采集系统和管理系统两部分。

    管理系统：基于Spring+SpringMVC+Hibernate，实现用户数据采集任务的创建，定时启动，异常采集过程收集，异常重试，任务实时进度展示，任务完成微信（itchat）和邮件通知，采集数据展示，数据下载等功能

    采集系统：基于Scrpy，Selenium、Requests、Chrome Webdriver，Appium，MySql等技术，采集网站数据。实现分布式任务处理功能，由单台服务器负责响应用户任务，向其他数据采集服务器下发任务。该系统破解了极验滑动验证码，极验3.0文字点击验证码，以及一般的防爬虫技术，并实现了基于CNN的字母数字验证码识别。

- **职业技能**
  - Java后端开发、Python开发
  - 了解[JVM虚拟机](https://gaoming95.github.io/2020/06/02/Java%E8%99%9A%E6%8B%9F%E6%9C%BA/)，有[JVM调优](https://gaoming95.github.io/2020/07/05/Jvm%E5%B7%A5%E5%85%B7/)相关经验
  - 熟悉数据结构，以及相关算法
  - 阅读Java相关源码，[Stream流](https://gaoming95.github.io/2021/07/09/Stream%E6%B5%81%E5%B8%B8%E8%A7%84%E7%94%A8%E6%B3%95/)，[ReentrantLock](https://gaoming95.github.io/2021/09/04/ReentrantLock%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90/)，HashMap，Spring等
  - 使用[RabbitMQ](https://gaoming95.github.io/2020/06/20/RabbitMQ/)，[Redission](https://gaoming95.github.io/2021/08/25/Redisson/)，[Zookeeper](https://gaoming95.github.io/2020/08/02/ZooKeeper/)，[Cat](https://gaoming95.github.io/2021/08/26/Cat/)，XXL-Job等中间件
  - 了解Mysql优化，了解[ElasticSearch](https://gaoming95.github.io/2020/07/15/ElasticSearch/)，[MongoDB](https://gaoming95.github.io/2020/06/08/mongodb/)，Clickhouse，[Redis](https://gaoming95.github.io/2020/08/06/Redis/)数据库
  - 掌握[Git](https://gaoming95.github.io/2020/11/01/Git%E5%9F%BA%E7%A1%80/)、Maven开发工具，了解[Devops](https://gaoming95.github.io/2021/01/15/Devops/)
  - 有ToC项目架构经验和搜索引擎业务相关经验
  - 了解机器学习相关算法，深度学习相关模型，tersorflow，keras等框架，熟练数据采集与分析

- **荣誉证书**

  - 上海市优秀毕业生
  - 上海大学优秀学生
  - 上海大学学业奖学金一等奖

- **所获成果**
  - [*An attention-based ID-CNNs-CRF model for named entity recognition on clinical electronic medical records*](https://link.springer.com/chapter/10.1007/978-3-030-30493-5_25)，发表于[*ICANN2019*](https://e-nns.org/icann2019/)（*CCF C*类会议），第一作者 
  - [*An Improved Method for named entity recognition and Its Appliaction to CEMR*](https://www.researchgate.net/publication/335406934_An_Improved_Method_for_Named_Entity_Recognition_and_Its_Application_to_CEMR)，发表于[*Future Internet*](https://www.mdpi.com/journal/futureinternet)（*ESCI*期刊），第一作者 
  - [*A Topic-Enhanced Recurrent Autoencoder Model for Sentiment Analysis of Short Texts*](https://www.researchgate.net/publication/340775534_A_topic-enhanced_recurrent_autoencoder_model_for_sentiment_analysis_of_short_texts)，发表于*EI*期刊[*International Journal of Internet Manufacturing and Services*](https://www.inderscience.com/jhome.php?jcode=IJIMS)， 第二作者
  - Chinese Electronic Medical Record Named Entity Recognition Based on PreAtt-BiLSTM-CRF，发表于一般期刊*Data SCIENCE AND INDUSTRIAL INTERNET*，第一作者
  - *A Topic-Enhanced Recurrent Autoencoder Model for Sentiment Analysis of Short Texts*，发表于国际会议 [*2018 11th International Conference on Service Science (ICSS2018)*](http://icss2018.shu.edu.cn/)， 第二作者

