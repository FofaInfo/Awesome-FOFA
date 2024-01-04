# FOFA SDK 使用说明

## 简介
  基于 [`FOFA API`](https://fofa.info/api) 编写的 `python` 版 `SDK`, 方便 `python` 开发者快速将 `FOFA` 集成到自己的项目中。

  [Api Documentation](https://fofapro.github.io/fofa-py/index.html)

## FOFA 命令行工具
  基于FOFA python SDK实现的简单命令行工具，用于简单的FOFA数据查询

## 安装
```shell
pip install fofa-py
```

## 用法

 显示所有支持的命令
```shell
fofa
```
### 设置fofa api key
  通过环境变量设置认证信息

| 环境变量Key  | Value                                                      |
|--------------|:----------------------------------------------------------:|
| `FOFA_EMAIL` | 用户登陆 `FOFA` 使用的 `Email`                             |
| `FOFA_KEY`   | 前往 [个人中心](https://fofa.info/userInfo) 查看 `API Key` |


### search
搜索子命令，从fofa搜索数据，

#### 搜索数据
搜索数据并展示结果
```shell
fofa search domain=bing.com --size 1000 -f ip,port,domain,title,certs_match,certs_expired
```

#### 统计搜索结果数量
```shell
fofa search domain=bing.com --count
382128
```

#### 查询聚合信息
```shell
fofa search domain=bing.com -f port,domain,protocol,title --stats --size 10
```

#### 保存结果数据
保存结果数据为csv或xls格式，示例:

```shell
# 保存数据为csv文件
fofa search domain=bing.com -f ip,port,domain,link,title,certs_match,certs_expired --size 50000 --save bing.csv

# 搜索证书匹配的数据
fofa search 'domain="bing.com" && cert.is_match=true' -f ip,port,domain,link,title,certs_match,certs_expired --size 50000 --save bing_cert_expired.xls
```

### host
查看一个域名或ip的host信息，示例

```shell
fofa host www.bing.com
{
    "asn": 59067,
    "category": [
        "其他企业应用"
    ],
    "consumed_fpoint": 0,
    "country_code": "CN",
    "country_name": "China",
    "domain": [
        "tuzhiji.com",
        "61.129.255.240:8080"
    ],
    "error": false,
    "host": "www.bing.com",
    "ip": "202.89.233.101",
    "org": "Microsoft Mobile Alliance Internet Services Co., Ltd",
    "port": [
        443,
        80,
        8080
    ],
    "product": [
        "Microsoft-RSA-TLS-CA",
        "Microsoft-RSA-TLS-CA-02"
    ],
    "protocol": [
        "https",
        "http"
    ],
    "required_fpoints": 0,
    "update_time": "2023-06-27 08:00:00"
}
```

### 代码使用sdk

``` python
# -*- coding: utf-8 -*-
import fofa

if __name__ == "__main__":
    email, key = ('test@test.com', 'xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx')  # 输入email和key
    client = fofa.Client(email, key)                # 将email和key传入fofa.Client类进行初始化和验证，并得到一个fofa client对象
    query_str = 'header="thinkphp" || header="think_template"'
    for page in range(1, 51):                       # 从第1页查到第50页
        fpoint = client.get_userinfo()["fofa_point"]      # 查询F点剩余数量
        if fpoint < 100:
            break                                   # 当F点小于100时，不再获取数据
        data = client.search(query_str, size=100, page=page, fields="ip,city")  # 查询第page页数据的ip和城市
        for ip, city in data["results"]:
            print "%s,%s" % (ip, city)              # 打印出每条数据的ip和城市

```


## 协议
`FOFA SDK` 遵循 `MIT` 协议，查看 [协议文件](https://opensource.org/licenses/mit)