# FOFA SDK User Guide

## Introduction
FOFA SDK is a Python SDK developed based on [FOFA API](https://fofa.info/api), which allows Python developers to easily integrate FOFA into their projects.

## FOFA Command-line Tool
FOFA Command-line Tool is a simple command-line tool implemented based on FOFA Python SDK, used for simple FOFA data queries.

## Installation
```shell
pip install fofa-py
```

## Usage

Display all supported commands
```shell
fofa
```

### Set FOFA API Key
Set authentication information using environment variables

| Environment Variable Key | Value                                                       |
|--------------------------|:-----------------------------------------------------------:|
| `FOFA_EMAIL`             | Email used for logging in to FOFA                           |
| `FOFA_KEY`               | API Key obtained from [Personal Center](https://fofa.info/userInfo) |

### search
Subcommand for searching data from FOFA

#### Search Data
Search data and display the results
```shell
fofa search domain=bing.com --size 1000 -f ip,port,domain,title,certs_match,certs_expired
```

#### Count Search Results
```shell
fofa search domain=bing.com --count
382128
```

#### Query Aggregation Information
```shell
fofa search domain=bing.com -f port,domain,protocol,title --stats --size 10
```

#### Save Result Data
Save the result data in CSV or XLS format. Examples:

```shell
# Save data as a CSV file
fofa search domain=bing.com -f ip,port,domain,link,title,certs_match,certs_expired --size 50000 --save bing.csv

# Search for data with matching certificates
fofa search 'domain="bing.com" && cert.is_match=true' -f ip,port,domain,link,title,certs_match,certs_expired --size 50000 --save bing_cert_expired.xls
```

### host
View host information for a domain or IP. Example:

```shell
fofa host www.bing.com
{
    "asn": 59067,
    "category": [
        "Other Enterprise Application"
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
    "update_time": "2023-06-27 16:00:00"
}
```

### Code using SDK

```python
# -*- coding: utf-8 -*-
import fofa

if __name__ == "__main__":
    email, key = ('test@test.com', 'xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx')  # Input email and key
    client = fofa.Client(email, key)                # Initialize and authenticate with fofa.Client using email and key, and get a fofa client object
    query_str = 'header="thinkphp" || header="think_template"'
    for page in range(1, 51):                       # Query from page 

1 to page 50
        fpoint = client.get_userinfo()["fofa_point"]      # Check the remaining number of F points
        if fpoint < 100:
            break                                   # Stop getting data when F points are less than 100
        data = client.search(query_str, size=100, page=page, fields="ip,city")  # Query the IP and city data for the current page
        for ip, city in data["results"]:
            print "%s,%s" % (ip, city)              # Print the IP and city for each data record

```


## License
`FOFA SDK` is licensed under the `MIT` License. See the [LICENSE](https://opensource.org/licenses/mit) file for more details.