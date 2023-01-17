# krakend-spike
This project is an spike to learn and practice the basic usage of KrakenD api gateway.

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
