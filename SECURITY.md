We hope that we'll be able to encourage security of the Po.et protocol and software through incentivized collaboration.

We value the input of researchers acting in good faith to help us maintain a high standard for the security and privacy for our users. This includes encouraging responsible vulnerability research and disclosure. Our policy sets out our definition of good faith in the context of finding and reporting vulnerabilities, as well as what you can expect from us in return.

## What is a responsible disclosure policy?
A responsible disclosure policy allows for researchers to collaborate with the Po.et core team to reveal potential vulnerabilities and give us a chance to fix the issue before a public release of the vulnerability. When vulnerabilities are submitted responsibly, it can encourage coordination to minimize the disruption of any services built using Po.et's software.

## What is covered under the responsible disclosures policy and bounty?
Only software directly developed by the Po.et team is covered under this disclosure policy.

Third-party packages/software/plugins or community-built software are not included. If we get a disclosure for outside software, we will not disclose the vulnerability to the third-party as to protect you from any undue legal issues and to ensure you're still eligible for any outstanding bounties that they provide.

Other examples of reports not covered under the responsible disclosures policy include:
  * Findings from physical testing such as office access (e.g. open doors, tailgating);
  * Findings derived primarily from social engineering (e.g. phishing, vishing);
  * Findings from applications or systems not listed in the 'Scope' section;
  * UI and UX bugs and spelling mistakes;
  * Network level Denial of Service (DoS/DDoS) vulnerabilities

## What software is considered to be 'in scope'?
  * https://github.com/poetapp/node
  * https://github.com/poetapp/frost-client
  * https://github.com/poetapp/frost-api
  * https://github.com/poetapp/explorer-web
  * https://github.com/poetapp/poet-js

## What can you expect from the Po.et team?
When working with us according to this policy, you can expect us to:

  * Extend Safe Harbor for your vulnerability research that is related to this policy
  * Work with you to understand and validate your report, including a timely initial response to the submission
  * Work to remediate discovered vulnerabilities in a timely manner
  * Recognize your contribution to improving our security if you are the first to report a unique vulnerability, and your report triggers a code or configuration change

## What about "safe harbor" protections?
When conducting vulnerability research according to this policy, we consider this research to be:

  * Authorized in accordance with the Computer Fraud and Abuse Act (CFAA) (and/or similar state laws), and we will not initiate or support legal action against you for accidental, good faith violations of this policy;
  * Exempt from the Digital Millennium Copyright Act (DMCA), and we will not bring a claim against you for circumvention of technology controls;
  * Exempt from restrictions in our Terms & Conditions that would interfere with conducting security research, and we waive those restrictions on a limited basis for work done under this policy; and
  * Lawful, helpful to the overall security of the Internet, and conducted in good faith.
  * You are expected, as always, to comply with all applicable laws.

If at any time you have concerns or are uncertain whether your security research is consistent with this policy, please submit a report through one of our official channels before going any further.

## How do I submit disclosures?
Please email **security@po.et** for all communications.  Do not use other channels such as Twitter, Telegram, or Reddit.

If you'd like to encrypt your submission, please use this PGP key:

