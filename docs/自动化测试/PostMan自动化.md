# PostMan自动化

## 1. 背景
> 当前项目众多，往往都是时间紧、任务重，对测试质量的要求越来越高，回归测试的成本越来越大，所以自动化接口测试势在必行。
>
> **从项目角度看：**
> * 提升回归测试效率
> * 节省人力
> * 降低手工回归测试带来的人为出错风险
> 
> **从个人角度看：**
> * 提升个人开发能力 = 提升个人价值

## 2. 目标
1. 出参和入参的校验，如参数缺失、值不对，等基本的校验
2. 某些组合场景的测试，接口的出参和入参需要从上下文的接口返回中获取的方法
3. 测试配置文件，比如配置文件中，咱们有各种开关，比如免鉴权，是不是关了后，再请求是按照免鉴权的逻辑走
4. 将现网的日志拷下来，然后根据一定的格式参数化后，再请求，如果响应的结果与日志中的一致，那么应该问题不大
5. 发请求后，比如鉴权，查询一个不存在的节目，返回1035，是不是这个节目就一定不存在呢？是否这个节目其实是存在的，但是由于代码的逻辑错误而导出返回是不存在呢？那么我们此时需要自已再去查个库，如果库里面确实没有这个节目，那返回应该是对的 

## 3. 更新说明
### 已完成：
 **2020-11-23~2020-11-25：** 
1. PostMan连接Mysql，已找到两种方式
- 内部二次开发的Sring Boot服务，已完成
- 基于Node js的Omysql服务，已使用
2. 聪聪帮忙找到基于Node js的Omysql服务以及Apifox框架
3. 程品已经开始在编写鉴权的测试用例，并提出自已编写过程中的疑问
4. 开始调研Apifox框架


 **2020-11-09~2020-11-20：** 
1. 三个框架的选型
2. 最终框架的选择
3. 调研Collection、Folder、Request中的Pre-request和Tests的执行顺序
4. 调研校验响应的JsonSchema格式
5. PostMan在Tests中发送http请求
6. PostMan连接数据库
7. 通用方法的抽取
8. newman命令行执行
9. 编写queryCustomer接口

***
### 未完成：
1. 与jenkins执行
2. 测试配置文件，此处需要二次开发
3. 格式化现网的日志后，批量参数化到PostMan中执行
4. 完善测试用例

## 4. 框架选择
> **我们选取了以下三个框架分别进行说明：**

### 4.1. Dech
> **Dark Eliminate译为消灭黑暗，指在通过自动化接口测试回归业务，提升效率**

- 自研框架：Java + TestNg + Retrofit2 + ALLURE2 + MAVEN + Jenkins
- 框架说明：
    - 使用java作为本项目的语言
    - 选择TestNg为单元测试框架
    - 使用Retrofit2作为请求接口的服务驱动框架，接口请求可以集中管理
    - 使用Allure2代替TestNG的报告驱动，二次美化功能，界面更美观
    - 使用Jenkins作为自动化持续集成平台，自动打包、自动运行测试脚本、邮件发送测试报告等。
