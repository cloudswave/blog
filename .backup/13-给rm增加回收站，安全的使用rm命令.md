[给rm增加回收站，安全的使用rm命令](https://github.com/cloudswave/blog/issues/13)

```
#!/usr/bin/env bash
#
# Description: 给rm命令增加回收站
# System Required: CentOS/Debian/Ubuntu
# Version: 1.0.0

cat > /usr/local/bin/rm.sh << 'EOF'
#!/bin/bash 

RECYCLE=~/.local/share/Trash/files
test ! -d ${RECYCLE} && mkdir -p ${RECYCLE} #if not trash folder,create one  
_datedir=`date +"%Y%m%d"`  
_dstdir=${RECYCLE}/${_datedir}  
test ! -d ${_dstdir} && mkdir -p ${_dstdir} #if not trash folder,create one  

for _parms in $*  
do  
if [ -d ${_parms} ] || [ -f ${_parms} ];then  
  test -d ${_parms} && echo "${_parms} is directory" && mv ${_parms} ${_dstdir}/  
  test -f ${_parms} && echo "${_parms} is file" && mv ${_parms} ${_dstdir}/  
  else   echo "${_parms} is not directory or file"  
fi  
done
EOF

chmod +x /usr/local/bin/rm.sh
echo "alias rm='/usr/local/bin/rm.sh'" >> ~/.bashrc
echo "alias surm='/bin/rm -i'" >> ~/.bashrc
echo "alias rmt='/bin/rm -i -r ~/.local/share/Trash/files/*'" >> ~/.bashrc # 一键清理回收站命令: rmt -f

source ~/.bashrc
```
<!--csdn-article-id:128270345-->