```
-----BEGIN PGP PUBLIC KEY BLOCK-----

mQINBFyUks4BEACxzvk+VvmgNxL+lutGzAh/g8jy2Ef/xH2q/YCiL62MgM5oPD8Q
2K2du6k8R498O94ppa27Nq9pmc4WphtSFlEw8F19zl7bvvPhgX20P0kAbg6nAybD
sgafH7tpsNuqFV4ZwN/jdvIvA99I7SAotgmd+4i8Vd/8rb14Xce4jMfhkz4k55E3
1QymXqF/Jzz3eVrMSAsABSNrFzjA2I99AFYPvpw+PvV28ShHm0mc2T9/FDd3Iakg
+PWvzVzcH5Mf5q8fzny6Y1ezX9Cyc8F7NLSxPBw493KaCnWAqDQFvq3x76gNCl7/
MdsRLgrZdrERFbH6541Y1XZNtoC9yPuTDk3i0yWbVa14a3j6Lk/1fNt4tgM0ihcN
o19WkiK62E6+uP5ZRzCwmmWjTs9iSBXwjzP6B1Zu14zpXqyFKj74gaV37/A9AFHc
tTI8+ZcXQdoNZExAM0OEaXmmMURnMgU3DipMSMHMdi2RUVqhksQ0MyAw5G5qRHoq
QaKiSi7PnBSnqnV8HP+2dSQLzZux5+tZGgY1RDEgxgk0Dik/3ssXYHv4zjzhUIyd
4ZzNK8qVVoAXANnUpcxHVuSsAMBOM9EvfNMZu2v4vwJTSC9WwAIga8hPmj4kVKCG
FRQKMM4YU+euVS5/TlIzrIUkdzDusdFLZcM0p2diovHdPOEIvvvfQP2F8QARAQAB
tDVQb2V0IE5ldHdvcmsgKFBvZXQgTmV0d29yayBTZWN1cml0eSkgPHNlY3VyaXR5
QHBvLmV0PokCTgQTAQoAOBYhBHJhaM2b8Zgduun6tqNUEncfXDFpBQJclJLOAhsD
BQsJCAcCBhUKCQgLAgQWAgMBAh4BAheAAAoJEKNUEncfXDFpGOkP/0tuyL+us3WA
zRMV7JRizXhZgra8YBRUuX4ujiecidspAXIcZSZnLonLXDoNCJtdnCKkSOpDx1Kn
upZxaMW0EFqlUkCDXdUq3mL+5Lxsc0bAVzUvxTtGBF5SpGHxNuLvvDh6X4PlrmxM
FXoKLVfq/p3uf/yms6Z5ETI03eHF0zq/2erPjq4yCn/T9tuTARDz/lkdJJ3/Cd9f
0vLjj2BCzcd1mDLEpRuUXES2SvlSRE5EAlafjJAQXvejWMI7IbugaoiUChcOxQRE
EPJ0PusISP9L9/s/koS9JICBTpW2RQTB53O55e0TpPiegcKCU6XxRWqr7JqT/72Q
4QDcuH2JyQ6aheXMIYrbZPDKtFoM4LkJQZFP13uzCs3bOqEgodsPNkN+rqORC6p6
rQ9HXSDKsyRbDyrHJrz0ADAvOErnE8vVY8H6RQWjCIx2Cv9F0q+8iMsw5HBOcJmx
ptF8nz3MXTofVqyBMTtlrqu1FeGmjbScBqKKz9pC91sd/9atcFjlr5taVFfQ/S31
TfvTG34V+eEZj1exFOOI+jFAvFjAOLXS1+e1f24YT7YVFrwKn+r6X1Y669gcqoyQ
QOty8MaqEcthiiYzj9aTHhMr1Sl+0x9WLvHzuongXfpehl195tiNnPEw8iDFd53Q
/16QikFovurVsxhkSOski5qAimhLqV3q
=TSe+
-----END PGP PUBLIC KEY BLOCK-----
```

## What about the bug bounty program?
We'll be kicking off our bounty program to make sure we're rewarding valid research work that adheres to our responsible disclosure policy on April 15, 2019.
Rewards will be based along the OWASP Risk Rating Methodology which allows us to estimate the associated risk of disclosed vulnerabilities to Po.et. Using this rating system will help to ensure that we're properly prioritizing any modifications that might need to be done and gives researchers a pretty well-known, simple system to reference. The basic way to describe the OWASP ratings is "Risk = Likelihood * Impact".  For more information, visit [https://www.owasp.org/index.php/OWASP_Risk_Rating_Methodology](https://www.owasp.org/index.php/OWASP_Risk_Rating_Methodology)

## What type of compensation is being given for the bounties?
All bounties will be paid out in POE according to where the fall into our risk rating scale; bounties are listed in USD for sake of clarity here.

* Note: Up to $50
* Low: Up to $100
* Medium: Up to $500
* High: Up to $1,500
* Critical: Up to $3,000

Every submission will also be evaluated for quality as a factor for compensation. High quality submissions need to include reproduction steps, failing test cases, and well-written fixes. Low quality submissions may not be rewarded at all, especially if they are too vague or don't contain steps for reproduction.

Only one bounty will be awarded per vulnerability. We'll favor the first person to submit a complete report.

This is a private program and all bounties are subject to the discretion of the Po.et Foundation.

## What eligibility requirements are there for collecting on any bounties?
We require that all researchers:
* Make every effort to avoid privacy violations, degradation of user experience, disruption to production systems, and destruction of data during security testing
* Perform research only within the scope set out below
* Use the identified communication channels to report vulnerability information to us
* Keep information about any vulnerabilities you've discovered confidential between yourself and Po.et until we've had 90 days to resolve the issue
* Perform testing only on in-scope systems, and respect systems and activities which are out-of-scope
* Submissions cannot be submitted through a broker or third-party
* Must be the first person to submit a complete report
* Must adhere to the responsible disclosures policy

Also, you must:
* Not be employed by any officially-related company of the Po.et organizations or Po.et Development Labs
* Not reside in any country which the United States has issued sanctions against (e.g. Cuba, Iran, North Korea, Sudan, and Syria)
* Not be in violation of any national, state, or local law or regulation with any activities related (directly or indirectly) to the responsible disclosures policy.
* Not attempt to extort us or demand ransom.

If you fail to act in good faith of these guidelines, or if we believe you have violated these guidelines, you will become ineligible for any bounties. If you feel like you are potentially breaking the rules, contact us through one of the official security related communication channels and let us work with you to make sure everything that you're doing is acceptable.

## Acknowledgements
Some of the language we’ve used for our policies came from [disclose.io](http://disclose.io), a collaborative and vendor-agnostic project to standardize best practices around safe harbor for good-faith security research.  We’ve also used language from [Snyk](https://snyk.io/docs/security/#snyk-s-vulnerability-disclosure-program) in prior disclosure documents.

We hope that you’re excited about our approaches to collaborative security and look forward to any vulnerabilities that you may find! 🙇

