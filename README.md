# 边缘网关ficus开发者平台全新驱动开发流程

# 简述  
&emsp;&emsp;开发者想要使用亿联云平台赋予的数据可视化、数字孪生等能力，需要先按照开发者平台的要求开发对应系统的驱动程序，完成数据的采集、转换、上报、分析，驱动程序是亿联云平台与末端设备或系统进行交互的桥梁。该文档描述了开发者进行开发开发的步骤、流程以及开发前需要准备的资料。


# 目标  
&emsp;&emsp;通过本流程指示得内容，解析后可以方便驱动开发，理想目标是脱离原始文档，仅通过本流程提供的资料，即可满足开发驱动的所有需要
- 确立厂家接口分析方法
- 明确驱动关心的档案、事件、动作
- 定义接口及接口关系描述的规范
- 定义接口数据及数据转换的规范
- 定义接口及数据规范在开发过程中使用的方法



# 驱动开发过程及数据结构定义  
> 完整驱动描述结构详见附录1
## 1. 确定系统编码
### 名词解释：  
&emsp;&emsp;系统：是园区建设中一类设备或应用所指代的类型，例如车场系统、楼控系统、视频监控系统、消防系统在亿联开发者平台中，一个驱动只能描述一个系统的事件、档案、动作的集合。将用于工程命名
### 编码获取方式
通过亿联开发者平台系统类型列表确定系统编码  
TODO 提供查看系统类型的模块路径
### 配置项
|字段名|字段类型|是否必填|字段注释|字段备注|  
|-|-|-|-|-|
|sysCode|string|是|系统编码|-|


## 2. 确定厂家编码  
### 名词解释：
&emsp;&emsp;厂家编码：是在亿联开发者平台中对设备或系统提供厂家的一个代号，将用于工程命名
### 编码获取方式
- 先从亿联开发者平台厂家列表中通过厂家中文检索  
- 厂家官网logo中的英文小写  
- 厂家官网中主域名英文小写  
- 厂家名称拼音    
以上列表由上倒下优先级依次降低
### 配置项
|字段名|字段类型|是否必填|字段注释|字段备注|  
|-|-|-|-|-|
|factoryCode|string|是|厂家编码|-|


## 3. 确定API及对应的产品型号  
### 说明：
&emsp;&emsp;驱动程序实质上是针对api进行开发，需要明确该api适用于哪些产品及型号。特定api可能适配与厂家软件平台的一个系列、一类公有协议协议（bacnet、snmp等）或某种私有封装协议（TCP、UDP）,型号即平台或协议版本。
apiCode将用于工程命名

### 确定apiCode的方式
- 厂家存在api名称简拼
- 使用软件或硬件产品名称简拼
> 以上项优先级自上而下


### 举例
- 海康综合安防管理平台（iSecure Center）V1.3
```json
{
  "apiName":"海康综合安防管理平台",
  "apiCode":"ISC",
  "apiVersion":"V1.3",
  "products":{
    "code":"CIOT",
    "name":"CIOT",
    "version":"1.0"
  }
}
```
- bacnet
```json
{
  "apiName":"BACNET",
  "apiCode":"bacnet",
  "apiVersion":"2.0",
  "products":[]
}
```

### 配置项
|字段名|字段类型|是否必填|字段注释|字段备注|  
|-|-|-|-|-|
|apiName|string|是|厂家API名称|-|
|apiCode|string|是|厂家编码|-|
|apiVersion|string|是|厂家API版本|-|
|products|array\<obj>|否|APi支持的产品|-|
| \|-code|string|是|受产品编码|-|
| \|-name|string|是|受产品名称|-|
| \|-version|string|是|受产品版本|-|

    
## 4. 厂家api列表  
### 名词解释
&emsp;&emsp;api:此处api包括但不限于restFUL风格的客户端或服务端 ，websocket接口，rpc接口,tcp自由协议，mqtt数据结构  
&emsp;&emsp;事件档案动作表：对亿联开发者平台中所有档案事件动作的一个归纳  
&emsp;&emsp;事件：亿联开发者平台事件是由园区中某一类应用例如车场，楼控等系统或设备产生的记录、状态变更，触发阈值达到的信号或消息。事件分为三类信息、告警、报警  
&emsp;&emsp;档案：对园区中可以产生事件的 实体或虚拟的设备，对其信息的登记信息  
&emsp;&emsp;动作：由亿联开发者平台发出的请求， 对园区中应用发送指令  
### 获取方式
&emsp;&emsp;从厂家提供的全量api中， 对照《事件档案动作表.xlxs》中筛选出可开发的事件、档案、动作的有效api部分
### 配置项
|字段名|字段类型|是否必填|字段注释|字段备注|  
|-|-|-|-|-|
|apiList|obj|是|api列表|当前驱动相关的所有api|
| \|-srcDoc|string|是|原始文档|url或文件副本路径|
| \|-contact|string|是|技术支持联系方式|可留空|
| \|-auth|string|否|api通用鉴权|根据具体协议方式会有不同的配置，通常情况下是一段描述|
| \|-details|array\<obj>|是|api详情|-|
| &emsp;\|-apiID|string|是|apiID|当前文档中保持唯一|
| &emsp;\|-apiType|string|是|api类型|详见附录2协议类型|
| &emsp;\|-apiConfig|obj|是|api配置详情|详见附录3按协议的api配置|


