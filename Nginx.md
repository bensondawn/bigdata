## location匹配优先级详解

>  location模块是nginx配置中出现最多的一个配置，主要用于配置路由访问信息。
>
> - 全等匹配："="前缀的指令严格匹配这个查询。如果找到，停止搜索，直接返回，等级最高。
>
>     - 只匹配 / 查询，比如http://localhost/ 将匹配该规则
>
>          location = / {
>
>          }
>
>    - 只匹配/static/image的查询
>
>         location = /static/image {
>
>         }
>
> - 普通匹配
>
>     - 不带^~：如果有多个location都能匹配的话，优先匹配表达式最长的location，匹配到暂存匹配结果，继续正则匹配。
>
>          location /test/ { … }
>
>          location /test/aaa/bbb/ { … }
>
>          URL请求为http://localhost:8080/test/aaa/bbb 以最大前缀为原则，则优先匹配第二项。
>
>     - 带^~：前缀匹配，按照最长表达式匹配到最优选项后，则终止后续正则匹配，直接进入跳转。
>
>          location ^~ /static/ { … }
>
>          location ^~ /static/aaa/bbb/ { … }
>
>          URL请求为http://localhost:8080/static/aaa/bbb/ 以最大前缀为原则，则优先匹配第二项。
>
> - 正则表达式匹配，包括~（区分大小写）和~*（不区分大小写）；正则匹配以从上到下的顺序为优先级，一旦匹配一个则不再继续匹配。如果匹配不到则使用前面的普通匹配的结果。正则匹配受先后顺序的影响，前面匹配成功后，后面则不再匹配。
>
> - location =/ { … } 与 location / { … } 的差别：
>
>     - 前一个是精确匹配，只响应/请求，所有/xxx或/xxx/xxxx类的请求都不会以前缀的形式匹配到它；
>     - 后一个是只要以 / 为前缀的请求都会被匹配到，其它规则都匹配失败后则最终匹配该规则。如：/abc ， /test/abc， /test/abc/aaaa；
>
> - 注意：不论前面哪种匹配方式，nginx转发后访问的目录或者control接口都必须跟url完全匹配。