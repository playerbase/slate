---
title: Playerbase API Reference

language_tabs:
  - shell
  - javascript
  - ruby
  - python

toc_footers:
  - <a href='mailto:support@playerbase.io'>Questions? Contact us</a>

includes:

search: true
---

# Introduction

The Playerbase API, api.playerbase.io, provides access to esports analytics. 

Examples are provided in a variety of languages, which you can access by switching the tabs in the top right.

All responses are JSON.

# Authentication

## Getting an access token

```shell
curl --request POST \
  --url https://api.playerbase.io/oauth/token \
  --header 'content-type: application/json' \
  --data '{"client_id":"[CLIENT_ID]", "client_secret":"[CLIENT_SECRET]", "audience":"https://api.playerbase.io", "grant_type":"client_credentials"}'
```

```javascript
var request = require("request");

var options = { method: 'POST',
  url: 'https://api.playerbase.io/oauth/token',
  headers: { 'content-type': 'application/json' },
  body: '{"client_id":"[CLIENT_ID]","client_secret":"[CLIENT_SECRET]","audience":"https://api.playerbase.io","grant_type":"client_credentials"}' };

request(options, function (error, response, body) {
  if (error) throw new Error(error);
  console.log(body);
});
```

```ruby
require 'uri'
require 'net/http'

url = URI("https://api.playerbase.io/oauth/token")

http = Net::HTTP.new(url.host, url.port)
http.use_ssl = true
http.verify_mode = OpenSSL::SSL::VERIFY_NONE

request = Net::HTTP::Post.new(url)
request["content-type"] = 'application/json'
request.body = "{\"client_id\":\"[CLIENT_ID]\",\"client_secret\":\"[CLIENT_SECRET]\",\"audience\":\"https://api.playerbase.io\",\"grant_type\":\"client_credentials\"}"

response = http.request(request)
puts response.read_body
```

```python
import http.client

conn = http.client.HTTPSConnection("api.playerbase.io")

payload = "{\"client_id\":\"[CLIENT_ID]\",\"client_secret\":\"[CLIENT_SECRET]\",\"audience\":\"https://api.playerbase.io\",\"grant_type\":\"client_credentials\"}"

headers = { 'content-type': "application/json" }

conn.request("POST", "/oauth/token", payload, headers)

res = conn.getresponse()
data = res.read()

print(data.decode("utf-8"))
```

> Sample response (JSON):

```json
{
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9...",
  "token_type": "Bearer"
}
```

You can execute a client credentials exchange to get an access token for api.playerbase.io. All requests to api.playerbase.io must include a valid token in the header. For example:

`Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9...`

In order to request a token, you need a client ID and client secret. If you don't have those values, <a href="mailto:support@playerbase.io">contact us</a>.

# Counter-Strike: Global Offensive

## /v1/csgo/parse

> Sample request body (JSON):

```json
{
  "matches": [
    {
      "url": "s3://esea2-playerbase-test/replays/esea_match_8514929.zip",
      "players": [
        {
          "steam_id": "765611980XXXXXXXX",
          "identifiers": [
            {
              "source_name": "esea",
              "source_id": "12345"
            }
          ]
        },
        ...
      ]
    },
    ...
  ]
}
```

> Sample response (JSON):

```json
{
  "data": {
    "job_id": "fc66e61a-aefb-4f79-81cb-237a6d4c843f"
  },
  "status": "ok"
}
```

This endpoint accepts requests to parse .dem files. You can also include third-party identifiers for the players involved in the provided matches.

### HTTP request

`POST https://api.playerbase.io/v1/csgo/parse`

### Body parameters

Parameter | Type | Description
--------- | ---- | -----------
matches | Array | Array of Match objects 

### Match

Parameter | Type | Description
--------- | ---- | -----------
url | String | URL of a .dem file, or a directory or an archive (.zip, .gz) containing .dem files. Only supports the s3:// protocol currently. To access files on an s3 bucket, you need to grant read access to the API (instructions forthcoming). If passing a directory, list permissions are also required.
players | Array | Array of Player objects

### Player

Parameter | Type | Description
--------- | ---- | -----------
steam_id | String | Steam identifier (e.g., 765611980XXXXXXXX)
identifiers | Array | Array of Identifier objects 

### Identifier

Parameter | Type | Description
--------- | ---- | -----------
source_name | String | Name of third party taxonomy. e.g., "hltv", "liquipedia", "esea"
source_id | String | Identifier

