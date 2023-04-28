# 概述

Asynchronous Message Service (AMS）

是一种数据系统通信架构，由自主的互操作模块组成，任务系统的**模块**可以被设计为独立运行，每个模块产生和消耗任务信息，而不明确知道其他模块当前正在运行

AMS的目的是通过以易于使用、高度自动化、灵活、健壮、可扩展和高效的方式为数据系统模块之间的信息交换提供标准、可重用的基础设施。

- AAMS

在同一连续体中的模块交换消息

- MAMS

配置服务器提供连续体内的配置信息，使AAMS能够在同一连续体进行消息交换

- RAMS

RAMS支持使用RAMS网关在其他连续体中的模块之间进行通信

# 基本参数

在AMS操作中，Continuum是最大的单元，通过配置服务器进行管理。提供相同功能的一组continuum称为venture，并具有内在venture编号。venture可以由多个continuum组成，单个continuum中的特定venture称为message space。换句话说，message space是continuum和venture的交叉点。venture中的单个module集称为unit。最后，单个continuum中的unit称为cell。cell是continuum和unit的交集

<img width="378" alt="image" src="https://user-images.githubusercontent.com/98028423/235210055-c72b4e03-f44f-4cff-ab89-5adb01bfd455.png">


C为配置服务器、R为注册器、棕与紫 两个连续体、蓝与橙 两个消息空间、黑色 一个venture包括红与灰 两个Unit、19和6 两个模块

- continuum

是一组封闭的**实体**，它们利用MAMS进行相互通信

每个连续体由**continuum name**和相应的非负连续体**编号**标识

- application

采用一组源代码文本文件的形式，依赖AMS过程来实现其目的，**application name**标识

- authority

负责应用程序配置和操作的管理实体,用**authority name**标识

- venture

应用程序的实例

- message

已知大小的八位字节数组

- content

包含消息传递的特定信息

- role

每个角色由**role name**和相应的非负角色编号标识，

- module

通信实体，通过与其他模块交换消息等方式，实现某些AMS项目的部分功能

为了完成AMS消息交换，模块生成AMS服务请求并使用AMS服务指示

- message space

venture中所有模块的集合，venture和continuum的交集

- unit

venture的子集

- cell

unit的成员，continuum的成员

- send

将其复制到指定模块的内存中，

- subscription

订阅模块中在某个subject发布的消息

- invitation

邀请是对邀请域中的模块向邀请模块发送有关subject的消息的方式的声明

# 元素架构

AMS消息交换基本上是异步的。也就是说，每条消息都是以“邮政”而不是“电话”方式发送的：在发送消息时，AMS模块无需等待任何消息的到达

基于AMS的数据系统中的大多数消息交换都是在“publish-subscribe”模块上进行的

<img width="450" alt="image" src="https://user-images.githubusercontent.com/98028423/235210116-dc0edef6-1ea3-4b85-bbe0-b72af874536c.png">

- modules
- registrars

注册机构是一个通信实体，它对信息空间单个单元的注册成员信息进行编目

每个unit由一个registrar提供服务，该注册器负责监控单元注册成员中所有模块的健康状况，并传播六种消息空间配置更改：模块注册和终止、订阅和邀请声明以及订阅和邀请取消。在收到来自其自身单元注册成员的模块的其中一条重新配置消息后，registrar立即将消息传播到该注册成员的其他模块，然后酌情传播到消息空间中其他单元的registrar

- configuration servers

配置服务器是一个通信实体，它对在某些AMS连续体中建立的消息空间信息进行编目

registrar向配置服务器注册包含消息空间的连续体

<img width="391" alt="image" src="https://user-images.githubusercontent.com/98028423/235210181-4e02e4b2-ecd9-47be-a93f-cf975e229455.png">
>
> 热传感器监测模块6和12定期在AAMS消息中发布有关主题“温度”的热传感器数据。两个板载模块订阅“温度”：一个实时热报警模块（模块#19）和一个注册为“遥测源”的模块（模块#4）。每当模块6或模块12发布“温度”消息时，模块4和模块19都会立即收到副本。在收到“温度”信息后，模块4发布热遥测数据包，以传输至地球上的任务操作中心

### RAMS

<img width="419" alt="image" src="https://user-images.githubusercontent.com/98028423/235210243-d0f7b3bd-6fed-4e39-b4c0-096476fb8a77.png">

两个连续体 continuum、一个venture、两个配置服务器、application Q、authority R

通过远程AMS（RAM）程序，AMS应用程序消息可以在不同AMS连续性中的模块之间轻松交换

每个RAMS网关都有两个通信环境的接口：其服务的AMS消息空间和RAMS网络，相互感知的RAMS网关的网状或树状结构，使在一个消息空间中生成的AMS信息能够转发到同一venture的其他消息空间

<img width="359" alt="image" src="https://user-images.githubusercontent.com/98028423/235210282-91d9d9c3-aa3d-4f14-bb9c-d99b576bb290.png">

**RAMS网关**使用**TCP/IP**与消息空间中的其他模块交换**AAMS**消息，但在CCSDS封装数据包和遥测/遥控链路上使用**DTN**捆绑协议和Licklider传输协议与月球车上的对应模块交换**RAMS**消息

> 端到端任务数据系统示例
>
> <img width="395" alt="image" src="https://user-images.githubusercontent.com/98028423/235210363-73943b7c-bc29-4e95-b3aa-49a5cb2b51bc.png">
>
> 每当模块6或模块12发布“温度”消息时，模块19和模块1（“漫游者”连续统中的“实时漫游者操作”RAMS网关）都会立即接收副本。漫游者的RAMS网关使用可靠的延迟/中断容限网络（DTN）协议将“温度”消息传递给MOC中的对应方，然后MOC将该消息转发给两个本地订户，即模块6和8。

# 实现

- amsd

可以作为配置服务器，或者作为单个消息空间的单个单元的注册器，或者同时作为这两者的注册器

```c
amsd mib_source_name eid_spec 
or 
amsd mib_source_name eid_spec application_name authority_name unit_name
```

eid_spec 是一个字符串，指定amsd必须建立的主传输服务应用程序点，使用“@”表示默认端口“2357“

- libams

库文件

- ramsgate

远程AMS网关

```c
ramsgate application_name authority_name_name lifetime [memory_size 
[memory_manager_name]]
```

### Test

> ion安装文件替换成4.1.1 版本的ams，rams莫名其妙通了

当程序启动时，它将分成两个进程， “pitcher” and  “catcher”，这两个进程都注册为“amsdemo/test”消息空间的根单元中的模块。catcher邀请关于subject“text”的消息，并等待第一条这样的消息到达。pitcher等待邀请发送subject为“text”的消息，当它到达时，发送一条这样的消息并终止，当pitcher接收到消息时，它打印消息文本，然后终止

```c
//ionstart
ionadmin	amroc.ionrc
ionadmin	global.ionrc &
ionsecadmin	amroc.ionsecrc &
ltpadmin	amroc.ltprc &
bpadmin		amroc.bprc &

amsd mib.amsrc @ amsdemo test "" &
sleep 90
ramsgate amsdemo test 2592000 &

//mib.amsrc
*ams_mib_init continuum_nbr=2 ptsname=dgr
//初始化MIB，连续体编号2，采用dgr协议，有dgr和udp
+ams_mib_add
//MIB配置项
*continuum nbr=1 name=ion1 desc='ION node 1'
//远程连续体，编号为1
*csendpoint epspec=@:2357
//配置服务器端点
*application name=amsdemo
//连续体的应用程序
+venture nbr=19 appname=amsdemo authname=test net_config=tree 
//本地连续体中的一个venture
//gweid，忽略gweid参数默认：bp@ipn:<local_continuum_number>.<venture_number>
*role nbr=2 name=shell
*role nbr=3 name=log
*role nbr=4 name=pitch
*role nbr=5 name=catch
*role nbr=6 name=benchs
*role nbr=7 name=benchr
*role nbr=8 name=videosender
*role nbr=9 name=videoreceiver
*role nbr=87 name=amsd
*role nbr=88 name=amsstop
*role nbr=89 name=amsmib
//功能标识
*subject nbr=1 name=text desc='ASCII text'
*subject nbr=2 name=noise desc='more ASCII text'
*subject nbr=3 name=bench desc='numbered msgs'
*subject nbr=4 name=textreturn desc='return receipt for text'
*subject nbr=5 name=video desc='video stream'
*subject nbr=88 name=amsstop desc='shutdown cmd'
*subject nbr=89 name=amsmib desc='reconfig cmd'
//发送的消息类型
//*send  *receiver，参数name必须是venture定义的角色的名称
*unit nbr=1 name=orbiters
*unit nbr=2 name=orbiters.near
*unit nbr=3 name=orbiters.far
//确定venture的连接情况
*msgspace nbr=2
//标识消息空间中的远程连续体
//gweid同上
-venture
-ams_mib_add
```

### 文件

- amsrc

mib初始化文件

```bash
ams_mib_init
ams_mib_add
ams_mib_change
ams_mib_delet
```

- amsxml

mib初始化XML文件

同amsmib

-  petition.log

RAMS请求日志

记录所有“请求”，可以在启动之前写入进行预接收

```c
protocolId  1=DTN  2=UDP
gatewayID  发出请求的RAMS网关节点
controlCode  2=确认 3=取消
subject、continuumNumber、unitNumber、roleNumber 发出请求的节点参数
```

### 测试例

- amsshell

消息发布

```c
amsshell unit_name role_name application_name authority_name [{ p | s | q | a }]
//默认以发布的形式运行
/*命令行第一个字符代表本行的意义
=  subject名称
r  设置role的编号
c  设置连续体编号
u  设置单元编号
m  设置向后续发送消息的模块编号
.  结束

p这是“发布”模式。每条消息都已发布。
s这是“发送”模式。每条消息都会私下发送到由指定的模块、单元和连续体编号标识的应用程序模块。
q这是“查询”模式。每个消息都被私下发送到由指定的模块、单元和连续体编号标识的应用程序模块，然后amsshell在继续之前等待回复消息。
a这是“无”模式。每一条消息都会通知由先前指定的角色、单元和连续体编号建立的域中的所有模块。
```

- amslog

消息接收器

```c
amslog unit_name role_name application_name authority_name [{ s | i }]
//输入”+“代表对消息类型感兴趣，订阅消息
```

- amslogprt

从amslog打印AMS日志消息的程序

- amsbenchs
- amsbenchr

### AMS函数

- 模块管理

```c
int ams_register(char *mibSource, char *tsorder, char *applicationName, 
char *authorityName, char *unitName, char *roleName, AmsModule 
*module);
//mibSource  mib位置，NULL默认为roleName.xml
//applicationName,authorityName  mib中声明的值
//启动应用程序作为模块参与本地AMS连续体中的消息空间
int ams_unregister(AmsModule module); 
//终止注册
```

```c
int ams_get_module_nbr(AmsModule module);
int ams_get_unit_nbr(AmsModule module);
得到module和unit的标识号
```

```c
int ams_set_event_mgr(AmsModule module, AmsEventMgt *rules);
//启动一个后台“事件管理器”线程，自动接收和处理为指定模块所有AMS事件（更改通知）
void ams_remove_event_mgr(AmsModule module); 
//停止
```

- 消息订阅和邀请

```c
int ams_invite(AmsModule module, int roleNbr, int continuumNbr, int 
unitNbr, int subjectNbr, int priority, unsigned char flowLabel, 
AmsSequence sequence, AmsDiligence diligence);
//确定模块在特定条件下接受特定主题的消息的意愿
int ams_disinvite(AmsModule module, int roleNbr, int continuumNbr, int 
unitNbr, int subjectNbr);
```

```c
int ams_subscribe(AmsModule module, int roleNbr, int continuumNbr, int 
unitNbr, int subjectNbr, int priority, unsigned char flowLabel, 
AmsSequence sequence, AmsDiligence diligence); 
//用于建立模块的请求，以在指定条件下接收针对指定主题发布的每个未来消息的副本，并说明模块希望发送这些消息
int ams_unsubscribe(AmsModule module, int roleNbr, int continuumNbr, 
int unitNbr, int subjectNbr);
```

- 配置查找（lookup）

```c
int ams_lookup_unit_nbr(AmsModule module, char *unitName);
int ams_lookup_role_nbr(AmsModule module, char *roleName);
int ams_lookup_subject_nbr(AmsModule module, char *subjectName);
int ams_lookup_continuum_nbr(AmsModule module, char *continuumName);
//返回查找的编号
char *ams_lookup_unit_name(AmsModule module, int unitNbr);
char *ams_lookup_role_name(AmsModule module, int roleNbr); 
char *ams_lookup_subject_name(AmsModule module, int subjectNbr); 
char *ams_lookup_continuum_name(AmsModule module, int continuumNbr);
//返回查找的name
```

```c
char *ams_get_role_name(AmsModule module, int unitNbr, int moduleNbr);
int ams_get_continuum_nbr(); 
//get本地名称和编号
```

```c
int ams_rams_net_is_tree(AmsModule module);
//网络是否为tree
```

```c
int ams_continuum_is_neighbor(int continuumNbr);
//连续体是否是本地连续体的邻居
```

- 消息发布

```c
int ams_publish(AmsModule module, int subjectNbr, int priority, 
unsigned char flowLabel, int contentLength, char *content, int 
context); 
//使AMS消息在指定的主题上构造，将该消息的一个副本发送到消息空间中当前声明订阅该主题消息的每个模块
int ams_send(AmsModule module, int continuumNbr, int unitNbr, int 
moduleNbr, int subjectNbr, int priority, unsigned char 
flowLabel, int contentLength, char *content, int context);
//使该消息发送到所指示的连续体内的模块，前提是该模块当前声明关于该主题的消息邀请
int ams_query(AmsModule module, int continuumNbr, int unitNbr, int 
moduleNbr, int subjectNbr, int priority, unsigned char 
flowLabel, int contentLength, char *content, int context, int 
term, AmsEvent *event);
//同send
int ams_reply(AmsModule module, AmsEvent msg, int subjectNbr, int 
priority, unsigned char flowLabel, int contentLength, char 
*content);
//与send相似
int ams_announce(AmsModule module, int roleNbr, int continuumNbr, int 
unitNbr, int subjectNbr, int priority, unsigned char flowLabel, 
int contentLength, char *content, int context); 
//发布到所有模块的集合
```

- 事件接收

```c
int ams_get_event(AmsModule module, int term, AmsEvent *event);
//获取当前AMS事件队列中尚未由应用程序处理的下一个AMS事件
int ams_get_event_type(AmsEvent event);
//返回所指示事件的事件类型
int ams_parse_msg(AmsEvent event, int *continuumNbr, int *unitNbr, int 
*moduleNbr, int *subjectNbr, int *contentLength, char **content, 
int *context, AmsMsgType *msgType, int *priority, unsigned char 
*flowLabel);
//提取事件内容
```

### RAMS函数

```c
int rams_run(char *mibSource, char *tsorder, char *applicationName, 
char *authorityName, char *unitName, char *roleName, int 
lifetime)
//用于将RAMS网关进程注册为AMS模块
```

