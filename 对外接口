# streams

## 鉴权 Authorization

```
- allow: "user"
- auths: ["qiniu/mac", "qbox/bearer"]
```

## Stream API

- StreamId
    `<Region>.<HubName>.<StreamTitle>`

- StreamTitle
    格式要求：^[a-zA-Z0-9_-]{4,100}$

- 如果推流时不存在对应 title 的 stream，Pili 会自动创建以该 title 为配置的 stream，
publishKey 和 publishSecurity 继承自对应 Hub。

- 播放不会自动创建任何 stream。

## 创建流

> 该接口为遗留接口，兼容老 sdk 的调用。创建后要请求bc机房 zoned 创建流接口。

```
POST /v1/streams
{
    "title": "<StreamTitle>",  // 可选，如果没指定，由系统生成一个
    "hub": "<HubName>", // 必选
    "publishKey": "<StreamPublishKey>",  // 可选，默认保存空值，表示继承hub上同名配置
    "publishSecurity": "<PublishSecurity>", // 可选，默认为保存空值，表示继承hub上同名配置
}

200 OK
{
    "id": "<StreamId>",
    "hub": "<HubName>",
    "title": "<StreamTitle>",
    "publishKey": "<StreamPublishKey>",
    "publishSecurity": "<PublishSecurity>",
    "disabled": false,
    "hosts": {
        "publish": {
            "rtmp": "<RtmpPublishHost>"
        },
        "live": {
            "rtmp": "<RtmpPlayHost>",
            "hls": "<HLSPlayHost>",
            "hdl": "<HDLPlayHost>",
            "http": "<HLSPlayHost>" // compatible for old code
        },
        "playback": {
            "hls"; "<HLSPlaybackHost>",
            "http": "<HLSPlaybackHost>" // compatible for old code
        }
    }
}

400 Bad request
{
    "error": "bad request",
    "details"; {
        "<Key>": "<Reason>"
    }
}

401 Unauth
{
    "error": "unauth"
}
```


## 列出流

> 本接口不依赖 zoned 接口

* `<HubName>`: 必选。
* `<Status>`: 可选。只能使用connected。
* `<StartMark>`: 可选。如果未指定则从头开始。
* `<LimitCount>`: 可选。如果未指定则用内部的一个上限值（比如1000）。
* `idonly`: 可选，选择后返回内容只有 StreamId 项。用于加速请求速度，一般和 status 配合使用。

```
GET /v1/streams?status=<Status>&hub=<HubName>&marker=<StartMarker>&limit=<LimitCount>&title=<TitlePrefix>

200 OK
{
    "marker": <NextMarker:string>,
    "end": <IfEnd>,
    "items": [
        {
            "id": "<StreamId>",
            "hub": "<HubName>",
            "title": "<StreamTitle>",
            "hosts": {
                "publish": {
                    "rtmp": "<RtmpPublishHost>"
                },
                "live": {
                    "rtmp": "<RtmpPlayHost>",
                    "hls": "<HLSPlayHost>",
                    "hdl": "<HDLPlayHost>",
                    "http": "<HLSPlayHost>" // compatible for old code
                },
                "playback": {
                    "hls"; "<HLSPlaybackHost>",
                    "http": "<HLSPlaybackHost>" // compatible for old code
                }
            }
        }
    ]
}

400 Bad request
{
    "error": "bad request",
    "details"; {
        "<Key>": "<Reason>"
    }
}

401 Unauth
{
    "error": "unauth"
}
```

```
GET /v1/streams?status=<Status>&hub=<HubName>&marker=<StartMarker>&limit=<LimitCount>&title=<TitlePrefix>&idonly

200 OK
{
    "marker": <NextMarker:string>,
    "items": [
        {
            "id": "<StreamId>"
        }
    ]
}

400 Bad request
{
    "error": "bad request",
    "details"; {
        "<Key>": "<Reason>"
    }
}

401 Unauth
{
    "error": "unauth"
}
```

## 获取流

> 本接口会根据 stream 的 region 信息，去对应 zoned 调用相应接口。

