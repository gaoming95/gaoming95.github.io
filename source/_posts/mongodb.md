---
title: MongoDB
subtitle: Spring集成MongoDB
date: 2020-06-08
author: 高明
tags:
	- 数据库
---

# MongoDb

## 安装 MongoDb

## 配置

### mongodb.conf

```bash
dbpath=/var/lib/mongo                                                                                                                     logpath=/var/log/mongodb/mongod.log
logappend=true
fork=true
port=27017
bind_ip=0.0.0.0
auth=true
```

### 启动服务

```bash
# 启动服务
./mongod --dbpath /var/lib/mongo --logpath /var/log/mongodb/mongod.log --fork
./mongod --config mongodb.conf
```

### 关闭服务

```bash
# 关闭服务
./mongod --shutdown --dbpath /var/lib/mongo
```

### 创建用户 

```sql
db.createUser({user:"test",pwd:"test",roles: [{role: "root", db: "db_name"}]})
db.createUser({user:"test",pwd:"test",roles:['readWrite']})
```

```
mongo -utest -ptest
db.auth("test","test")
```

## SpringData 集成

```xml
<dependency>
    <groupId>org.mongodb</groupId>
    <artifactId>mongo-java-driver</artifactId>
    <version>3.1.0</version>
</dependency>
```

```java
@Configuration
@PropertySource(value = "classpath:mongo.properties")
@EnableMongoRepositories(basePackages = "com.biz.mongo.repositories")
public class MongoConfig {

    @Value("${mongo.host:}")
    private String host;
    @Value("${mongo.port:}")
    private int port;
    @Value("${mongo.dbname:db_name}")
    private String dbname;
    @Value("${mongo.username:test}")
    private String username;
    @Value("${mongo.password:test}")
    private String password;
    @Value("${mongo.connectionsPerHost:10}")
    private int connectionsPerHost;
    @Value("${mongo.threadsAllowedToBlockForConnectionMultiplier:5}")
    private int multiplier;
    @Value("${mongo.connectTimeout:1000}")
    private int connectTimeout;
    @Value("${mongo.maxWaitTime:1500}")
    private int maxWaitTime;
    @Value("${mongo.socketKeepAlive:true}")
    private boolean socketKeepAlive;
    @Value("${mongo.socketTimeout:1500}")
    private int socketTimeout;

    @Bean
    public MongoClientFactoryBean mongo() {

        MongoClientOptions options = MongoClientOptions.builder()
                .threadsAllowedToBlockForConnectionMultiplier(multiplier)
                .connectionsPerHost(connectionsPerHost)
                .connectTimeout(connectTimeout)
                .maxWaitTime(maxWaitTime)
                .socketTimeout(socketTimeout)
                .build();

        MongoClientFactoryBean mongo = new MongoClientFactoryBean();
        MongoCredential[] mongoCredentials = new MongoCredential[1];
        mongoCredentials[0] = MongoCredential.createCredential(username, dbname, password.toCharArray());
        mongo.setHost(host);
        mongo.setPort(port);
        mongo.setCredentials(mongoCredentials);
        mongo.setMongoClientOptions(options);
        return mongo;
    }

    @Bean(value = "mongoTemplate")
    public MongoOperations mongoTemplate(Mongo mongo) {
        return new MongoTemplate((MongoClient) mongo, dbname);
    }
}
```

### Po

```java
@Builder
@Data
@NoArgsConstructor
@AllArgsConstructor
@Document(collection = "page_config")
public class MongoPageConfigPo {
    @Id()
    private Long pageId;

    @Field("config")
    private Object config;

    @Field("accountId")
    private Integer accountId;
}
```

### IMongoPageRepository

```java
public interface IMongoPageRepository extends MongoRepository<MongoPageConfigPo, Long> {
}
```

### IMongoPageService 业务接口与实现

```java
public interface IMongoPageService {

    /**
     * 插入页面配置
     *
     * @param dto
     */
    void savePage(MongoPageDto dto);
}
```

```java
@Service
public class MongoPageServiceImpl implements IMongoPageService {

    @Autowired
    private IMongoPageRepository MongoPageRepository;

    @Override
    public void savePage(MongoPageDto dto) {
        MongoLandPageConfigPo po = convertMongoPageDto2Po(dto);
        MongoPageRepository.save(po);
    }

    private MongoLandPageConfigPo convertMongoPageDto2Po(MongoPageDto dto) {
        return MongoLandPageConfigPo.builder()
                .pageId(dto.getPageId())
                .config(dto.getConfig())
                .accountId(dto.getAccountId())
                .build();
    }
}
```

## 本地测试

```java
public class MyMain {
    public static void main(String[] args) {
//连接服务器端的地址和端口号
        ServerAddress serverAddress = new ServerAddress("127.0.0.1", 27017);
//project数据库的用户名为:test 密码：test
        char[] password = "test".toCharArray();
//创建 用户名和密码认证
        MongoCredential mongoCredential = MongoCredential.createCredential("test", "db_name", password);
        List<MongoCredential> mongoCredentialList = new ArrayList<MongoCredential>();
        mongoCredentialList.add(mongoCredential);

//连接MongoDB的一个数据库，需要用户名、密码、数据库、服务器端的IP和端口号
        MongoClient mongoClient = new MongoClient(serverAddress, mongoCredentialList);
        MongoDatabase mongoDatabase = mongoClient.getDatabase("db_name");
        mongoDatabase.createCollection("777");

        System.out.println("connect successfully!");
    }
}
```





