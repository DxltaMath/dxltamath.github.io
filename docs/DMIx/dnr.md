---
sidebar_position: 2
name: "Network Rules"
desc: "Network interception that DMIx performs"
---

# DeclarativeNetRequest

Network rules that DMIx runs in the background.


### Block main.js
This will prevent DeltaMath from using script tags to load the original main.js file.

<details>
    <summary>JSON Code</summary>

```json
{
    "id": 1,
    "priority": 1,
    "action": {
        "type": "block"
    },
    "condition": {
        "urlFilter": "*://*deltamath.com/app/main*js",
        "resourceTypes": ["script"]
    }
}
```

</details>



### Remove CSP and XFrame headers
This removes all `Content-Security-Policy` and `X-Frame-Options` headers from all DeltaMath domains, allowing us to inject our code.

<details>
    <summary>JSON Code</summary>

  ```json
{
    "id": 2,
    "priority": 1,
    "action": {
        "type": "modifyHeaders",
        "responseHeaders": [
            {
                "header": "Content-Security-Policy",
                "operation": "remove"
            },
            {
                "header": "content-security-policy",
                "operation": "remove"
            },
            {
                "header": "X-Frame-Options",
                "operation": "remove"
            },
            {
                "header": "x-frame-options",
                "operation": "remove"
            }
        ]
    },
    "condition": {
        "urlFilter": "*://*deltamath.com/*",
        "resourceTypes": ["main_frame", "sub_frame", "stylesheet", "script", "image", "font", "object", "xmlhttprequest", "ping", "csp_report", "media", "websocket", "webtransport", "webbundle", "other"]
    }
}
```

</details>


### Manifest
Code inside the extension Manifest that runs the rulesets.

<details>
    <summary>JSON Code</summary>

```json
"permissions": [
    "declarativeNetRequest"
],
"host_permissions": [
    "https://*.deltamath.com/*"
],
"declarative_net_request": {
    "rule_resources": [
        {
            "id": "ruleset_1",
            "enabled": true,
            "path": "~rulesets/rules.json"
        }
    ]
}
```

</details>



### Source file

<details>
    <summary>Full rules.json</summary>

  ```json
[
    {
        "id": 1,
        "priority": 1,
        "action": {
          "type": "block"
        },
        "condition": {
          "urlFilter": "*://*.deltamath.com/app/main*js",
          "resourceTypes": ["script"]
        }
    },
  
    {
        "id": 2,
        "priority": 1,
        "action": {
            "type": "modifyHeaders",
            "responseHeaders": [
                {
                  "header": "Content-Security-Policy",
                  "operation": "remove"
                },
                {
                  "header": "content-security-policy",
                  "operation": "remove"
                },
                {
                  "header": "X-Frame-Options",
                  "operation": "remove"
                },
                {
                  "header": "x-frame-options",
                  "operation": "remove"
                }
            ]
        },
        "condition": {
          "urlFilter": "*://*deltamath.com/*",
          "resourceTypes": ["main_frame", "sub_frame", "stylesheet", "script", "image", "font", "object", "xmlhttprequest", "ping", "csp_report", "media", "websocket", "webtransport", "webbundle", "other"]
        }
    }
    
]
```

</details>