```
GET /v1/streams/:streamId
200 OK
{
    "id": "<StreamId>",
    "hub": "<HubName>",
    "title": "<StreamTitle>",
    "publishKey": "<StreamPublishKey>",
    "publishSecurity": "<PublishSecurity>",
    "disabled": false,
    "hosts": {
        "publish": {
            "rtmp": "<RtmpPublishHost>"
        },
        "live": {
            "rtmp": "<RtmpPlayHost>",
            "hls": "<HLSPlayHost>",
            "hdl": "<HDLPlayHost>",
            "http": "<HLSPlayHost>" // compatible for old code
        },
        "playback": {
            "hls"; "<HLSPlaybackHost>",
            "http": "<HLSPlaybackHost>" // compatible for old code
        }
    }
}

401 Unauth
{
    "error": "unauth"
}

404 Not Found
{
    "error": "not found"
}
```

## 更新流

> 本接口会根据 stream 的 region 信息，去对应 zoned 调用相应接口。

```
POST /v1/streams/:streamId
{
    "publishKey": "<StreamPublishKey>",
    "publishSecurity": "<PublishSecurity>",
    "disabled": true
}

200 OK
{
    "id": "<StreamId>",
    "hub": "<HubName>",
    "title": "<StreamTitle>",
    "publishKey": "<StreamPublishKey>",
    "publishSecurity": "<PublishSecurity>",
    "disabled": true,
    "profiles": ["<ProfileName>"],
    "hosts": {
        "publish": {
            "rtmp": "<RtmpPublishHost>"
        },
        "live": {
            "rtmp": "<RtmpPlayHost>",
            "hls": "<HLSPlayHost>",
            "hdl": "<HDLPlayHost>",
            "http": "<HttpPlayHost>" // compatible for old code
        },
        "playback": {
            "hls"; "<HLSPlaybackHost>",
            "http": "<HttpPlaybackHost>" // compatible for old code
        }
    }
}

400 Bad request
{
    "error": "bad request",
    "details"; {
        "<Key>": "<Reason>"
    }
}

401 Unauth
{
    "error": "unauth"
}

404 Not Found
{
    "error": "not found"
}
```

## 删除流

> 本接口会根据 stream 的 region 信息，去对应 zoned 调用相应接口。

```
DELETE /v1/streams/:streamId

200 OK

401 Unauth
{
    "error": "unauth"
}

404 Not Found
{
    "error": "not found"
}
```

## 获取流状态

> 本接口会根据 stream 的 region 信息，去对应 zoned 调用相应接口。

- Status
    - connected
    - disconnected
- reqId
    每个流，每次推流，具有的id，用于查询这个流在某个机器上。此id可能会重复，但大部分情况下不会重复。
    生成方法为：

    ```
    machineID := "bc196"

    func genReqId(machineID string) string {
        var b [12]byte
        copy(b[:8], []byte(machineID+"."))
        binary.LittleEndian.PutUint32(b[8:], uint32(time.Now().UnixNano()/time.Millisecond.Nanoseconds()))
        return base64.URLEncoding.EncodeToString(b[:])
    }
    ```

```
200 OK
{
    "addr": "<RemoteAddress>",
    "startFrom": "<StartFromTime>",
    "updatedAt": "<UpdatedAt>",
    "bytesPerSecond": <CurrentBytesPerSecond>,
    "framesPerSecond": {
        "audio": <AudioFramesPerSecond>,
        "video": <VideoFramesPerSecond>,
        "data": <DataFramesPerSecond>
    },
    "reqId": "<StreamReqID>",
    "status": "<Status>"
}

401 Unauth
{
    "error": "unauth"
}

404 Not Found
{
    "error": "not found"
}
```

## 合并流

> 本接口会根据 stream 的 region 信息，去对应 zoned 调用相应接口。

```
POST /v1/streams/:streamId/saveas
{
    "start": <StartUnixTimestamp>, // 可选，默认为该流最开始的时间
    "end": <EndUnixTimestamp>, // 可选，默认为该流结束的时间
    "name": "<VideoName>", // 如果给出了 name， 会内部计算得到 entry 信息
    "format": "<VideoFormat>" // 可选，为空时，只保存m3u8，不做转码 http://developer.qiniu.com/docs/v6/api/reference/fop/av/avthumb.html
    "pipeline": "<UserPipeline>", // 可选，默认为空
    "notifyUrl": "<NotifyUrl>" // 截图生成时，通知回调地址
}

200 OK
{
    "url": "<m3u8Url>",
    "duration": <DurationOfM3U8>,
    "targetUrl": "<TargetFileUrl>",
    "persistentId": <PersistentId>
}

400 Bad request
{
    "error": "bad request",
    "details"; {
        "<Key>": "<Reason>"
    }
}

401 Unauth
{
    "error": "unauth"
}

404 Not Found
{
    "error": "not found"
}
```

