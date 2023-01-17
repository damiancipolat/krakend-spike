# krakend-spike
This project is an spike to learn and practice the basic usage of KrakenD api gateway.

### Resources:
To create this project:
- https://www.youtube.com/watch?v=YhLoCNtGkHY&list=PLRvQJHX4HOd723jUhwecqWc60Z4Tw6_vc
- https://www.krakend.io/docs/overview/

### Configuration:
To test this project run the following commands, you require docker installed in your computer.
Is required a **.json** configuration file, you can create it using a online designer https://designer.krakend.io/#!/

### Commands:

```sh
# Dowload KrakenD image
docker pull devopsfaith/krakend

# Check configuration file:
docker run -it -p 8080:8080 -v $PWD:/etc/krakend/ devopsfaith/krakend check --config krakend.json

# Run container:
docker run -p 8080:8080 -v $PWD:/etc/krakend/ devopsfaith/krakend

# Build image, optional.
docker build . -t api-gateway
```

When the configuration and install is complete, test using a request.
```sh
curl http://localhost:8080/__health
```

There are a postman collection to test the request in the project with the name **postman-collection.json**.

## Concepts:
Here we cover 8 exercises of krakend usage and how to create configuration file.

### 1) Proxy:
We create a forward **http://localhost:8080/api/products/** to **https://jsonplaceholder.typicode.com/posts**.

```json
    {
      "@comments":"This endpoint has rate limit",        
      "endpoint": "/api/products/",
      "method": "GET",
      "output_encoding": "json",
      "backend": [
        {
          "url_pattern": "/posts",
          "encoding": "json",
          "sd": "static",
          "method": "GET",
          "disable_host_sanitize": false,
          "host": [
            "https://jsonplaceholder.typicode.com/"
          ],
          "is_collection": true,
          "target": "",
          "group": "",
          "mapping": {
            "collection": "products"
          }
        }
      ],
      "extra_config": {
        "qos/ratelimit/router": {
          "max_rate": 2,
          "client_max_rate": 1,
          "strategy": "ip"
        }
      }
    }
```

### 2) Proxy query string:
We create a forward **http://localhost:8080/api/responses/{id}** to **https://jsonplaceholder.typicode.com/comments/{id}**.

```json
    {
      "@comments":"This endpoint proxy query string",
      "endpoint": "/api/responses/{id}",
      "method": "GET",
      "output_encoding": "json",
      "backend": [
        {
          "url_pattern": "/comments/{id}",
          "encoding": "json",
          "sd": "static",
          "method": "GET",
          "disable_host_sanitize": false,
          "host": [
            "https://jsonplaceholder.typicode.com/"
          ]
        }
      ],
      "input_query_strings":[
        "id"
      ]
    }
```

### 3) Proxy headers:
We create a forward **http://localhost:8080/api/responses/headers** to **https://jsonplaceholder.typicode.com/comments/**.

```json
   {
      "@comments":"This endpoint proxy headers ????",
      "endpoint": "/api/responses/headers",
      "method": "GET",
      "output_encoding": "json",
      "backend": [
        {
          "url_pattern": "/comments/",
          "encoding": "json",
          "sd": "static",
          "method": "GET",
          "disable_host_sanitize": false,
          "host": [
            "https://jsonplaceholder.typicode.com/"
          ],
          "is_collection": true,
          "target": "",
          "group": "",
          "mapping": {
            "collection": "comments"
          }
        }
      ],
      "input_headers":[
        "x-test"
      ]
    }
```

### 4) Merge responses:
We create a forward **http://localhost:8080/api/responses/merge** to **https://jsonplaceholder.typicode.com/comments/1**.

```json
    {
      "@comments":"This endpoint merge two responses post + comment.",
      "endpoint": "/api/responses/merge",
      "method": "GET",
      "output_encoding": "json",
      "backend": [
        {
          "url_pattern": "/comments/1",
          "encoding": "json",
          "method": "GET",
          "host": [
            "https://jsonplaceholder.typicode.com/"
          ]
        },
        {
          "url_pattern": "/posts/2",
          "encoding": "json",
          "method": "GET",
          "host": [
            "https://jsonplaceholder.typicode.com/"
          ]
        }
      ]
    }
```

### 5) Group data:
We create a forward **http://localhost:8080/api/responses/group** to **https://jsonplaceholder.typicode.com/comments/1**.

```json
    {
      "@comments":"This endpoint group data.",
      "endpoint": "/api/responses/group",
      "method": "GET",
      "output_encoding": "json",
      "backend": [
        {
          "url_pattern": "/comments/1",
          "encoding": "json",
          "method": "GET",
          "host": [
            "https://jsonplaceholder.typicode.com/"
          ],
          "group":"response"
        }
      ]
    }
```

### 6) Field renaming:
We create a forward **http://localhost:8080/api/responses/mapping** to **https://jsonplaceholder.typicode.com/comments/1**.

```json
   {
      "@comments":"This endpoint rename field email->emailText name->nameText.",
      "endpoint": "/api/responses/mapping",
      "method": "GET",
      "output_encoding": "json",
      "backend": [
        {
          "url_pattern": "/comments/1",
          "encoding": "json",
          "method": "GET",
          "host": [
            "https://jsonplaceholder.typicode.com/"
          ],
          "mapping":{
            "email":"emailText",
            "name":"nameText"
          }
        }
      ]
    }
```

### 7) Target data:
We create a forward **http://localhost:8080/api/post/target** to **https://jsonplaceholder.typicode.com/users/2**.

```json
    {
      "@comments":"This endpoint get the address response attribute data.",
      "endpoint": "/api/post/target",
      "method": "GET",
      "output_encoding": "json",
      "backend": [
        {
          "url_pattern": "/users/2",
          "encoding": "json",
          "method": "GET",
          "host": [
            "https://jsonplaceholder.typicode.com/"
          ],
          "target":"address"
        }
      ]
    }
```

### 8) Cache:
We create a forward **http://localhost:8080/api/post/cache** to **https://jsonplaceholder.typicode.com/users/2**.

```json
    {
      "@comments":"This endpoint use internal cache",
      "endpoint": "/api/post/cache",
      "method": "GET",
      "output_encoding": "json",
      "backend": [
        {
          "url_pattern": "/users/2",
          "encoding": "json",
          "method": "GET",
          "host": [
            "https://jsonplaceholder.typicode.com/"
          ],
          "extra_config":{
            "qos/http-cache":{
              "shared":true
            }
          }
        }
      ]
    }
```
