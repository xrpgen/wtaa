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

## Deposit API:

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

## WTAA API

The WTAA protocol could resolve email-like addresses such as name@yourdomain.com. It allows the client create a dynamic interface for user to transfer their assets.

The client will send `wtaa` and `quote` requests to the API server.

### Step 1: wtaa request

The client sends a GET request with below parameters. For example, when user input *"Ripple@xagfans.com"*.

| Name | Type | Description |
|:-------------------------|:-------------------------:|:-------------------------|
| type | string | Must be "wtaa". |
| domain | string | The string after "@". |
| destination | string | The string before "@". |
| network | string | Must be "xrpgen" or "xag". |
| address | string | *Optional* The address of the user. |
| client | string | *Optional* The client and version information. |
| lang | string | *Optional* Language of the client. |

`````````````````
https://xagfans.com/withdraw?address=YOUR_XAG_ADDRESS&client=xagtrade-1.4.0&destination=Ripple&domain=xagfans.com&lang=en&network=xag&type=wtaa
`````````````````

The response could be a xag wallet (address mapping) or some extra fields (dynamic interface). If it is a address, the server returns an object with the following structure:

| Name | Type | Description |
|:-------------------------|:-------------------------:|:-------------------------|
| result | string | Must be "success". |
| data | object | Object contains "destination_address" |
| *data*.type | string | Must be "wtaa_record". |
| *data*.destination_address | string | A xag address. |
| request | object | *Optional* The request parameters. |

``````````
{
  "result": "success",
  "data": {
    "type": "wtaa_record",
    "destination_address": "rMeL8gHJifANAfVchSDkTUmUWjHMvCeXrp"
  },
  "request": {
    "address": "r3kmLJN5D28dHuH8vZNUZpMC43pEHpaocV",
    "client": "xagtrade-1.4.0",
    "destination": "Ripple",
    "domain": "xagfans.com",
    "lang": "en",
    "network": "xag",
    "type": "wtaa"
  }
}
``````````

The client would consider the email-like address as a alias of the destination_address account. Step 2 is not necessary in this case.

The server could also returns the extra_fields with the following structure:

| Name | Type | Description |
|:-------------------------|:-------------------------:|:-------------------------|
| result | string | Must be "success". |
| data | object | Object contains extra fields. |
| *data*.type | string | Must be "federation_record". |
| *data*.destination | string | The value which will be sent in quote step. |
| *data*.domain | string | The value which will be sent in quote step. |
| *data*.quote_url | string | The quote url. |
| *data*.currencies | array | The assets accepted by the destination. Each asset have currency and issuer field. |
| *data*.extra_fields | array | An array contains the components (text, select, label) which could be used to create a dynamic form. |
| request | object | *Optional* The request parameters. |

``````````
{
  "result": "success",
  "data": {
    "type": "wtaa_record",
    "destination": "Ripple",
    "domain": "xagfans.com",
    "quote_url": "https://xagfans.com/withdraw",
    "currencies": [
      {
        "currency": "Ripple",
        "issuer": "rMeL8gHJifANAfVchSDkTUmUWjHMvCeXrp"
      }
    ],
    "extra_fields": [
      {
        "type": "label",
        "label": "Withdrawal XRP through Ripple network",
        "hint": "Fees 2 XRP"
      },
      {
        "type": "text",
        "name": "rippleAddress",
        "label": "Ripple Address",
        "required": true
      },
      {
        "type": "text",
        "name": "tag",
        "label": "Tag",
        "hint": "Please confirm if a Tag is required by the recipient.",
        "required": false
      }
    ]
  },
  "request": {
    "address": "r3kmLJN5D28dHuH8vZNUZpMC43pEHpaocV",
    "client": "xagtrade-1.4.0",
    "destination": "Ripple",
    "domain": "xagfans.com",
    "lang": "en",
    "network": "xag",
    "type": "wtaa"
  }
}
``````````

Server could return an error object with below structure:

| Name | Type | Description |
|:-------------------------|:-------------------------:|:-------------------------|
| error | string | Error code |
| error_message | string | Error description |
| request | object | *Optional* The request parameters. |

