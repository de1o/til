###在看pagespeed配置的过程中学到的

1.  HTTP Header之`Vary: User-Agent|Accept-Encoding|...`，用来声明，cache会依赖于哪些HTTP Header来决定它们是否是不同的版本。比如Accept-Encoding，对于接受Gzip和不接受Gzip的客户端来说，缓存服务器需要替它们分别缓存不同的版本。避免使用User-Agent这样的字段作为Vary字段，因为这样会导致需要缓存的份数巨多。pagespeed为了减少缓存的数量，默认是不遵循Vary字段的。
2.  HTTP Header之`Cache-Control: `，客户端和服务器都可以指明Cache-Control字段，总得来说该字段可以分成以下几类：

	*  用来约束哪些内容可以被缓存，通常是源服务器指定
	*  用来约束一个缓存可以缓存哪些内容，源服务器和客户端均可以指定
	*  修改默认的缓存失效机制，Both
	*  控制缓存的重验证，重载，客户端指定
	*  对实体的改变，主要用来约束一些不透明代理对缓存内容的优化，用在要求每个字节都准确的情况下
	*  拓展caching系统
	
	为静态文件开启缓存`http://www.darrenfang.com/2015/01/setting-up-http-cache-and-gzip-with-nginx/`。
	
	
		location ~* ^.+\.(ico|gif|jpg|jpeg|png)$ { 
        	access_log   off; 
        	expires      30d;
		}
		location ~* ^.+\.(css|js|txt|xml|swf|wav)$ {
    		access_log   off;
    		expires      24h;
		}
		location ~* ^.+\.(html|htm)$ {
        	expires      1h;
		}
		
		location ~* ^.+\.(eot|ttf|otf|woff|svg)$ {
        	access_log   off;
        	expires max;
		}
		
		