## 获取历史推流信息

> 本接口会根据 stream 的 region 信息，去对应 zoned 调用相应接口。

```
GET /v1/streams/:streamId/segments?start=<StartUnixTimestamp>&end=<EndUnixTimestamp>&limit=<Limit>

200 OK
{
    "start": <StreamStartUnixTimestamp>,
    "end": <StreamEndUnixTimestamp>,
    "duration": <TotalDurationOfRequestSegments>,
    "segments": [
        {
            "start": <StartUnixTimestamp>,
            "end": <EndUnixTimestamp>
        },
        {
            "start": <StartUnixTimestamp>,
            "end": <EndUnixTimestamp>
        }
    ]
}

400 Bad request
{
    "error": "bad request",
    "details"; {
        "<Key>": "<Reason>"
    }
}

401 Unauth
{
    "error": "unauth"
}

404 Not Found
{
    "error": "not found"
}
```

## 截图

> 本接口会根据 stream 的 region 信息，去对应 zoned 调用相应接口。

```
POST /v1/streams/:streamId/snapshot
{
    "name": "<SnapshotName>", // 如果给出了 name， 会内部计算得到 entry 信息
    "format": "<SnapshotFormat>", // 输出图片格式
    "time": <SnapAtUnixTimestamp>, // 可选，默认为最近的时刻截图（大概会有5-10s的延迟）
    "pipeline": "<UserPipeline>", // 可选，默认为空
    "notifyUrl": "<NotifyUrl>" // 截图生成时，通知回调地址
}

200 OK
{
    "persistentId": "<PersistentId>"

    // 遗留
    "targetUrl": "<TargetUrl>",
}

400 Bad request
{
    "error": "bad request",
    "details"; {
        "<Key>": "<Reason>"
    }
}

401 Unauth
{
    "error": "unauth"
}

404 Not Found
{
    "error": "not found"
}
```

## 流可用性

此接口实现时，需要考虑兼容已有 stream.disabled 的行为。建议 stream 增加一个结构：

```go
type Stream struct {
    DisabledTill int64 `bson:"disabledTill" json:"disabledTill"`
    Disabled     bool  `bson:"disabled" json:"disabled"`
}
```

如果 Disabled 为 false，流有效
如果 Disabled 为 true，当前时刻晚于 DisabledTill，流有效，返回 stream 结构时需要将 Disabled 改为 false
如果 Disabled 为 true，当前时刻早于 DisabledTill，流无效
如果 Disabled 为 true，DisabledTill 为 0， 流无效

```
POST /v1/streams/:streamId/available
{
    "available": "<AvailableStatus>", // 默认为 "enabled"，如果设置为 "disabled"，流不可用，对应 stream.disabled 为 true
    "disabledTill": <DisabledTillUnixTimestamp> // 默认为0，当 available 为 disabled 时，此时刻前，disabled 有效，此时刻后，切换为 enabled。如果为空，永远处于 disabled 状态。设置为 enabled 时候，此值不起作用。
}

200 OK

400 Bad request
{
    "error": "bad request",
    "details"; {
        "<Key>": "<Reason>"
    }
}

401 Unauth
{
    "error": "unauth"
}

404 Not Found
{
    "error": "not found"
}
```


## 获取历史推流信息

只能获取最近30天的历史数据。超过数据无法获取。

```
POST /v1/streams/<:StreamId>/history
{
    "start": "<StartTime>", // 必选，2006-01-02T15:04:05Z
    "end": "<EndTime>", // 必选，2006-01-02T15:04:05Z
    "limit": <LimitCount> // 可选，默认不做limit
}

200 OK
{
    "statuses": [
        {
            "addr": "<RemoteAddress>",
            "startFrom": "<StartFromTime>",
            "updatedAt": "<UpdatedAt>",
            "bytesPerSecond": <CurrentBytesPerSecond>,
            "framesPerSecond": {
                "audio": <AudioFramesPerSecond>,
                "video": <VideoFramesPerSecond>,
                "data": <DataFramesPerSecond>
            },
            "reqId": "<StreamReqID>",
            "status": "<Status>"
        },
        ...
    ]
}

400 Bad Request
{
    "error": "bad request",
    "details": {
        "<key>": "<reason>"
    }
}

401 Unauth
{
    "error": "unauth"
}

404 Not Found
{
    "error": "not found"
}
```
