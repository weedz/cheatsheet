# nginx

## Proxy websocket

```nginx
# Reference: <https://nginx.org/en/docs/http/websocket.html>
location /ws {
  proxy_pass http://upstream;
  proxy_http_version 1.1;
  proxy_set_header Upgrade $http_upgrade;
  proxy_set_header Connection "upgrade";
}
```
