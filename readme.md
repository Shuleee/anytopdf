# 在linux环境下 从doc/docx, ppt/pptx, xls/xlsx转为pdf

unoconv (依赖于libreoffice)

#### 0. 确认libreoffice相应组件已经安装完成

#### 1. 安装unoconv
  
    sudo apt-get install unoconv

#### 2. 转换

    unoconv -f pdf sample.doc

其中 -f 是-format，后面给定想要转换的格式（例如：pdf），sample.doc为要转换的文件名，最终会得到sample.pdf 这个文件。


还可以添加
  参数：-o exemple.pdf代表输出文件名称
  
  参数：-e PageRange=1-2 代表需要转换的页数

#### 3. 后台服务调用unoconv

#### 3.1 首先启动unoconv server（启动libreoffice 的 soffice）

  unoconv --listener

  或者编写脚本文档：
    `# create the start script for unoconv - start in listener mode:
    touch /etc/init.d/unoconvd
    chmod 755 /etc/init.d/unoconvd

    # File /etc/init.d/unoconvd: # script includes the tar file

    #!/bin/sh
    ### BEGIN INIT INFO
    # Provides: unoconvd
    # Required-Start: $network
    # Required-Stop: $network
    # Default-Start: 2 3 5
    # Default-Stop:
    # Description: unoconvd - Converting documents to PDF by unoconv
    ### END INIT INFO
    case "$1" in
        start)
            /usr/bin/unoconv --listener &amp;
            ;;
        stop)
            killall soffice.bin
            ;;
        restart)
            killall soffice.bin
            sleep 1
            /usr/bin/unoconv --listener &amp;
            ;;
    esac`

#### 3.2
  连接到unoconv并完成转换：
        
        unoconv --connection 'socket,host=127.0.0.1,port=2002,tcpNoDelay=1;urp;StarOffice.ComponentContext' -f pdf 3.doc
        
  其中3.doc为想要转化的文件，其中host地址以及port号都可以在开启服务时修改，如不修改则默认为127.0.0.1的本地ip以及2002的端口号
