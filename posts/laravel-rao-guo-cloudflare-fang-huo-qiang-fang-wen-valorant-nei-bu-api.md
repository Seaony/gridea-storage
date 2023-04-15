---
title: '🪴 绕过 Cloudflare 访问 Valorant Api'
date: 2023-04-07 02:46:08
tags: [SSL,Laravel,GuzzleHttp,Proxy,Curl]
published: true
hideInList: false
feature: /post-images/laravel-rao-guo-cloudflare-fang-huo-qiang-fang-wen-valorant-nei-bu-api.png
isTop: false
---

> 本文章所实现内容仅做学习分享所用，本人不承担任何责任，请勿将相关信息用于非法用途。

# 🫠 准备工作

首先，安装 [GuzzleHttp](https://github.com/guzzle/guzzle) 网络请求 Package。

```shell
composer require guzzlehttp/guzzle
```

---

# 💭 绕过 Cloudflare

Valorant Api 受到 Cloudflare 防护，以及 Riot 也加了一层验证。

绕过 Cloudflare 的核心原理是使用 `Ciphers` 伪造 SSL，具体请求方式如下：

```php
 /**
* 用于绕过 Riot 防护的请求头
*
* @var string[] 请求头
*/
$headers = [
    'Content-Type'          => 'application/json',
    'User-Agent'            => 'RiotClient/63.0.5.4887690.4789131 rso-auth (Windows; 10;;Professional, x64)',
    'X-Riot-ClientPlatform' => 'ew0KCSJwbGF0Zm9ybVR5cGUiOiAiUEMiLA0KCSJwbGF0Zm9ybU9TIjogIldpbmRvd3MiLA0KCSJwbGF0Zm9ybU9TVmVyc2lvbiI6ICIxMC4wLjE5MDQyLjEuMjU2LjY0Yml0IiwNCgkicGxhdGZvcm1DaGlwc2V0IjogIlVua25vd24iDQp9',
];

/**
* 请求中绕过 Cloudflare 所需要的 SSL 证书加密方式
*
* @var string[] 用于请求的参数
*/
$ciphers13 = [
    'TLS_CHACHA20_POLY1305_SHA256',
    'TLS_AES_128_GCM_SHA256',
    'TLS_AES_256_GCM_SHA384',
];

/**
* 请求中绕过 Cloudflare 所需要的 SSL 证书加密方式
*
* @var string[] 用于请求的参数
*/
$ciphers = [
    'ECDHE-ECDSA-CHACHA20-POLY1305',
    'ECDHE-RSA-CHACHA20-POLY1305',
    'ECDHE-ECDSA-AES128-GCM-SHA256',
    'ECDHE-RSA-AES128-GCM-SHA256',
    'ECDHE-ECDSA-AES256-GCM-SHA384',
    'ECDHE-RSA-AES256-GCM-SHA384',
    'ECDHE-ECDSA-AES128-SHA',
    'ECDHE-RSA-AES128-SHA',
    'ECDHE-ECDSA-AES256-SHA',
    'ECDHE-RSA-AES256-SHA',
    'AES128-GCM-SHA256',
    'AES256-GCM-SHA384',
    'AES128-SHA',
    'AES256-SHA',
    'DES-CBC3-SHA',  # most likely not available
];

// 实例化 HttpClient
 $client = new \GuzzleHttp\Client([
    'cookies' => true, // 开启 Cookie，保存 Riot 返回的 Token
    'curl'    => [
        CURLOPT_SSLVERSION      => CURL_SSLVERSION_TLSv1_3,
        CURLOPT_SSL_CIPHER_LIST => implode(':', $ciphers),
        CURLOPT_TLS13_CIPHERS   => implode(':', $ciphers13),
    ],
]);

// 发送请求
$response = $client->request('POST', 'https://auth.riotgames.com/api/v1/authorization', [
    'json' => [
        'client_id'             => 'riot-client',
        'code_challenge'        => '',
        'code_challenge_method' => '',
        'acr_values'            => '',
        'claims'                => '',
        'nonce'                 => '69420',
        'redirect_uri'          => 'http://localhost/redirect',
        'response_type'         => 'token id_token',
        'scope'                 => 'openid link ban lol_region',
    ],
]);

// do something
```

---

# 👨‍💻 Reference Links

- [techchrism/valorant-api-docs](https://github.com/techchrism/valorant-api-docs)