## 5. 驱动关心的档案、事件、动作列表
### 作用：方便快速了解当前驱动都会实现哪些档案、事件、动作
### 配置项
|字段名|字段类型|是否必填|字段注释|字段备注|  
|-|-|-|-|-|
|eventCodeList|array\<string>|是|事件编码列表|当前驱动关注的事件编码|
|archiveCodeList|array\<string>|是|档案编码列表|当前驱动关注的档案列表|
|actionCodeList|array\<string>|是|动作编码列表|当前驱动关注的动作编码列表|

## 6. 配置档案、事件、动作
&emsp;&emsp; 配置第5步包含的每个档案、事件、动作相关得api， 并配置字段之间得对应关系与字段值得转换关系。
### 配置项
####  档案
|字段名|字段类型|是否必填|字段注释|字段备注|  
|-|-|-|-|-|
|archiveConfig|array\<obj>|是|档案配置|-|
| \|-archiveCode|string|是|档案编码|-|
| \|-api|array\<obj>|是|当前档案需要调用的api|结构同eventConfig[].api|  
  
<br>
      
#### 事件
|字段名|字段类型|是否必填|字段注释|字段备注|  
|-|-|-|-|-|
|eventConfig|array\<obj>|是|事件配置|-|
| \|-eventCode|string|是|事件编码|-|
| \|-eventType|string|是|事件类型|eventValue、alarm、desc|
| \|-api|array\<obj>|是|当前事件需要调用的api|-|
| &emsp;\|-apiID|string|是|apiID|-|
| &emsp;\|-apiLevel|string|是|api级别|1级为最外层api|
| &emsp;\|-reqParamSource|obj|是|描述当前请求中必传参数来源方式|参数来源可能是本地配置文件的一个key，一个常量或上级api获得的字段|
| &emsp;\|-apiToValueConfig|string|是|apiID|在事件或档案编排api时一定会存在此对象，但不一定在那一层出现-----TODO 这个描述需要讨论|
| &emsp;&emsp;\|-${\<string>}|string|是|描述资源的key|在事件属于eventValue和alarm类型时此key也是eventValue或alarm，desc类型则为desc.${key}-----TODO 这个描述需要讨论|
| &emsp;&emsp;&emsp;\|-res|string|是|api对应|从api中响应结构的对应字段层次，如果是数组使用中括号表示|
| &emsp;&emsp;&emsp;\|-map|map\<string,string>|是|值映射|-|
| \|-apiToValueConfig|string|是|apiID|在事件或档案编排api时一定会存在此对象，但不一定在那一层出现-----TODO 这个描述需要讨论|
| &emsp;&emsp;\|-${\<string>}|string|是|描述资源的key|在事件属于eventValue和alarm类型时此key也是eventValue或alarm，desc类型则为desc.${key}-----TODO 这个描述需要讨论|
| &emsp;&emsp;&emsp;\|-res|string|是|api对应|从api中响应结构的对应字段层次，如果是数组使用中括号表示|
| &emsp;&emsp;&emsp;\|-map|map\<string,string>|是|值映射|-|
| \|-eparkSingalID|obj|是|亿联开发者平台设备档案唯一id|-|
| &emsp;\|-sign|array\<string>|是|迭代层次关键字|TODOeparkSingalID中结构得解释和理解要单独拿出来 -----TODO 这个描述需要讨论|
| &emsp;\|-changjiacode|string|是|api对应|-|


