# Autonomy agreement of Web3 (WTAA) protocol

Internet email allows people to communicate with each other from anywhere. The WTAA protocol enables people to send value to each other anywhere. Every user can make payments using an email-like address.

To implement the WTAA protocol, the gateway should provide a JSON file (wtaa.json) on its website. The file should be https protected. It should also have CORS headers. ("Access-Control-Allow-Origin": "*").

In the wtaa.json file, the gateway can list all necessary information such as assets and APIs.

``````````
  "domain": "gateway-domain.com",
  "logo": "https://gateway-domain.com/logo.png",
  "description": "The gateway-domain is a sample",
  "assets": [
    {
      "code": "USDT",
      "issuer": "rnzcChVKabxh3JLvh7qGanzqTCDW6fUSDT",
      "image": "https://gateway-domain.com/usdt-logo.png"
    },
    {
      "code": "ETH",
      "issuer": "rHJ6a42xxExCxyUJWQAKHdwarxVf6L9ETH",
      "image": "https://gateway-domain.com/usdt-logo.png"
    }
  ],
  "deposit": "https://gateway-domain.com/deposit",
  "wtaa": "https://gateway-domain.com/withdraw"
}
`````````````````
