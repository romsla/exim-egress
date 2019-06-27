# Docker Exim Egress Relay

For use in Kubernetes and other docker orchestration environments as an egress email relay with governance options.

  - Lightweight exim 4.92 based on alpine 3.10
  - Basic exim smtp relay
  - Options for governance of egress messages

# Governance options

  - Sender and Receiver header whitelisting using wildcards and lists
  - Reversed SPF lookups for egress, as opposite to common SPF lookups during ingress. Insures egress relay is not even accepting SPF-restricted content for relay.
  - Template regex validation for message content and subject header
  - Rate and size limiting

### Options

Filtering occurs only after configuration variables are defined.
Empty configuration defines an open relay for LAN subnets for messages of size 1 megabytes or less.

| Plugin | DEFAULT | Info | 
| ------ | ------ | ------ |
| SENDER_MASK | * | The sender email address list used to whitelist incoming mail. Filter syntax: *@example.com to filter domain, or user@example.com to filter user. Accepts wildcards. List syntax is colon separated: "item1:item2". |
| RELAY_TO_USERS | Empty | The list of allowed users (email addresses) the relay is whitelisting it's egress to. Filter syntax: *@example.com to filter domain, or user@example.com to filter user. Accepts wildcards. List syntax is colon separated: "item1:item2".|
| RELAY_TO_DOMAINS | * | A simplier form of whitelist than RELAY_TO_USERS to filter only domains. Defines the list of domains the relay itself is allowed to egress to. Accepts wildcards. List syntax is colon separated: "item1:item2". |
| RELAY_SPF_REGEX_DOMAIN | Empty | Regex for sender TXT and\or SPF checking. Will filter message if the specified TXT record does not exist for the sender domain of incoming mail message. Suggested regex - see below*.  |
| RELAY_MSGBODY_REGEX | Empty | Regex or single non-unicode** keyword for message body checking. |
| RELAY_MSGSUBJ_REGEX | Empty | Regex or single non-unicode** keyword for message subject checking. |
| RATE_INTERVAL | Empty | Must be used together with RATE_VOLUME to work. Time interval for rate limiting - 1h for 1 hour, or 15m for 15 minutes, 1d for 1 day, etc. ***|
| RATE_VOLUME | Empty | Must be used together with RATE_INTERVAL to work. Integer number, example: 50. ***|
| SIZE_LIMIT | 1M | Size limit for messages. Default is 1 megabyte. |
| SMARTHOST | Empty | Smarthost (another egress relay) for sending mail. Default - deliver directly. |
| SMARTHOSTPORT | 25 | Smarthost (another egress relay) port for sending mail.|
| SMTP_PASSWORD | Empty | Smarthost password |
| SMTP_USERNAME | Empty | Smarthost login |
| SMTP_USERDOMAIN | Empty | Smarthost NTLM Domain (for NTLM) |
| LOCAL_DOMAINS | Empty | Domains to intercept mail and deliver locally to relay. Should not be used as it defeats the relay purpose. Is intented for forensics. Accepts wildcards. List syntax: "item1 : item2". |
| RELAY_FROM_HOSTS | All LAN Subnets | Subnets that are allowed to use this relay. Default -(192, 10, 172...). Accepts wildcards. List syntax: "item1 : item2". |

*Suggested regex to filter TXT records with "include:example.com" :
````
(^|\n)v=spf1(\s+|\s+.*\s+)include:example\.com(\s|$|\n)
````

**To make unicode work you can either encode into unicode manually, or just send yourself a sample message and view it's source.

***The rate limiter counts any attempts to send a message, not just related messages. It does so even when the limit is reached and blocking is already being enforced upon incoming email traffic. This is to completely block off any spam infected containers (pods) and to prevent any other misuse. You should probably have centralized logging configured in your environment, as well as network policies before enabling this feature.

License
----
Apache 2.0
Based in part on code from https://github.com/industrieco/docker-exim-relay and modified to make governance features work

Contact me
----
Email (as a pic): ![N|Solid](http://safemail.justlikeed.net/e/a5d0a48f0f70ab6407fa332215ba472c.png)

Donate
----

[![paypal](https://www.paypalobjects.com/en_US/i/btn/btn_donateCC_LG.gif)](https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=6DKTXXTER5C5S)