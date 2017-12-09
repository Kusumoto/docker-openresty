# Docker-Openresty
Docker tooling for OpenResty build-in lua-resty-waf

Base from : https://github.com/openresty/docker-openresty

[![Docker Build Status](https://img.shields.io/docker/build/kusumoto/docker-openresty.svg)](https://hub.docker.com/r/kusumoto/docker-openresty/)

## Original Source 

Openresty : https://openresty.org/

lua-resty-waf : https://github.com/p0pr0ck5/lua-resty-waf

### Nginx Configuration 
The Nginx configuration load from `/usr/local/openresty/nginx/conf/nginx.conf` you can mount volume from this point outter container. Default html part `/usr/local/openresty/nginx/html` .

### Example nginx.conf for use lua-resty-waf
Orginal From : https://github.com/p0pr0ck5/lua-resty-waf/blob/master/README.md
```lua
http {
        -- include lua_resty_waf in the appropriate paths
        lua_package_path '/usr/local/openresty/lua_resty_waf/?.lua;;';
        lua_package_cpath '/usr/local/openresty/lua_resty_waf/?.lua;;';

        init_by_lua '
            -- use resty.core for performance improvement, see the status note above
            require "resty.core"

            -- require the base module
            local lua_resty_waf = require "resty.waf"

            -- define options that will be inherited across all scopes
            lua_resty_waf.default_option("debug", true)
            lua_resty_waf.default_option("mode", "ACTIVE")

            -- this may be desirable for low-traffic or testing sites
            -- by default, event logs are not written until the buffer is full
            -- for testing, flush the log buffer every 5 seconds
            lua_resty_waf.default_option("event_log_periodic_flush", 5)

            -- perform some preloading and optimization
            lua_resty_waf.init()
        ';
    }

    server {
        location / {
            access_by_lua '
                local lua_resty_waf = require "resty.waf"

                local waf = lua_resty_waf:new()

                -- default options can be overridden
                waf:set_option("debug", false)

                -- run the firewall
                waf:exec()
            ';

            header_filter_by_lua '
                local lua_resty_waf = require "resty.waf"

                -- note that options set in previous handlers (in the same scope)
                -- do not need to be set again
                local waf = lua_resty_waf:new()

                waf:exec()
            ';

            body_filter_by_lua '
                local lua_resty_waf = require "resty.waf"

                local waf = lua_resty_waf:new()

                waf:exec()
            ';

            log_by_lua '
                local lua_resty_waf = require "waf"

                local waf = lua_resty_waf:new()

                waf:exec()
            ';
        }
    }
```
    
### Contact info
* [Website :: https://kusumotolab.com](https://kusumotolab.com)
* [Twitter :: @Kusumoto_ton](https://twtter.com/kusumoto_ton)
* [Facebook :: Weerayut Hongsa](https://facebook.com/Azerdar.t.Kusumoto)