```````````````
{
  "error": "noSuchUser",
  "error_message": "The supplied user was not found."
}
```````````````

### Step 2: quote request

After user fill the form generated with the extra_fields in step 1, a quote GET request should be fired to `quote_url`. 

| Name | Type | Description |
|:-------------------------|:-------------------------:|:-------------------------|
| type | string | Must be "quote". |
| domain | string | The string domain got in step 1. |
| destination | string | The string destination got in step 1. |
| amount | string | The amount user would send. Using "/" to seperate the currency, like "100/USDT".  |
| address | string | The address of the user. |
| network | string | Must be "xrpgen" or "xag". |
| *extras* | string | The dynamic fields user provided. |
| client | string | *Optional* The client and version information. |
| lang | string | *Optional* Language of the client. |

```````````````
https://xagfans.com/withdraw?address=r3kmLJN5D28dHuH8vZNUZpMC43pEHpaocV&amount=100%2FUSDT&client=xagtrade-1.4.0&destination=USDT&domain=xagfans.com&lang=cn&network=xag&tronAddress=TDTFpXBcmScEdTvvUB8z5kG5q3nTqgLU39&type=quote
```````````````

The server will calculate the amount user need to send when get the quote requests. The return value has the following structure:

| Name | Type | Description |
|:-------------------------|:-------------------------:|:-------------------------|
| result | string | Must be "success". |
| timestamp | timestamp | When the quote would be expired. |
| quote | object | Object contains quote information. |
| *quote*.type | string | Must be "quote". |
| *quote*.destination | string | Destination value. |
| *quote*.domain | string | Domain |
| *quote*.address | string | *Same as destination_address* |
| *quote*.source | string | The source account used in payment. |
| *quote*.destination_address | string | The destination xag wallet used in payment. |
| *quote*.destination_tag | UInt | *Optional* The tag that can be used to identify a particular payment. |
| *quote*.invoice_id | string | *Optional* A 256-bit hash that can be used to identify a particular payment. |
| *quote*.memos | array | *Optional* Array of memos to attach to the transaction. |
| *quote*.send | array | Array of amounts. The calculated amounts need to send. |
| request | object | *Optional* The request parameters. |

```````````````
{
  "result": "success",
  "quote": {
    "type": "quote",
    "destination": "USDT",
    "domain": "xagfans.com",
    "amount": "100/USDT",
    "source": "r4gZMECmikvdPqMYC7fnzjbEwitUknAuvq",
    "destination_address": "r3ccPsMqBetGFSQGdHTPTjrcBZREppE4Nq",
    "address": "r3ccPsMqBetGFSQGdHTPTjrcBZREppE4Nq",
    "destination_tag": 87654321,
    "invoice_id": "00000000000000000000000000000000000000000000000359720A1587270684",
    "memos": [
      {
        "data": "tron",
        "type": "network",
        "format": "text"
      },
      {
        "data": "TDTFpXBcmScEdTvvUB8z5kG5q3nTqgLU39",
        "type": "address",
        "format": "text"
      }
    ],
    "send": [
      {
        "value": "102",
        "issuer": "rnzcChVKabxh3JLvh7qGanzqTCDW6fUSDT",
        "currency": "USDT"
      }
    ],
    "expires": 1653404761
  },
  "request": {
    "address": "r4gZMECmikvdPqMYC7fnzjbEwitUknAuvq",
    "amount": "100/USDT",
    "client": "xagtrade-1.4.0",
    "destination": "USDT",
    "domain": "xagfans.com",
    "lang": "cn",
    "network": "xag",
    "tronAddress": "TDTFpXBcmScEdTvvUB8z5kG5q3nTqgLU39",
    "type": "quote"
  },
  "timestamp": 1653397561
}
```````````````

Server could return an error object with below structure:

| Name | Type | Description |
|:-------------------------|:-------------------------:|:-------------------------|
| error | string | Error code |
| error_message | string | Error description |
| request | object | *Optional* The request parameters. |

```````````````
{
  "error": "InvalidAmount",
  "error_message": "Send at least 1 USDT."
}
```````````````
