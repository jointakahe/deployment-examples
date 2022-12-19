Webserver Examples
==================

nginx
-----

**The nginx example needs at least nginx version 1.20.**

When you want to use the nginx example, including caching, you need to create the specific directories first, so that nginx will not run into errors:

```bash
mkdir -p /usr/share/nginx/takahe/{static,cache,logs}
```

If you don't want caching, remove the lines starting with `proxy_cache_`, `add_header X-Cache $upstream_cache_status;` and `proxy_ignore_headers X-Accel-Expires Expires Cache-Control;`  

Remember to use the command `nginx -t` to check, if the configuration works.