# POC API Gateway with Tyk

This POC is based on this medium article: [Get Started With Tyk API Gateway](https://medium.com/geekculture/get-started-with-tyk-api-gateway-aae127186e1b).

## Setup

```
docker-compose up -d
```

## Configuration

In `docker-compose.yml`, you can set the Tyk secret editing the TYK_GW_SECRET environment entry (a value is already available but you can change it).

All the endpoints proxied by Tyk are available in json files inside `.docker/data/gateway/apps`. The file `httpbin.json` is an example of how to proxy `httpbin.org`. If you modify this file or add a new one, remember to notify Tyk to reload configurations (see how below).

## Testing and using

- **Reload API Gateway configuration:**

    ```
    http :8080/tyk/reload 'x-tyk-authorization:6cf68f93-f6d9-4a96-9ed1-68632a6e448e'
    ```

    or

    ```
    curl http://localhost:8080/tyk/reload -H 'x-tyk-authorization:6cf68f93-f6d9-4a96-9ed1-68632a6e448e'
    ```

- **Verify that the API Gateway is up and running:**

    ```
    http :8080/hello
    ```

    or

    ```
    curl http://localhost:8080/hello
    ```

- **Request proxied `httpbin.org`:**

   ```
   http :8080/httpbin/get
   ```

   or

   ```
   curl http://localhost:8080/httpbin/get
   ```

- **Request token authenticaded proxied `httpbin.org`:**

    Request an authentication token
    ```
    cat .docker/data/request/session-token.json | http post :8080/tyk/keys 'x-tyk-authorization:6cf68f93-f6d9-4a96-9ed1-68632a6e448e'
    ```

    With the token (copy the value from "key" entry), request:
    ```
    http :8080/httpbin/bearer/get 'Authorization: <token>'
    ```

- **Request basic authenticaded proxied `httpbin.org`:**

    Request an user session:
    ```
    cat .docker/data/request/basic-session-token.json | http post :8080/tyk/keys/a-user 'x-tyk-authorization:6cf68f93-f6d9-4a96-9ed1-68632a6e448e'
    ```

    Request with basic auth
    ```
    http :8080/httpbin/basic/get --auth a-user:a-very-secure-password
    ```