Day2 command line notes

How to open:  GitBash  (in Git files, different from command line)

Useful commands:

(1) about 'man':
There is no command called 'man' can be used in windows operation systems. Only'--help' works.

(2)cd + 想要切换到的目录     eg: cd / file a = change directory to file a

(3)pwd/echo   ---  to view the current place

(4)ls --- list all the files

(5)cat hello.txt --- print all the content of the file

(6) sort hello.txt --- 按字典排序后输出

(7)uniq hello.txt --- 去除连续重复的行

(8)head/tail hello.txt --- 分别打印该文件的前几行和后几行

(9) grep pattern hello.txt
grep: Global Regular Expression Print（全局正则表达式打印）equals to Ctrl+F

pattern是在真正查找的时候想要查找的内容

(10)sed -i 's/pattern/replacement/g' hello.txt  --- 把所有的pattern**替换**成replacement

sed: stream editor  流式处理数据

\-i : inline 直接修改文件

s/ 替换

/g 替换所有

(11) find \~/Downloads -type f -name "\*.zip" -mtime +30

在下载目录中查找所有超过30天的zip文件

find \~ -type f -size +100M -exec ls -lh {} \\;

在home目录找所有大于100M的文件并列出，且把每个匹配的文件路径替换到{}内的位置。其中“-exec”的命令必须以“；”结尾

find . -name "\*.py" -exec grep -l "TODO" {} \\;

在当前目录下找到所有含TODO内容的.py文件

(12) awk '{print $2}' hello.csv

打印hello.csv里面的每一行的第二列

awk -F, '{print $2}' hello.csv

按照逗号分成列，然后打印第二列

(13) 理解一段bash脚本

\#!/bin/bash                          ------规定解释器

set -euo pipefail                   

\-----上面那行里：
-e 遇到任何命令执行失败，立即终止

\-u 遇到未定义的量，立刻报错推出

\-o pipefail 管道符|链接的命令里，只要有一个失败，整个管道就失败（防御性编程）





\# Start CPU stress in background

stress --cpu 8 \&       ---- 启动8个死循环进程吃满cpu，\&是把程序丢到后台运行，不卡终端

STRESS\_PID=$!         ----$!专门获取上一个在后台运行的进程ID，存到这个变量里，方便后续杀掉



\# Setup log file

LOGFILE="test\_runs\_$(date +%s).log"        ---$(xxx)命令替换，把里面的输出结果塞到字符串里，date是获取时间戳

echo "Logging to $LOGFILE"          ---日志路径



\# Run tests until one fails

RUN=1                                    -----初始化一个计数器

while cargo test my\_test > "$LOGFILE" 2>\&1; do    

&#x20;   echo "Run $RUN passed"        ----告诉我第几次成功了

&#x20;   ((RUN++))                              ----Bash的算术运算，把RUN的值+1

done



while会判断后面的命令是否执行成功，cargo test my\_test是Rust的测试命令，> "$LOGFILE"把标准输出覆盖写入到日志文件，2>\&1把标准错误重定向到标准输出去，报错信息也会一起写进日志文件。整体while语句的逻辑：如果测试成功，就进入循环体，如果崩溃就跳出循环。



\# Cleanup and report

kill $STRESS\_PID                        ----循环退出说明测试终于崩溃了，不再需要压力测试，用kill把放在后台的stress进程杀掉，释放cpu

echo "Test failed on run $RUN"      ----报告在第几次测试的时候挂了

echo "Last 20 lines of output:"     

tail -n 20 "$LOGFILE"     -----精准提取日志的最后20行

echo "Full log: $LOGFILE"     ----告知我完整的日志在哪里



