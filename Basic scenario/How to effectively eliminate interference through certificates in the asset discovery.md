# How to effectively eliminate interference through certificates in the asset discovery #

## Issues ##

FOFA engineers utilize the syntax `cert.is_valid` to quickly verify the validity of certificates during asset review processes, ensuring that the certificates are issued by trusted certificate authorities and assessing the credibility of the assets.

However, during the asset review process, we often encounter a specific scenario even when using the syntax `cert="fofa.info" && cert.is_valid=true`. We check the trustworthiness of the certificates but still come across assets that do not belong to FOFA. 

This issue arises due to certificate leaks or others pointing their domain names to our IP address.



[![p92vKQP.png](https://s1.ax1x.com/2023/05/16/p92vKQP.png)](https://imgse.com/i/p92vKQP)

Another common situation is when a certificate is trusted but has expired. During asset classification, it is still necessary to filter and evaluate such cases.

Therefore, our objective is to address these issues and avoid including irrelevant assets when collecting assets based on certificates.

 
## Proposed Solution ##

We have identified two main problems:

- Determining if a trusted certificate has expired.
- Ensuring that a trusted certificate matches the asset.

The first problem can be easily solved by checking the expiration field of the certificate.

Because whether the certificate expires is an asset compliance judgment.

[![p92vnzt.png](https://s1.ax1x.com/2023/05/16/p92vnzt.png)](https://imgse.com/i/p92vnzt)

To address the second problem, where a trusted certificate does not match the domain stated in the certificate, we can use this discrepancy to filter out irrelevant data. 

However, this approach presents additional challenges, such as handling multiple domains under the same certificate and different criteria for judging certificates based on protocol and website data.

During our exploration, we came across an interesting observation. **When accessing a website using HTTPS, the browser checks whether the accessed host matches the one listed in the certificate. If there is a mismatch, the browser displays the certificate as untrusted.**

However, most HTTPS websites' certificates only contain the domain name. If you directly access the website using its IP address, a certificate error will be returned. Nevertheless, in the asset review process, this information is necessary for the assets themselves. **Therefore, solely relying on host-certificate matching, similar to what browsers do, is insufficient. We also need to consider cases where the certificate is bound to an IP address.
**
[![p9oNFij.md.png](https://s1.ax1x.com/2023/05/22/p9oNFij.md.png)](https://imgse.com/i/p9oNFij)

Based on these considerations, we have introduced two new certificate filtering syntaxes: `cert.is_match` and `cert.is_expired`.

To clarify, let's summarize the existing and newly added certificate syntax together:

- `cert.is_valid`: Determines if the certificate is trusted (**true** for trusted, **false** for untrusted).
- `cert.is_match`: Checks if the certificate matches the asset (**true** for a match, **false** for a mismatch).
- `cert.is_expired`: Determines if the certificate has expired (**true** for expired, **false** for not expired).

Returning to the example case, the syntax `cert="fofa.info" && cert.is_valid=true && cert.is_match=true` can effectively exclude irrelevant assets.

[![p9oNYy6.md.png](https://s1.ax1x.com/2023/05/22/p9oNYy6.md.png)](https://imgse.com/i/p9oNYy6)

The newly introduced certificate verification syntax has proven to be highly useful in the process of asset collection, including the attack surface enumeration conducted in the FOFA Open Lab. We encourage developers to explore and discover new possibilities using this syntax in https://en.fofa.info




