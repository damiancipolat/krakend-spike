# krakend-spike
This project is an spike to learn and practice the basic usage of KrakenD api gateway.

## Configuration:
To test this project run the following commands, you require docker installed in your computer.

Is required a **.json** configuration file, you can create it using a designer:

https://designer.krakend.io/#!/


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
