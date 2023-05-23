# How to effectively eliminate interference through certificates in the asset discovery #


## Background and Pain Points ##

One of the main tasks for FOFA engineers is conducting attack surface review, also known as exposure management. The core requirement is to be as comprehensive and accurate as possible. One effective method is to use the `cert.is_valid` syntax to perform combined queries and quickly verify if an asset's certificate is issued by a trusted certificate authority. For example, using the syntax `cert="fofa.info" && cert.is_valid=true`.

`cert.is_valid` is used to determine the validity of a certificate. However, during the process of reviewing the attack surface, we often encounter distractions. Many assets are not part of the organization's assets but are intentionally associated with the DNS records of major companies. For example, after a DDoS attack, some websites deliberately bind their DNS to "baidu.com" to redirect the attack traffic to a large internet company, as shown in the following image:

[![p9T0bNT.md.png](https://s1.ax1x.com/2023/05/23/p9T0bNT.md.png)](https://imgse.com/i/p9T0bNT)

In such cases, we find that those domain names intentionally bound to the IP addresses of large companies are not the valid attack surface we need to consider. Therefore, it is necessary to handle these distractions.

## Proposed Solution ##

The most straightforward approach is to simulate the behavior of a browser and check if the domain name matches the information in the certificate. However, this approach immediately raises another question: **If a certificate is valid but does not match the domain name, should we consider the asset as part of the target exposure or as interference data?**

Let's consider the following example:

[![p9TBGvj.md.png](https://s1.ax1x.com/2023/05/23/p9TBGvj.md.png)](https://imgse.com/i/p9TBGvj)

In the search results above, we know that all the listed IPs belong to a certain bank because binding a certificate requires possession of the certificate's private key. When opened, the browser displays an invalid certificate warning. If we rely solely on the matching of the certificate, we would discard this domain name. However, it is evident to the human eye that this is actually part of our target exposure.

Such cases are widespread. We speculate that the operations team may have placed a valid certificate on a server with an internal business domain name that is not registered in the certificate. It is possible that the operations team plans to update the certificate in the future but has not done so yet, or they consider the certificate trustworthy and see no need to update it. After all, it can still be used, and it is better than a self-signed certificate.

Based on the aforementioned example, we realized that a simple approach like adding a "certificate trust" switch is insufficient. Instead, we need to consider the requirements of comprehensiveness and accuracy. Therefore, we have conducted research and added the `cert.is_match` syntax, which also handles the evaluation of certificate expiration.

Let's summarize the previously used syntax and the two new additions:

- `cert.is_valid`: Evaluates the trustworthiness of the certificate (true for trusted, false for untrusted or self-signed)
- `cert.is_match`: Checks if the certificate matches the domain name of the asset (true for a match, false for a mismatch)
- `cert.is_expired`: Determines if the certificate has expired (true for expired, false for not expired)

Returning to the example mentioned earlier, using the syntax `cert="fofa.info" && cert.is_valid=true && cert.is_match=true` allows

 us to exclude distracting assets.

[![p9oNYy6.md.png](https://s1.ax1x.com/2023/05/22/p9oNYy6.md.png)](https://imgse.com/i/p9oNYy6)

Furthermore, in cases where the trustworthiness is true but the match is false, indicating that the asset still belongs to itself, as mentioned in the previous text, we can directly retrieve it using the syntax `cert="abchina.com.cn" && cert.is_valid=true && cert.is_match=false`.

[![p9TuMNV.md.png](https://s1.ax1x.com/2023/05/23/p9TuMNV.md.png)](https://imgse.com/i/p9TuMNV)

## Conclusion ##

The newly added certificate validation syntax has played a significant role in the asset collection process, including its application in attack surface review within the FOFA Open Lab.

This syntax allows for single or multiple combinations, providing greater flexibility and options. We encourage everyone to explore and discover more possibilities with this syntax.

[![p9oNFij.md.png](https://s1.ax1x.com/2023/05/22/p9oNFij.md.png)](https://imgse.com/i/p9oNFij)

Based on these considerations, we have introduced two new certificate filtering syntaxes: `cert.is_match` and `cert.is_expired`.

To clarify, let's summarize the existing and newly added certificate syntax together:

- `cert.is_valid`: Determines if the certificate is trusted (**true** for trusted, **false** for untrusted).
- `cert.is_match`: Checks if the certificate matches the asset (**true** for a match, **false** for a mismatch).
- `cert.is_expired`: Determines if the certificate has expired (**true** for expired, **false** for not expired).

Returning to the example case, the syntax `cert="fofa.info" && cert.is_valid=true && cert.is_match=true` can effectively exclude irrelevant assets.

[![p9oNYy6.md.png](https://s1.ax1x.com/2023/05/22/p9oNYy6.md.png)](https://imgse.com/i/p9oNYy6)

The newly introduced certificate verification syntax has proven to be highly useful in the process of asset collection, including the attack surface enumeration conducted in the FOFA Open Lab. We encourage developers to explore and discover new possibilities using this syntax in https://en.fofa.info