```json

//desc类事件1  最终使用哪种方式
{
    "eventCode":"cctv_event_1002",
    "eventType":"desc", 
    "api":[{
        "apiID":"appid04",
        "apiLevel":"1",
        "reqParamSource":{}
        
    },{
        "apiID":"appid05",
        "apiLevel":"2",
        "reqParamSource":{"controlid":"appid04.data.list[].id"}
    }],
    "apiToValueConfig":{
            "devcode":{
                "res":"appid05.data.list[].code",
                "map": {}
            },
            "controid":{
                "res":"appid04.data.list[].id",
                "map": {}
            },
            "status":{
                "res":"appid05.data.list[].st",
                "map": {"1":"0","0":"1"}
            }
    },
    "eparkSingalID":{
        "sign":["control","deviceid"],
        "changjiacode":["appid04.data.list[].id","appid05.data.list[].code"]
    }
}
//desc类事件2
{

    "eventCode":"cctv_event_1002",
    "eventType":"desc", 
    "api":[{
        "apiID":"appid04",
        "apiLevel":"1",
        "reqParamSource":{},
        "apiToValueConfig":{
        "controid":{
            "res":"data.list[].id",
            "map": {}
        }
    }
    },{
        "apiID":"appid05",
        "apiLevel":"2",
        "请求参数来源":{"controlid":"appid02.data.list[].id"},
        "apiToValueConfig":{
            "status":{
                "res":"data.list[].st",
                "map": {"1":"0","0":"1"}
            },
            "devcode":{
                "res":"data.list[].code",
                "map": {}
            }
        }
    }],
    "eparkSingalID":{
        "sign":["control","deviceid"],
        "changjiacode":["appid04.data.list[].id","appid05.data.list[].code"]
    }
}
```
####  动作
|字段名|字段类型|是否必填|字段注释|字段备注|  
|-|-|-|-|-|
|actionConfig|array|是|动作配置|-|
| \|-actionCode|string|是|动作编码|-|
| \|-actionType|string|是|动作类型|-|
| \|-api|array\<obj>|是|当前档案需要调用的api||
| &emsp;\|-apiID|string|是|apiID|-|
| &emsp;\|-apiLevel|string|是|apiID|-|
| &emsp;\|-reqParamSource|obj|是|描述当前请求中必传参数来源方式|-|
| &emsp;\|-resSuccess|obj|是|判断调用成功的依据|如果api为多级调用，则本字段大多会出现在最后一级|

```json
{
  "actionConfig":[
      {
          "actionCode":"",
          "actionType":"",
          "api":[{
            "apiID":"",
            "apiLevel":"1",
            "reqParamSource":{},
            "resSuccess":{}
          }]
      }
  ]
}
```

# 7 通过以上描述的配置内容开发驱动
## 1. 创建工程项目
- 下载设备驱动模板
- 更新工程名和go.mod模块名  ，工程名规则： 系统名-厂家编码-apiCode
## 2. 更新配置文件

- 项目相关配置文件：
  +  配置路径：res/conf/projectconfig.yaml（同一驱动在不同项目中的配置不同）
  +  配置项：  
     - projectCode: 项目code在eparkSignalId中拼接
     -  gatewayCode: 网关编码
     -  driverCode: 驱动编码
     -  driverToken: 驱动Token

- 驱动相关配置文件：
  + 配置路径：res/conf/driverconfig.yaml（当前驱动特定配置内容）
  + 配置项
    - factoryCode: 厂家编码
    - deviceName: 设备名称 -- 同 res/devices/devicelist.toml.Name

- 其他配置项：推荐在以上两个配置文件中扩展对应的属性

- 资源描述文件： 
  + 配置路径：res/profiles/xxx.yaml 亿联开发者平台边缘网关规定一个驱动有且只有一个资源描述文件
  + 配置项：
    - name： 当前资源配置文件名称 -- 同 res/devices/devicelist.toml.ProfileName 

- 配置设备： 
  + 配置路径：res/devices/devicelist.toml 亿联开发者平台边缘网关规定一个驱动有且只有一个设备
  + 配置项：
    - Name: 当前驱动对应的设备名称 -- 同 res/conf/driverconfig.yaml.deviceName
    - ProfileName： 当前驱动中Name代表的设备所拥有的资源 同 res/profiles/xxx.yaml.name

3. 对照第4步api列表封装

