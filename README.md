# GISAID downloader GISAID数据爬虫（附界面程序）

# 2025.04.08 原来的程序无法使用，进行修改，能正常获取数据
fork from https://github.com/c4-driod/GISAID-spiders

[Windows版本下载](https://github.com/c4-driod/GISAID-spiders/releases/download/gisaid_spiders/gisaid_downloader.zip)

**基于selenium，用于全自动下载GISAID数据.**  \
Based on selenium, aiming at download GISAID data automatically.

---
需要火狐浏览器或者谷歌浏览器（谷歌开无界面会被服务器拒绝），默认用火狐浏览器。\
使用火狐，需要准备： 
  * **geckodriver**（Windows版已加入目录，也可以自行百度、下载并把文件放在脚本同文件夹下）； 
  * 安装**火狐浏览器**； 
  * 安装Python第三方包**selenium**； 

有两种使用方式：  
1. **命令行**（教程在下方）  
2. **界面程序**（ start.py 或者 gisaid下载器.exe ）;

打包好的“gisaid下载器.exe”程序在gisaid_downloader.zip内，windows下可以直接解压使用。

感谢大佬的资助，项目得以完成。

---
## 功能
1.全自动下载一些基础数据\
2.断点续下（如果想重新下过，可以把advance文件夹里对应名字的json文件删除）

---
## 示例
### 下载metadata和fasta
python gisaid_downloader.py -n 这里填你的账号名 -p 这里填密码 -f 这里填csv文件
### 下载metadata、fasta和病例数据
python gisaid_downloader.py -n name -p password -f xx.csv -dr 2 4

---
## 注意事项
### csv文件如何获得？
在GISAID网页上用多选框选择数据，然后点击“select”按钮，再点击“CSV”按钮即可下载得到序列的CSV文件。
### 不要长时间连续下载
经实测，连续下载fasta文件12个小时就会封ip，且至少一周内不会解封（但不会封号）。  
建议连续下载10小时后休息几小时，再继续下载。
### 爬虫卡住了？
请注意，如果爬虫卡住，可能是以下原因： \
1.还没下载就卡住了，这种情况很可能是Firefox的日志文件导致的（会导致跳过按某些按钮，具体原因不明），删除脚本目录下的geckodriver.log文件一般能解决。
如果还不能，再试试不开启界面模式，或者多试几次； \
2.卡在了下载patient status 的过程中，具体表现就是下载停留在了patient status（dr=4）那一步。 \
这是因为GISAID网站在处理这个数据（patient status）的时候，如果id一次性非常大（比如5000），将会非常慢（手动下载也会这样）。比较合适的一个值是2000。 \
第一次下载时使用的dr没有4（即不包含patient status这类数据），爬虫就会把一次性下载的数量设为5000（也就是一次性能下载的最大值）， \
之后使用的包含了4，还是会按5000一次来下载，所以会卡住。 \
解决方法： \
删掉advance文件夹下同名json文件，dr参数值带上4（或者界面上选中patient status下载选项），重新开始下载。 \
3.如果你的下载只进行了第一次就一直停着，而且你的系统语言不是**简体中文**或者**英文**，可以把Firefox的默认语言设置为简体中文/英文，或者如果可行的话，重新以这两种语言中的一种安装；也可以把gisaid_downloader.py代码中“wait_downloaded_filename”函数下的“剩余时间”改成你的火狐浏览器下载时，下载界面进度条上一定会出现的文字，比如繁体的“剩餘時間”（但作者并不清楚是不是这个）。因为代码就是靠检测这段文字是否存在来判断下载是否结束，系统语言不同，没有识别到对应文字，就会认为下载一直没有开始，从而一直等待。 \
3.If your download just starts once, problem would be the system language. Try to change your Firefox language into English/Simplified Chinese or replace the characters "剩余时间" in file "gisaid_downloader.py" into the characters must appear on your Firefox download bar to fix it.  


---
## 需要GISAID账号？
加入GISAID账号共享QQ群：621209458，免费分享账号。  

---
## 所有参数
-n NAME, --name NAME  账号名\
  -p PASSWORD, --password PASSWORD\
                        密码\
  -f MISSION_FILE, --mission_file MISSION_FILE\
                        包含序列号的csv文件\
  -ms MISSION_STR [MISSION_STR ...], --mission_str MISSION_STR [MISSION_STR ...]\
                        通过此参数直接输入一至多个序列号下载\
  -sp SAVE_PATH, --save_path SAVE_PATH\
                        结果的保存路径\
  -g, --is_graphic      开启浏览器界面，默认不开启\
  -nm, --do_not_merge_data\
                        不解压、合并数据，默认为解压+合并\
  -r, --retain_raw_data\
                        保留原始数据（无——nm时无效），默认不保留\
  --driver DRIVER       浏览器类型，目前支支持firefox和chrome\
  -dr DOWNLOAD_RANKS [DOWNLOAD_RANKS ...], --download_ranks DOWNLOAD_RANKS [DOWNLOAD_RANKS ...]\
                        通过此参数传入配置下载的数据类型，可一次多下。默认为[2,]，即fasta和metadata的tar文件。其中数字是对应类型选项从上向下的序号： \
                        1：Dates and Location \
                        2：Input for the Augur pipeline\
                        3：Nucleotide Sequences (FASTA) \
                        4：Patient status metadata \
                        5：Sequencing technology metadata

