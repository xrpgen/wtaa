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

The gateway usually provides deposit and withdrawal services, so two APIs should be provided in the file: deposit and wtaa. In addition to being used for withdrawals, WTAA can also provide address mapping functions and the ability to interact with other blockchains or smart contracts.

## Deposit:

The client should send a GET request with below parameters. 

| Name | Type | Description |
|:-------------------------|:-------------------------:|:-------------------------|
| currency | string | The code of the asset, like "USDT", "Ripple" etc. |
| address | string | The address of the wallet. |
| network | string | Must be "xrpgen" or "xag". |
| lang | string | *Optional* The language, like "en", "cn" etc. |

`````````````````
https://xagfans.com/deposit?currency=Ripple&network=xrpgen&lang=en&address=YOUR_XAG_ADDRESS
`````````````````

### Response:

The response is a deposit address with some extra fields.

| Name | Type | Description |
|:-------------------------|:-------------------------:|:-------------------------|
| network | string | The blockchain network like "Ripple", "Ethereum" etc. |
| address | string | The deposit address on the network above. |
| memo | string | *Optional* Some blockchain may require you to provide a memo or a tag. |
| expires | number | *Optional* The address will expire after the specified number of seconds. |
| extra_info | array of string | *Optional* . Some additional information users need to know. |
| history | array of object | *Optional* . Deposit records for users to view. |


`````````````````
{
  "network": "Ripple",
  "address": "rJi335PArxMtb2D8P3s7CnizSemSgRZMvQ",
  "memo": "Tag: 220315",
  "expires": 0,
  "extra_info": [
    "The address is ONLY available for XRP deposit.",
    "Your payment must have a TAG.",
    "Minimum amount per deposit: 10 XRP. Assets less than min. amount will NOT be credited.",
    "Any problem please visit https://xagfans.com."
  ],
  "history": []
}
`````````````````

### Error:

The server returns an error object when an error occurs.

| Name | Type | Description |
|:-------------------------|:-------------------------:|:-------------------------|
| error | string | Error code. |
| address | string | Error details. |

`````````````````
{
  "error": "NoTrust",
  "error_message": "Please add a trustline first."
}
`````````````````

