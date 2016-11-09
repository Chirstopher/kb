<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [报障功能](#%E6%8A%A5%E9%9A%9C%E5%8A%9F%E8%83%BD)
    - [背景介绍](#%E8%83%8C%E6%99%AF%E4%BB%8B%E7%BB%8D)
    - [功能需求](#%E5%8A%9F%E8%83%BD%E9%9C%80%E6%B1%82)
      - [开放范围](#%E5%BC%80%E6%94%BE%E8%8C%83%E5%9B%B4)
      - [信息收集集合](#%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E9%9B%86%E5%90%88)
      - [传输协议](#%E4%BC%A0%E8%BE%93%E5%8D%8F%E8%AE%AE)
      - [是否支持第三方](#%E6%98%AF%E5%90%A6%E6%94%AF%E6%8C%81%E7%AC%AC%E4%B8%89%E6%96%B9)
    - [行为约定](#%E8%A1%8C%E4%B8%BA%E7%BA%A6%E5%AE%9A)
      - [触发时机](#%E8%A7%A6%E5%8F%91%E6%97%B6%E6%9C%BA)
    - [协议定义](#%E5%8D%8F%E8%AE%AE%E5%AE%9A%E4%B9%89)
      - [注意，有些字段优先级不高，可以在第一期内容置空，放到以后，会有标明，如果需要后期调整的字段，第一阶段可以不要求完善](#%E6%B3%A8%E6%84%8F%EF%BC%8C%E6%9C%89%E4%BA%9B%E5%AD%97%E6%AE%B5%E4%BC%98%E5%85%88%E7%BA%A7%E4%B8%8D%E9%AB%98%EF%BC%8C%E5%8F%AF%E4%BB%A5%E5%9C%A8%E7%AC%AC%E4%B8%80%E6%9C%9F%E5%86%85%E5%AE%B9%E7%BD%AE%E7%A9%BA%EF%BC%8C%E6%94%BE%E5%88%B0%E4%BB%A5%E5%90%8E%EF%BC%8C%E4%BC%9A%E6%9C%89%E6%A0%87%E6%98%8E%EF%BC%8C%E5%A6%82%E6%9E%9C%E9%9C%80%E8%A6%81%E5%90%8E%E6%9C%9F%E8%B0%83%E6%95%B4%E7%9A%84%E5%AD%97%E6%AE%B5%EF%BC%8C%E7%AC%AC%E4%B8%80%E9%98%B6%E6%AE%B5%E5%8F%AF%E4%BB%A5%E4%B8%8D%E8%A6%81%E6%B1%82%E5%AE%8C%E5%96%84)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# 报障功能

### 背景介绍
* 当用户推流或者播放过程中出现卡顿或者其他异常，我们需要更详细的诊断信息，客户也想知道终端用户的确切情况，通过提供API 的方式，让终端用户在界面上触发。比自动上报测试更充分，不过可能离开了用户使用的场景，有一定偏差。

### 功能需求
#### 开放范围
* 对外，我们的客户可以在portal 上查询
* 手机上SDK 提供 API，对外暴露

#### 信息收集集合
* 操作系统信息，包括cpu, 内存，硬盘，硬编，手机型号等
* 网络信息，ping，traceroute 信息等，wifi name, 信号强度。其他网站的访问情况，比如www.baidu.com。http get baidu 首页，httpdownload ping 我们节点
* 媒体信息，当前或者上一个流（播｜推）的地址。试着本地编码看下效果性能等。

#### 传输协议
* 使用JSON 格式，自解释，比较方便查看
* 使用http/https, 方便上传

#### 是否支持第三方
* 非七牛域名不参与诊断

### 行为约定
#### 触发时机
* 用户点击界面
* 客户认为可以触发时主动触发

### 协议定义
#### 注意，有些字段优先级不高，可以在第一期内容置空，放到以后，会有标明，如果需要后期调整的字段，第一阶段可以不要求完善

```
POST /v1/self-diagnosis
Host: pili-qos-ds.qiniuapi.com
Content-Type: application/json

{
	"begain_at": int64 开始时间，单位秒
	"end_at":int64 结束时间，单位秒
	"version": string, sdk 所有组件的版本信息
	"id": string, // 为此次测试生成的随机uuid, 返回给客户端 进行查询
	"os":{
		"device": string // 设备id，和推拉流的id 一致，便于排查
		"model": string  // 设备类型 iphone 4, ipad 2
		"platform": string  // android ios
		"version": string  // 10.0，android rom version
		"appid": string  // android package id, ios bundle id
		"syscpu": int // 占用百分比，系统cpu 占用
		"appcpu": int // 占用百分比，app cpu 占用
		"appcount": int // android, 后台运行app 数目
		"diskleft": int // 剩余磁盘空间, 单位M
		"mem": int // 总的内存, 单位M
		"memleft": int 剩余内存，单位M
		"ui_fps": int UI线程渲染频率，可以通过在UI线程队列插入一段代码，测试执行频率，优先级低，这个会比cpuusage 更有用一点，只在触发后测试10s
		"root": bool, 是否被root 或越狱，会影响系统稳定性
		"battery": int // 电池电量百分比
		"temperature": string // 获取系统温度信息，传回全部文本，去掉里面的""， ios 忽略
		"cpuinfo": string // cpu 详细信息，包括型号，架构，是否neon 等等 iOS 忽略
		"gpuinfo": string // gpu 信息，包括 型号，api 版本 iOS 忽略
	}
	"network":{
		"nettype": string //网络类型
		"isp": string // 运营商，但使用WI-FI 时 不填
		"signal_level": int // 3g 信号强度
		"signal_db": int //3g 或者WI-FI信号强度，单位db, 获取不到时 就不填
		"wifiname": Wi-Fi 热点名字，用来关联 以及排查路由器问题
		"localip": string
		"dns": string
		"ping": { // 可以并行执行
			"baidu.com": { // 当前正在播 或者推流的服务器域名
			   "ip": string 具体IP
			   "size": int 每包大小 字节
			   "sent": int 发送多少
			   "drop":int 丢了多少
			   "interval": int 间隔时间 ms
			   "max": int ms
			   "min": int ms
			   "avg": int ms
			   "stddev": int ms 标准差，衡量波动情况
			},
			...
		},
		"traceroute": { // 可以并行执行
			"www.baidu.com":{ // 当前正在播 或者推流的服务器域名
				"ip": string,
				"content": string， 纯文本 需要trip 里面的"号，一般不会有
			}，
			...
		},
		"httpget": {
			"www.baidu.com":{
				"ip": string,
				"time": int ms
			},
			...
		},
		"httppost": {
			"www.baidu.com":{
				"ip": string,
				"time": int ms
			},
			...
		},
		"tcpping": {
			"baidu.com":{
				"ip":string,
				"sent": int 发送多少次
				"drop":int 失败了多少次
		   		"interval": int 间隔时间 ms
		   		"max": int ms
		   		"min": int ms
		   		"avg": int ms
		   		"stddev": int ms 标准差，衡量波动情况
			}
		}
	},
	"media":{
		"lastplay": string url
		"lastpublish":string url
	}
}

```

函数签名例子
`string self_daig()` // 异步函数，立即返回一个uuid 作为服务端查询根据。
