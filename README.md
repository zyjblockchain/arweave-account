# Arweave Account

This npm package is paired with the permadapp [Account](https://github.com/MetaweaveTeam/Account).

Arweave accounts rely on the native gateways operations to write and retrieve users data.

# Development

> ℹ️ If it's your first time trying `arweave-account`, __we recommend to try the [interactive documentation](https://account.metaweave.xyz)__.

If you're in for a deeper look at arweave account, you are at the right place

- [Development](#development)
  - [Installation & Import](#installation--import)
  - [Basic usages](#basic-usages)
  - [Creation/update flow](#creationupdate-flow)
  - [Advanced usages](#advanced-usages)
  - [References](#references)
    - [Typescript imports](#typescript-imports)
    - [ArAccount object](#araccount-object)
    - [ArProfile object](#arprofile-object)
    - [`avatar` and `banner` properties](#avatar-and-banner-properties)
- [Architecture](#architecture)
- [Migrate from 1.2.5 to 1.3.x](#migrate-from-125-to-13x)

## Installation & Import

__Installation__
```
npm install arweave-account
```

__Usage__
```
import Account from 'arweave-account'

const account = new Account();
```

If you are using Typescript, see [_typescript imports_](#typescript-imports) section.

## Basic usages

__Get user profile by wallet address__
```
await account.get(walletAddr);
```

__Search user profile by handle name__
```
await account.search(handle);
```

__Find user profile by wallet address & handle name__
```
await account.find(handle#uniqID);
```

## Creation/update flow

With the 3 methods above, you are able to display any arweave account without requiring your user to even have an Arweave wallet. A [permanent application](https://github.com/MetaweaveTeam/Account) is available for your users to create and update their arweave account profile. Further more, we have made available a [redirection link](https://account.metaweave.xyz) that will bring your users to the latest deployed version of the permadapp. 

> ℹ️ [What is a permanent application?](https://www.youtube.com/watch?v=ZduvXKxSgkQ)

Feel free to make your own redirection link with your own branding or create an issue to enhance the onboarding flow so everyone can benefit from it.

Although, if you want to implement your own onboarding flow right inside your app, the `arweave-account`, see the following _Advanced usages_ section.

## Advanced usages

__Connect the user wallet__
```
await account.connect(jwk?);
```
If no argument is passed, account will use the injected `arweaveWallet` object. In that case, make sure you have handled the wallet connection flow with the right permissions before calling this method to avoid any unexpected behavior. 

__Create/update arweave account profile__
```
await account.updateProfile(profileObj);
```
Make sure `connect()` is called before.

### Note

The advanced usages are only compatible with wallets injecting the `arweaveWallet` object with [`dispatch()`](https://github.com/th8ta/ArConnect#dispatchtransaction-promisedispatchresult).

> ℹ️ If you wish having more wallets compatible, feel free to open a discussion here (issue) or DM me on discord: cromatikap#6039

## References

### Typescript imports

```
import { ArAccount, ArProfile } from 'arweave-account';
```

### ArAccount object

When getting account information, the library request the relevant transaction through the gateway, decode the information stored and return an account object formatted the following way:

| Property name | req. | description |
| ------------- | ---  | ------------- |
| `txid`        | yes  | Current transaction associated with the arweave account |
| `addr`        | yes  | Wallet address of the arweave account |
| `handle`      | yes  | Unique handle derived from `profile.handleName` and the wallet address |
| `profile`     | yes  | Profile object |
| `apps`        | no   | WIP from [this discussion](https://github.com/MetaweaveTeam/Account/issues/1): maybe named `storage`?  |

### ArProfile object

| Property name | req. | description |
| ------------- | ---  | ------------- |
| `handleName`  | yes  | The handle name chosen by the user, this is a required constituent of the generated account unique handle |
| `name`        | no   | A secondary name |
| `bio`         | no   | Biography information |
| `avatar`      | no   | picture URI of the user avatar [supporting multiple protocols](#avatar-and-banner-properties) |
| `avatarURL`   | no   | Out of the box URL picture of the user avatar |
| `banner`      | no   | picture URI of the user banner [supporting multiple protocols](#avatar-and-banner-properties) |
| `bannerURL`   | no   | Out of the box URL picture of the user banner |
| `links`       | yes  | user social links |
| `wallets`     | yes  | user wallets from other blockchains |

## `avatar` and `banner` properties

Image properties such as `avatar` and `banner` can refer to an NFT from different chains. The following protocols are supported:

- http://
- https://
- ar://

> ℹ️ If you wish to add other protocol, we would be happy to do so. Please feel free to reach out! 

> ❣️ Also, the Metaweave team gives grants for merged Pull Requests.

For each image property, an `https` URL is generated according to the original URI provided. You can access it with the suffix `URL`.

For example:

| Property name       | value                 |
| ------------------  | --------------------------------------------------------------- |
| `profile.avatar`    | ar://xqjVvn9b8hmtDJhfVw80OZzAsn-ErpWbaFCPZWG5vKI                |
| `profile.avatarURL` | https://arweave.net/xqjVvn9b8hmtDJhfVw80OZzAsn-ErpWbaFCPZWG5vKI |

# Architecture

In the arweave account protocol, a set of data representing the user profile is encoded to be stored in a transaction and decoded to a `ArAccount` type object.

Here is an exemple of an encoded and decoded account dataset for the wallet `aIUmY9Iy4qoW3HOikTy6aJww-mM4Y-CUJ7mXoPdzdog`:

## Encoded arweave account data (written on-chain)

```
{
  "handle":"cromatikap",
  "avatar": "ar://xqjVvn9b8hmtDJhfVw80OZzAsn-ErpWbaFCPZWG5vKI",
  "banner": "ar://a0ieiziq2JkYhWamlrUCHxrGYnHWUAMcONxRmfkWt-k",
  "name":"Axel",
  "bio":"Founder of Metaweave.xyz\nI love dogs",
  "links": {
    "twitter":"cromatikap",
    "github":"cromatikap",
    "instagram":"cromatikap",
    "discord":"cromatikap#6039",
    "linkedin": "",
    "facebook": "",
    "youtube": "",
    "twitch": ""
  },
  "wallets": {
    eth: "0xeEEe8f7922E99ce6CEd5Cb2DaEdA5FE80Df7C95e"
  }
}
```

## Decoded arweave account data

Here is the dataset you get by using the library

```
{
  "txid": "NPJJoq-9EwUeAce_bSbSyqICaGs4_7Hg6VxCyoCY8UQ",
  "addr": "aIUmY9Iy4qoW3HOikTy6aJww-mM4Y-CUJ7mXoPdzdog",
  "handle": "cromatikap#aIUdog",
  "profile": {
    "avatar": "ar://xqjVvn9b8hmtDJhfVw80OZzAsn-ErpWbaFCPZWG5vKI",
    "avatarURL": "https://arweave.net/xqjVvn9b8hmtDJhfVw80OZzAsn-ErpWbaFCPZWG5vKI",
    "banner": "ar://a0ieiziq2JkYhWamlrUCHxrGYnHWUAMcONxRmfkWt-k",
    "bannerURL": "https://arweave.net/a0ieiziq2JkYhWamlrUCHxrGYnHWUAMcONxRmfkWt-k",
    "handleName": "cromatikap",
    "name": "Axel",
    "bio": "Founder of Metaweave.xyz\nI love dogs",
    "links": {
      "twitter": "cromatikap",
      "github": "cromatikap",
      "instagram": "cromatikap",
      "discord": "cromatikap#6039",
      "linkedin": "",
      "facebook": "",
      "youtube": "",
      "twitch": ""
    },
    "wallets": {
      eth: "0xeEEe8f7922E99ce6CEd5Cb2DaEdA5FE80Df7C95e"
    }
  }
}
```

For convenience, the related `txid` and wallet `addr` is added. As well, you have access to the full unique `handle` derived from the user chosen handle and their wallet address

# Migrate from 1.2.5 to 1.3.x

1. The unique handle (name#xxxxxx) is at the Account level

`account.profile.handle` -> `account.handle`

The unique handle derived from the user chosen handle name and their wallet address is now accessible at the `account` level.

In the `account.profile` object, a new property `handleName` is available which constitutes the name part only of the user handle.

2. URL ressources comes auto generated

You no longer need to craft an URL from the `avatar` property. The library now delivers an URL ready to use in any standard <img /> tag
