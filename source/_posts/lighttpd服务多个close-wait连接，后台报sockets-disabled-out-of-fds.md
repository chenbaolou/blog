---
title: 'lighttpd服务多个close_wait连接，后台报sockets disabled, out-of-fds'
date: 2017-02-07 17:29:18
tags:
---
最近在使用lighttpd架构了web服务器，奇怪的是过些时间，测试人员会报页面访问不了了。
后台netstat显示多个close_wait的TCP的连接，lighttpd日志会显示sockets disabled, out-of-fds，然后请求一直异常，且请求不会记录到日志文件。查了很多资料，问了同事，也是无果。最后，上了官网论坛，查了是lighttpd42/43的bug。
```c    
    --- a/src/mod_cgi.c
    +++ b/src/mod_cgi.c
    @@ -1027,6 +1027,7 @@ static int cgi_write_request(server *srv, handler_ctx *hctx, int fd) {
    /* sent all request body input */
    /* close connection to the cgi-script */
    if (-1 == hctx->fdtocgi) { /*(received request body sent in initial send to pipe buffer)*/
    +  --srv->cur_fds;
    if (close(fd)) {
        log_error_write(srv, __FILE__, __LINE__, "sds", "cgi stdin close failed ", fd, strerror(errno));
    }
```
意思是，cur_fds一直会增加，过段时间就到达到配置中的最大值，所以就报错了。（同事看出来的）详见http://redmine.lighttpd.net/issues/2771 。

最后解决方案是升级lighttpd到44版本。
