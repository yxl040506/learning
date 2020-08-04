# 哇哇哇nginx 
### 使用nginx解决本地跨域问题
```
server {
        	listen 3001;
		 	server_name local.jd.com;
		 
			location /static/ {
		          	proxy_pass http://localhost:3000/;
		        }
			location /live/fm {
		          	proxy_pass http://localhost:3000/live/fm;
		        }
			location /live/tp {
		          	proxy_pass http://localhost:3000/live/tp;
		        }
			location /live/xc {
		          	proxy_pass http://localhost:3000/live/xc;
		        }
			location /live/ppp {
		          	proxy_pass http://localhost:3000/live/ppp;
		        }
			location /lingapi/ {
				proxy_pass http://ling-test.jd.com:80/;
			}
    }

```