4. 实现档案、事件上报逻辑
5. 实现动作逻辑：动作逻辑即实现 HandleWriteCommands方法
6. 调试环境debug调试
7. docker镜像调试


# 附录1 完整驱动描述结构
 

|字段名|字段类型|是否必填|字段注释|字段备注|  
|-|-|-|-|-|
|sysCode|string|是|系统编码|-|
|factoryCode|string|是|厂家编码|-|
|apiName|string|是|厂家API名称|-|
|apiCode|string|是|厂家编码|-|
|apiVersion|string|是|厂家API版本|-|
|products|array\<obj>|否|APi支持的产品|-|
| \|-code|string|是|受产品编码|-|
| \|-name|string|是|受产品名称|-|
| \|-version|string|是|受产品版本|-|
|apiList|obj|是|api列表|当前驱动相关的所有api|
| \|-srcDoc|string|是|原始文档|url或文件副本路径|
| \|-contact|string|是|技术支持联系方式|可留空|
| \|-auth|string|否|api通用鉴权|根据具体协议方式会有不同的配置，通常情况下是一段描述|
| \|-details|array\<obj>|是|api详情|-|
| &emsp;\|-apiID|string|是|apiID|当前文档中保持唯一|
| &emsp;\|-apiType|string|是|api类型|详见附录2协议类型|
| &emsp;\|-apiConfig|obj|是|api配置详情|详见附录3按协议的api配置|
|eventCodeList|array\<string>|是|事件编码列表|当前驱动关注的事件编码|
|archiveCodeList|array\<string>|是|档案编码列表|当前驱动关注的档案列表|
|actionCodeList|array\<string>|是|动作编码列表|当前驱动关注的动作编码列表|
|eventConfig|array\<obj>|是|事件配置|-|
| \|-eventCode|string|是|事件编码|-|
| \|-eventType|string|是|事件类型|eventValue、alarm、desc|
| \|-api|array\<obj>|是|当前事件需要调用的api|-|
| &emsp;\|-apiID|string|是|apiID|-|
| &emsp;\|-apiLevel|string|是|api级别|1级为最外层api|
| &emsp;\|-reqParamSource|obj|是|描述当前请求中必传参数来源方式|参数来源可能是本地配置文件的一个key，一个常量或上级api获得的字段|
| &emsp;\|-apiToValueConfig|string|是|apiID|在事件或档案编排api时一定会存在此对象，但不一定在那一层出现-----TODO 这个描述需要讨论|
| &emsp;&emsp;\|-${\<string>}|string|是|描述资源的key|在事件属于eventValue和alarm类型时此key也是eventValue或alarm，desc类型则为desc.${key}-----TODO 这个描述需要讨论|
| &emsp;&emsp;&emsp;\|-res|string|是|api对应|从api中响应结构的对应字段层次，如果是数组使用中括号表示|
| &emsp;&emsp;&emsp;\|-map|map\<string,string>|是|值映射|-|
| \|-eparkSingalID|obj|是|亿联开发者平台设备档案唯一id|-|
| &emsp;\|-sign|array\<string>|是|迭代层次关键字|TODOeparkSingalID中结构得解释和理解要单独拿出来 -----TODO 这个描述需要讨论|
| &emsp;\|-changjiacode|string|是|api对应|-|  
|archiveConfig|array\<obj>|是|档案配置|-|
| \|-archiveCode|string|是|档案编码|-|
| \|-api|array\<obj>|是|当前档案需要调用的api|结构同eventConfig[].api|
|actionConfig|array|是|动作配置|-|
| \|-actionCode|string|是|动作编码|-|
| \|-actionType|string|是|动作类型|-|
| \|-api|array\<obj>|是|当前档案需要调用的api||
| &emsp;\|-apiID|string|是|apiID|-|
| &emsp;\|-apiLevel|string|是|apiID|-|
| &emsp;\|-reqParamSource|obj|是|描述当前请求中必传参数来源方式|-|
| &emsp;\|-resSuccess|obj|是|判断调用成功的依据|如果api为多级调用，则本字段大多会出现在最后一级|
```json
{
    "sysCode":"cctv",
    "factoryCode":"hikvision",
    "apiName":"综合安防管理平台",
    "apiCode":"isc",
    "apiVersion":"1.3",
    "products":[
      {"code":"","name":"","version":""}
    ],
    "apiList":{
            "srcDoc":"url或文件副本路径",
            "contact":"",
            "details":[
                {
                    "apiID":"",
                    "apiType":"",//协议类型
                    "apiConfig":{}
                }
            ]
    },
    "eventCodeList":["cctv_event_1000","cctv_event_1001"],
    "archiveCodeList":["cctv_archive_1000","cctv_archive_1001"],
    "actionCodeList":["cctv_action_1000","cctv_action_1001"],
    
    
    "eventConfig":[{
        "eventCode":"cctv_event_1000",
        "eventType":"eventValue", 
        "api":[{
            "apiID":"",
            "apiLevel":"1",
            "reqParamSource":{},
            "apiToValueConfig":{
                "eventValue":{
                    "res":"data.list[].name",
                    "map": {
                        "50001": "视频丢帧",
                        "50002": "视频掉线"
                    }
                }
            }
        }],
        "eparkSingalID":{
            "sign":["controlID"],
            "changjiacode":["data.list[].id"]
        }
    },{
        
        "eventCode":"cctv_event_1001",
        "eventType":"alarm", 
        "api":[{
            "apiID":"appid02",
            "apiLevel":"1",
            "reqParamSource":{}
        },{
            "apiID":"appid03",
            "apiLevel":"2",
            "reqParamSource":{"controlid":"appid02.data.list[].id"},
            "apiToValueConfig":{
                "alarm":{
                    "res":"data.list[].status",
                    "map": {
                        "1": "0",
                        "0": "1"
                    }
                }
            }
        }],
        "eparkSingalID":{
            "sign":["deviceid"],
            "changjiacode":["appid02.data.list[].id"]
        }
    },{
        "eventCode":"cctv_event_1002",
        "eventType":"desc", 
        "api":[{
            "apiID":"appid04",
            "apiLevel":"1",
            "reqParamSource":{}
            
        },{
            "apiID":"appid05",
            "apiLevel":"2",
            "reqParamSource":{"controlid":"appid04.data.list[].id"}
        }],
        "apiToValueConfig":{
                "devcode":{
                    "res":"appid05.data.list[].code",
                    "map": {}
                },
                "controid":{
                    "res":"appid04.data.list[].id",
                    "map": {}
                },
                "status":{
                    "res":"appid05.data.list[].st",
                    "map": {"1":"0","0":"1"}
                }
        },
        "eparkSingalID":{
            "sign":["control","deviceid"],
            "changjiacode":["appid04.data.list[].id","appid05.data.list[].code"]
        }
    },{

        "eventCode":"cctv_event_1002",
        "eventType":"desc", 
        "api":[{
            "apiID":"appid04",
            "apiLevel":"1",
            "reqParamSource":{},
            "apiToValueConfig":{
              "controid":{
                  "res":"data.list[].id",
                  "map": {}
              }
            }
        },{
            "apiID":"appid05",
            "apiLevel":"2",
            "请求参数来源":{"controlid":"appid02.data.list[].id"},
            "apiToValueConfig":{
                "status":{
                    "res":"data.list[].st",
                    "map": {"1":"0","0":"1"}
                },
                "devcode":{
                    "res":"data.list[].code",
                    "map": {}
                }
            }
        }],
        "eparkSingalID":{
            "sign":["control","deviceid"],
            "changjiacode":["appid04.data.list[].id","appid05.data.list[].code"]
        }
    }],
    "archiveConfig":[
      {
        "archiveCode":"",
        "api":[{
            "apiID":"",
            "apiLevel":"1",
            "reqParamSource":{},
            "apiToValueConfig":{
                "attr1":{
                    "res":"data.list[].name",
                    "map": {
                        "50001": "视频丢帧",
                        "50002": "视频掉线"
                    }
                },
                "attr2":{
                    "res":"data.list[].name",
                    "map": {
                        "50001": "视频丢帧",
                        "50002": "视频掉线"
                    }
                },
                "attr3":{
                    "res":"data.list[].name",
                    "map": {
                        "50001": "视频丢帧",
                        "50002": "视频掉线"
                    }
                }
            }
        }]
      }
    ],
    "actionConfig":[
        {
            "actionCode":"",
            "actionType":"",
            "api":[{
              "apiID":"",
              "apiLevel":"1",
              "reqParamSource":{},
              "resSuccess":{}
            }]
        }
    ]
}
```


# 附录2 协议类型
HttpClient HttpServer TCPServer TCP Client UDP Bacnet DB 动态库 TODO待补充

# 附录3 按协议的api配置
> 待补充
