# linux 考题

1. Linux查看CPU核数命令

    `cat /proc/cpuinfo | grep "cpu cores" | uniq` (Linux uniq 命令用于检查及删除文本文件中重复出现的行列，一般与 sort 命令结合使用。)