- GIT地址：[Dech](http://gitlab.ott.bestv.com.cn/otttest/Dech)

### 4.2 MeterSphere
> **MeterSphere 是一站式的开源持续测试平台,涵盖测试跟踪、接口测试、性能测试、团队协作等功能,兼容 JMeter 等开源标准，有效助力开发和测试团队充分利用云弹性进行高度可扩展的自动化测试，加速高质量的软件交付，推动中国测试行业整体效率的提升。**

- 测试脚本
    - 在线编辑测试内容
    - 支持参数化测试
    - 支持断言、变量提取
- 测试报告
    - 自动生成测试报告
    - 多次测试结果对比
    - 查看请求及响应详情
    - 测试报告内容导出
- [MeterSphere官网地址](https://www.fit2cloud.com/metersphere/index.html)
- [MeterSphere测试环境](http://10.215.29.165:8081/login)，用户名: admin 密码: metersphere

### 4.3 PostMan
> **专业的接口测试工具**
>
> [官网地址](https://www.postman.com/)

## 5. 框架比较&选择
主要从以下几个角度进行比较，*代表可用，-代表不可用

|功能 | Dech | MererSphere | PostMan |
|---|:--:|:--:|:--:|
参数化测试 | * | * | *
在线编辑测试内容| 需要开发 | * | 需要注册账号使用Team Workspace<br>但是由于未付费，所以编辑用例有数量限制
支持断言、变量提取 | 需要开发 | * | *
支持全局环境变量 | * | * | *
支持数据库连接 | * | 连接数据库报异常 | *
支持预执行脚本 | * | * | *
支持集合和文件夹的通用预执行脚本 | 需要开发 | 不支持 | *
支持后执行脚本 | * | * | *
支持集合和文件夹的通用后预执行脚本 | 需要开发 | 不支持 | *
支持后执行脚本 | * | * | *
支持通用测试方法的提取 | * | 不支持 | *
支持后执行脚本发送request请求 | 需要开发 | 待研究 | *
支持Json Schema的统一验证 | 需要开发 | 不支持 | *
自动生成测试报告 | 暂不完善 | * | 可以生成HTML等多种形式的测试报告
与Jenkins集成 | 需要开发 | * | *
二次开发 | 需要开发 | 帮助文档不是很完善，如要二次开发成本太高 | 有完善的帮助文档和实践
帮助手册 | 暂不完善 | 不是很完善 | *
百度或GOOGLE上的问题支持 | 无 | 能检索出来的问题很少 | 具有很多的实践文章

**综上，其实对于我们来说核心的诉求是入参、请求、校验的逻辑需要更加的灵活和强大，Dech开发成本较高，其实不需要重复造轮子，MererSphere的接口测试处于起步阶段，主要是解决了在线编辑测试用例的功能，但是校验的功能不是很灵活，POstMan抓住了我们的核心诉求，即主要是校验逻辑可以通用、可以连接数据库、还可以直接在校验逻辑中发请求、集成了很多的第三方库，所以综合后，目前采用PostMan + NewMan + Jenkins + Python或Spring boot进行二次开发作为接口自动化的测试工具**

## 6. 框架使用
### 6.1 下载地址
- [点我下载](https://www.postman.com/downloads/)

### 6.2 帮助手册
- [面向测试人员](https://learning.postman.com/docs/getting-started/introduction/)
- [面向二次开发](https://learning.postman.com/docs/developer/resources-intro/)

### 6.3 使用指南
#### 6.3.1 Collection、Folder、Request中的Pre-request和Tests的执行顺序
PostMan目前有三个层级Collection、Folder、Request，每一个层级下面都有Pre-request和Tests的功能，那么这里就带来了三个问题：

- 脚本的执行顺序?
- 保存在集合/子文件夹中的请求单独发送时是否会执行集合以及子文件中设置的脚本?
- 使用Runner运行集合时, 集合/子文件夹的脚本是只执行一次还是每个脚本都执行一次?

##### 6.3.1.1 脚本的执行顺序说明
 **层级结构:** 

![](https://images.gitee.com/uploads/images/2020/1123/085358_156ec721_1408664.png "WX20201120-171325@2x.png")


1. 我们在BIMS-AAA这个Collection下，点击EDIT后，在Pre-request Script和Tests中分别输入console.log("From: 集合-请求前脚本")和console.log("From: 集合-请求后脚本")，点击保存

 **新建Collection:** 
![](https://images.gitee.com/uploads/images/2020/1123/092714_c2d5e800_1408664.png "WX20201123-092427@2x.png")

2. 我们在该Collection下新建一个queryCustomer的Folder,Pre-request Script和Tests中分别输入console.log("From: 子文件夹-请求前脚本")和console.log("From: 集合-请求后脚本")，点击保存

 **新建Folder:** 
![](https://images.gitee.com/uploads/images/2020/1123/093125_a09a6e19_1408664.png "WX20201123-092528@2x.png")

3. 我们在该Folder目录下新建一个请求，Pre-request Script和Tests中分别输入console.log("From: queryCustomer请求-请求前脚本")和console.log("From: queryCustomer请求-请求后脚本")，点击保存

 **新建queryCustomer请求:** 
![](https://images.gitee.com/uploads/images/2020/1123/093739_4b74ff17_1408664.png "WX20201123-093641@2x.png")

4. 执行脚本，从下图中可以看出，预执行脚本和后执行脚本的顺序都是一个由大到小的顺序

![](https://images.gitee.com/uploads/images/2020/1123/094315_b247b1ee_1408664.png "WX20201123-094236@2x.png")


##### 6.3.1.2 保存在集合/子文件夹中的请求单独发送时是否会执行集合以及子文件中设置的脚本?
单独执行脚本也会触发Collection/Folder中设置的脚本，从上图中可以看出

##### 6.3.1.3 使用Runner运行集合时, 集合/子文件夹的脚本是只执行一次还是每个脚本都执行一次?
假设Collection中有多个请求，那么执行这个Collection时，Collection/Folder中的脚本每个请求都会执行一遍

![](https://images.gitee.com/uploads/images/2020/1123/095249_3a07d5b5_1408664.png "WX20201123-094628@2x.png")

#### 6.3.2 校验响应的JsonSchema格式
> 对于JSON格式的响应数据，在不同的数据和场景下往往会有一部分动态的值及字段。此时我们可以使用JSON Scheme Validator（JSON结构验证）来验证JSON的结构，各参数及嵌套参数的类型，以及必要字段，另外也必须要校验json的格式返回是否正确，如服务有改动，那么可以校验返回的格式是否有更改，可有效用于回归测试。

比如请求aaa模块的queryCustomer接口的返回：
```
{
    "customer": {
        "customerCode": "40100071260627"
    },
    "userList": [
        {
            "userId": "21742317689156"
        }
    ]
}
```

此时我们可以验证：
1. 整体是一个（类型为）object对象，包含属性customer,userList等必要字段（必须出现的字段），目前假设为所有
2. 校验customer为object对象，包含属性customerCode，customerCode的类型是字符串
3. 校验userList是一个列表，每个item中包含属性userId，userId的类型是字符串

转为JSON Schema语法如下：
```
{
  "type": "object",
  "properties": {
    "customer": {
      "type": "object",
      "properties": {
        "customerCode": {
          "type": "string"
        }
      },
      "required": [
        "customerCode"
      ]
    },
    "userList": {
      "type": "array",
      "items": [
        {
          "type": "object",
          "properties": {
            "userId": {
              "type": "string"
            }
          },
          "required": [
            "userId"
          ]
        }
      ]
    }
  },
  "required": [
    "customer",
    "userList"
  ]
}
```



此时可以使用PostMan tv4进行验证，tv4即 Tiny Validator for JSON data的缩写，微型JSON结构验证器。在Postman中的使用方法也很简单，首先在Tests脚本中根据响应编写JSON Schema结构模板，然后使用tv4.validate(jsonData, schema)进行验证即可，同时断言也是通过的，如下图：

![](https://images.gitee.com/uploads/images/2020/1123/101750_d7dbadca_1408664.png "WX20201123-101720@2x.png")

Tests代码：

```
var queryCustomerSchema = {
  "type": "object",
  "properties": {
    "customer": {
      "type": "object",
      "properties": {
        "customerCode": {
          "type": "string"
        }
      },
      "required": [
        "customerCode"
      ]
    },
    "userList": {
      "type": "array",
      "items": [
        {
          "type": "object",
          "properties": {
            "userId": {
              "type": "string"
            }
          },
          "required": [
            "userId"
          ]
        }
      ]
    }
  },
  "required": [
    "customer",
    "userList"
  ]
}

pm.test('queryCustomerSchema is valid', () => {
    pm.expect(tv4.validate(pm.response.json(), queryCustomerSchema)).to.eql(true);
});
```

这里提供一个json格式在线转为json schema格式的网站:

[online-json-to-schema-converter](https://www.liquid-technologies.com/online-json-to-schema-converter)

#### 6.3.3 PostMan在Tests中发送http请求
> PostMan的脚本中提供了可以请求http的能力，这样我们可以做很多的操作，如连接数据库，请求另外一个接口获取数据等操作

代码如下：

```
pm.sendRequest("http://www.baidu.com", (error, response) => {
    if(error) {
        console.log(error);
    }
    console.log(response)
});
```

![](https://images.gitee.com/uploads/images/2020/1123/111227_a472f0df_1408664.png "WX20201123-111133@2x.png")

#### 6.3.4 PostMan连接数据库
> 使用接口测试时，会有需求用到连接数据库，比如查询父子关系接口中通过子账号查询父账号，是需要通过自已查库后得到父账号与接口中返回的父账号进行比较才能确定接口返回的是正确的，但PostMan自身未提供操作数据库的功能，为方便测试人员使用PostMan，进行接口自动化测试时，可以直接请求操作数据库，故提供以下两种方式进行使用。

PostMan连接mysql，目前有两种方法：
- 第一种是内部二次开发的Spring boot服务
- 第二种是Omysql，它是一个node js的服务

##### 6.3.4.1 内部二次开发的Spring boot服务
> 这个服务在10.215.29.169的阿里云机器上，目录为：/usr/nettv/apps/postman_service，这个服务的作用是直接通过post请求，然后将需要连接的数据库的驻地信息和自定义查询sql语句发到服务中，服务根据驻地信息自动切换到对应的数据源，然后返回查询后数据，如果要增加数据源，那么可以在application.properties配置文件中新增或是修改数据源信息，重启服务即可，无需修改代码。

增加数据源示例，分为两步：
1. 假设要新增上海联通的BIMS的mysql库的地址，在application.properties下方新增一下信息：

```
spring.datasource.dynamic.datasource.shlt.url=jdbc:mysql://10.215.29.151:3306/aaa_shlt_bims?useUnicode=true&characterEncoding=utf-8&useSSL=false
spring.datasource.dynamic.datasource.shlt.username=bims
spring.datasource.dynamic.datasource.shlt.password=bims
spring.datasource.dynamic.datasource.shlt.driver-class-name=com.mysql.jdbc.Driver
```
2. 修改dynamic.datasource.list信息，在后台新增shlt的信息，以逗号分割，**注意这里的shlt与上面第一步中的信息保持一致即可，即spring.datasource.dynamic.datasource.驻地信息.url** 

```
dynamic.datasource.list=shyd,shdx,shlt
```

![](https://images.gitee.com/uploads/images/2020/1125/151004_2489b582_1408664.png "WX20201125-150948@2x.png")

请求地址:http://10.215.29.169:9001/api/mysql/queryForList

承载协议：HTTP（POST）

接口功能：用于PostMan请求mysql，获取数据

备注说明：采用POST信息传递参数

请求参数：
| 名称     | 说明        | 数据类型   | 是否必选 |
|--------|-----------|--------|------|
| area   | 请求的数据库的驻地信息，目前为shyd、shdx | String | Y    |
| opType | 目前必须为query  | String | Y    |
| sql    | 数据库查询语句   | String | Y    |

应答参数:
| 名称      | 说明    | 数据类型   | 是否必选 |
|---------|-------|--------|------|
| code    | 响应码，=0：正常，=1：失败，=2：参数错误，=500：系统错误| Int    | Y    |
| message | 响应消息  | String | Y    |
| data    | 响应结果  | Array  | Y    |

请求示例：

![](https://images.gitee.com/uploads/images/2020/1125/150714_d99e3292_1408664.png "WX20201125-150639@2x.png")

在Tests中的代码为：

```
var var1 = {
    "area": "shyd",
    "opType": "query",
    "sql": "select a.id,a.user_id,b.code from ioss_cs_subscriber a, ioss_cs_customer b where a.customer_id = b.id and a.user_id = '2D4003FF004735100000A44027682CA7';"
}

const mysqlRequest = {
    url: 'http://10.215.29.169:9001/api/mysql/queryForList',
    method: "POST",
    header: {
        'Content-Type': 'application/json',
    },
    body: {
        mode: 'raw',
        raw: JSON.stringify(var1)
    }
};

pm.sendRequest(mysqlRequest, (error, response) => {
    console.log(error ? error : response.json());
    console.log(response.json().data[0].id);
});
```

##### 6.3.4.2 Omysql，基于Node js的服务
> 启动后可以直接通过将数据库的地址、端口、账号、密码、库名、查询语句等信息直接发给后台服务，并且返回数据，https://www.cnblogs.com/longronglang/p/11324456.html

调用代码：

```
{
	"host":"10.215.29.151",
	"port":"3306",
	"user":"bims",
	"password":"bims",
    "database":"aaa_shyd_bims",
	"findSql":"select a.id,a.user_id,b.code from ioss_cs_subscriber a, ioss_cs_customer b where a.customer_id = b.id and a.user_id = '2D4003FF004735100000A44027682CA7';"
}
```

从图中可以看出已经返回了查询后的结果

![输入图片说明](https://images.gitee.com/uploads/images/2020/1125/180007_07294d42_1408664.png "WX20201125-175951@2x.png")


#### 6.3.5 通用方法的抽取
> 对接口响应进行验证时，我们需要对返回的状态码、返回的json schema等进行验证，但是如果此时一个接口下的测试用例有10条甚至更多，那么如果在每条用例中的Tests下写同样的验证方法就很冗余，并且不利于维护，此时可以将代码抽取到Collection或是Folder的Tests中。

这里以验证请求的状态码为例，这里在Folder中编写通用的验证方法：

![](https://images.gitee.com/uploads/images/2020/1123/112142_4796db40_1408664.png "WX20201123-112047@2x.png")

然后执行用例时，就会先执行Folder中通用的状态码验证方法，然后在执行请求用例中的定制化的方法，从图中的Tests执行结果可以看到，先是执行了Folder中编写通用的验证状态码的方法，然后再验证这条请求中的Tests中的customerCode is valid的验证方法

![](https://images.gitee.com/uploads/images/2020/1123/112525_1b58241b_1408664.png "WX20201123-112506@2x.png")

 **这里还可以控制某些用例执行某些通用的方法，** 比如这里的UserID为空、和UserID字段缺失这两条用例，这两个请求服务端返回的json的数据都是一样的，那么又不想分别在这两条用例中写同样的验证方法，所以这里可以在Folder中进行用例的判断，如果此时请求是这两条用例（通过pm.info.requestName判断），则走IF控制中的方法，同时UserID不为空的用例则不会走到IF的逻辑中

![](https://images.gitee.com/uploads/images/2020/1123/113522_10924b58_1408664.png "WX20201123-113437@2x.png")

#### 6.3.6 PostMan的常用方法
1. 在使用中，经常需要比较两个字符串是否相等，同时Tests中需要输出预期值和实际值的相关信息，所有这里可以在全局变量中写一个通用的验证方法，再通过eval进行计算，方法中name为返回的元素key，actual为返回的实际值，expected为预期值，这里可以编写多个验证方法
- assertEqual，返回元素是否与预期值一致
- assertNotEqual，返回元素是否与预期值不一致
- assertType，验证返回元素的类型

![](https://images.gitee.com/uploads/images/2020/1124/090823_42bc5452_1408664.png "WX20201124-090606@2x.png")

代码如下:

```
var assertEqual=(name,actual,expected)=>{pm.test(`${name}预期值${expected}(实际值：${actual})`,function(){actual===expected;})};
```

#### 6.3.7 newman命令行执行
> PostMan可以使用命令行下的newman执行，并且可以输出html的报告

步骤为：
1. 将Collection导出成json格式的文件
2. 如果有环境变量也需要导出成json格式的文件
3. 使用命令newman run BIMS-AAA.postman_collection.json -g ./SHYD-Test.postman_environment.json --reporters html --reporter-html-export /usr/nettv/apps/postman/test_1.html
- run后面的json为Collection导出的文件
- -g后面的json为环境变量
- --reporters html为需要导出为html格式
- --reporter-html-export /usr/nettv/apps/postman/test_1.html为导出html结果的路径和文件名

4. 执行完成后，会在相应的目录中生成html报告

可以从此报告中看到概要和请求的详情
![](https://images.gitee.com/uploads/images/2020/1123/113125_f2b9e75a_1408664.png "WX20201123-113105@2x.png")

#### 6.3.8 与Jenkins集成
> 可以与Jenkins集成，这里待补充

#### 6.3.9 queryCustomer案例参考
1. [测试用例下载地址](http://10.215.29.123:8089/apk/BIMS-AAA-queryCustomer-PostMan.zip)，解压zip文件
2. 导入BIMS-AAA的Collection:

![](https://images.gitee.com/uploads/images/2020/1123/122404_c1d5879b_1408664.png "WX20201123-122324@2x.png")

3. 导入环境变量BIMS-SHYD-Test.postman_environment.json

![](https://images.gitee.com/uploads/images/2020/1123/122547_f1698ed4_1408664.png "WX20201123-122525@2x.png")

4. 执行用例

5. 10.215.29.169上的xmysql服务默认目前是关闭的，如果直接执行的话，会导致用例UserID不为空的最后一步验证失败，这是正常的，后续我会改为默认开启xmysql