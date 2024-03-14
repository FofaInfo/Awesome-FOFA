# Practical FOFA Asset Discovery: COLDRIVER

![0](https://github.com/FofaInfo/Awesome-FOFA/blob/529738afb527a50b18b885c19b2c769fb0394402/Storage/coldriver/coldriverpage.png)

## Overview
COLDRIVER (also known as UNC4057, Star Blizzard, and Callisto) is a Russian threat group specializing in credential phishing activities targeting non-governmental organizations, former intelligence and military officers, as well as prominent individuals of NATO governments.

**This article attempts to trace recent activities of COLDRIVER based on clues, using the FOFA platform to search for traces of the COLDRIVER organization. Eventually, 2 data points very similar to the C2 publicly disclosed on January 18, 2024, were discovered on the FOFA platform. The activity period is between August and October 2023. However, no unknown asset samples were found for the newly discovered suspicious IOCs, but we believe these two IPs are highly suspicious.**

## Collection of Original Samples and C2 Addresses
Google Threat Analysis Group published an analysis article on phishing attacks by the APT organization COLDRIVER on January 18, 2024. The article found that the APT organization COLDRIVER was active between August and September 2023 and obtained a C2 address through samples.

![1](https://github.com/FofaInfo/Awesome-FOFA/blob/529738afb527a50b18b885c19b2c769fb0394402/Storage/coldriver/coldriver1.png)

Through this article, we obtained the initial IOC information:

```
path:
/ws

c2:
45.133.216[.]15:3000
```

## Asset Discovery

Use FOFA to query the C2 address mentioned in the article:

![2](https://github.com/FofaInfo/Awesome-FOFA/blob/529738afb527a50b18b885c19b2c769fb0394402/Storage/coldriver/coldriver2.png)

It can be seen that the update time on FOFA is close to the reporting time on 2023-10-16. From this FOFA data, few valid information were discovered, only the port 3000, TLS protocol banner information, and the existence of certificates.

```
1. Port 3000
FOFA Syntax: port="3000"
2. TLS banner information
FOFA Syntax: banner="\x15\x03\x03\x00\x02\x022"
3. Existence of certificates
```

With the above known information, several approaches for expansion were considered.

**Discovery 1: Through the search of port 3000 and its banner information, 17 records with 16 IPs were discovered.**

`banner="\x15\x03\x03\x00\x02\x022" && port="3000"`

![3](https://github.com/FofaInfo/Awesome-FOFA/blob/529738afb527a50b18b885c19b2c769fb0394402/Storage/coldriver/coldriver3.png)

After observing these 17 data points, it was found that their port and banner information were highly similar, but there were still obvious data misreports. Therefore, it is not feasible to only search through banner and port in this way, further conditions need to be added. Consequently, we added the certificate condition and obtained 5 data points:

`banner="\x15\x03\x03\x00\x02\x022" && port="3000" && cert="Internet Widgits Pty Ltd"`

![4](https://github.com/FofaInfo/Awesome-FOFA/blob/529738afb527a50b18b885c19b2c769fb0394402/Storage/coldriver/coldriver4.png)

**Discover 2: Through certificate search, we found that the Organization in the certificate: Internet Widgits Pty Ltd should be the default value when configuring the certificate. **

An analysis found a crucial clue, which is the certificate expiration date. We searched through the certificate expiration date and the default organization name:

![5](https://github.com/FofaInfo/Awesome-FOFA/blob/529738afb527a50b18b885c19b2c769fb0394402/Storage/coldriver/coldriver5.png)

`cert="Internet Widgits Pty Ltd" && cert="2023-06-23 15:59 UTC"`

![6](https://github.com/FofaInfo/Awesome-FOFA/blob/529738afb527a50b18b885c19b2c769fb0394402/Storage/coldriver/coldriver6.png)

6 IPs were searched out, and these 6 IPs were considered to have highly consistent certificate information.

Summary:

Combining data from Expansion 1 and Expansion 2, 3 data points were obtained:

`banner="\x15\x03\x03\x00\x02\x022" && port="3000" && cert="Internet Widgits Pty Ltd" && cert="2023-06-23 15:59 UTC"`

![7](https://github.com/FofaInfo/Awesome-FOFA/blob/529738afb527a50b18b885c19b2c769fb0394402/Storage/coldriver/coldriver7.png)

```
IP:
45.133.216.15:3000
95.164.17.94:3000
89.19.211.240:3000
```

By examining the time these three data points were discovered, it was found to be between August 2023 and October 2023, which coincides with the period Google Threat Analysis Group considered the organization active.

![8](https://github.com/FofaInfo/Awesome-FOFA/blob/529738afb527a50b18b885c19b2c769fb0394402/Storage/coldriver/coldriver8.png)

![9](https://github.com/FofaInfo/Awesome-FOFA/blob/529738afb527a50b18b885c19b2c769fb0394402/Storage/coldriver/coldriver9.png)

![10](https://github.com/FofaInfo/Awesome-FOFA/blob/529738afb527a50b18b885c19b2c769fb0394402/Storage/coldriver/coldriver10.png)

## Verification

Query to verify the above obtained 3 IPs. However, except for the IP 45.133.216.15:3000 disclosed by Google, we did not find relevant information or sample evidence for the other 2 IPs in the query results from VirusTotal, Qianxin, and Threatbook. Details are as follows:

![11](https://github.com/FofaInfo/Awesome-FOFA/blob/529738afb527a50b18b885c19b2c769fb0394402/Storage/coldriver/coldriver11.png)

![12](https://github.com/FofaInfo/Awesome-FOFA/blob/529738afb527a50b18b885c19b2c769fb0394402/Storage/coldriver/coldriver12.png)

![13](https://github.com/FofaInfo/Awesome-FOFA/blob/529738afb527a50b18b885c19b2c769fb0394402/Storage/coldriver/coldriver13.png)

## Conclusion

Using the FOFA platform to search for traces of the COLDRIVER organization, 2 data points very similar to the C2 publicly disclosed on January 18, 2024, were eventually discovered on the FOFA platform. The activity period is between August and October 2023. However, no unknown asset samples were found for the newly discovered suspicious IOCs, but we believe these two IPs are highly suspicious. The syntax for this expansion is as follows:

```
FOFA Syntax 1:
banner="\x15\x03\x03\x00\x02\x022" && port="3000" && cert="Internet Widgits Pty Ltd"
FOFA Syntax 2:
cert="Internet Widgits Pty Ltd" && cert="2023-06-23 15:59 UTC"
Final Syntax:
banner="\x15\x03\x03\x00\x02\x022" && port="3000" && cert="Internet Widgits Pty Ltd" && cert="2023-06-23 15:59 UTC"
```

## Reference

https://blog.google/threat-analysis-group/google-tag-coldriver-russian-phishing-malware/

## Appendix

```
Suspicious Addresses：
95.164.17.94:3000
89.19.211.240:3000
C2：
45.133.216.15:3000
HASH(SHA256)
0f6b9d2ada67cebc8c0f03786c442c61c05cef5b92641ec4c1bdd8f5baeb2ee1
A949ec428116489f5e77cefc67fea475017e0f50d2289e17c3eb053072adcf24
C97acea1a6ef59d58a498f1e1f0e0648d6979c4325de3ee726038df1fc2e831d
Ac270310b5410e7430fe7e36a079525cd8724b002b38e13a6ee6e09b326f4847
```
