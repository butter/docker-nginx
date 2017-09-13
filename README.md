# Supported tags and respective `Dockerfile` links

-	[`1.13.5`, `1.13`, `latest` (*Dockerfile*)](https://github.com/butter/docker-nginx/blob/bb23d0f696060e134c0514eb98793ec2fb1a90d1/Dockerfile)
-	[`1.11.10`, `1.11` (*1.11/Dockerfile*)](https://github.com/butter/docker-nginx/blob/346bde140f16b21942fcd0e4d422b088905f5efa/1.11/Dockerfile)
-	[`1.11.9` (*1.11/Dockerfile*)](https://github.com/butter/docker-nginx/blob/1c7ee3da033923d4ecf3794d9b9d17f67390619f/1.11/Dockerfile)

# What is Nginx?
Nginx is a web server, which can also be used as a reverse proxy, load balancer and HTTP cache.

> [wikipedia.org/Nginx](https://en.wikipedia.org/wiki/Nginx)

![logo](https://upload.wikimedia.org/wikipedia/commons/c/c5/Nginx_logo.svg)

# How to use this image

## Create a `Dockerfile` in your Nginx web server

```dockerfile
FROM butter/nginx:1.13.5
CMD ["nginx", "-g", "daemon off;"]
```

# License

View license information for [Nginx](https://nginx.org/LICENSE).
