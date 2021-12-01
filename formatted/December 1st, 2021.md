- r2m_web 项目代码
    - public @ResponseBody 写法
    - controller 中做了太多的业务逻辑
    - @Transactional(rollbackFor = Exception.class)
        - 在@Transactional注解中如果不配置rollbackFor属性,那么事物只会在遇到RuntimeException的时候才会回滚,加上rollbackFor=Exception.class,可以让事物在遇到非运行时异常时也回滚
    - @Autowired建议修改为@Resource
    - maven个别插件没有版本号无法下载
- controller 层不写业务代码
    - 控制器，主要负责业务的调度，而业务主要放在service中来实现
    - 代码都写在controller会看起来很乱！易读性差！
    - 也是最重要的一条！applicationContext-db.xml中配置的事务配置的事务不会生效！
    - 如果用 @Transactional注解还会导致接口404
- 凡事有交代，件件有着落，事事有回音。首先，是做事[靠谱](<靠谱.md>)。
- vir脚本
    - 停止所有的container（容器），这样才能够删除其中的images：docker stop $(docker ps -aq)
    - 如果想要删除所有container（容器）的话再加一个指令：docker rm $(docker ps -aq)
    - docker pull luminoleon/epicgames-claimer
    - docker run -it --name=epic luminoleon/epicgames-claimer
    - docker run -it --name=epicgames -v /你的硬盘/docker/epicgames_claimer/User_Data:/User_Data -e PUSH_SERVERCHAN_SENDKEY=<server酱sendkey> -e RUN_AT=10:00 -e AUTO_UPDATE=true -e TZ=Asia/Shanghai luminoleon/epicgames-claimer:latest -u <epic账号> -p <epic密码>
    - docker run --name=cast -p 8080:8080 -v $(pwd)/data:/app/data/ -v $(pwd)/config.toml:/app/config.toml ghcr.io/fqx/podsync-with-yt-dlp:latest
    - docker pull ghcr.io/fqx/podsync-with-yt-dlp:latest
    - sudo apt-get install docker-ce
- cast 脚本 config.toml
    - ```javascript
[server]
port = 8080
hostname = "http://cast.yibishe.com:8080" # 此处填写 IP 地址或者域名，如果有HTTPS证书的话记得改成 https://
data_dir = "/app/data" # 不要改动这个地方！

# Tokens from `Access tokens` section
[tokens]
youtube = "AIzaSyBN0356XIPQHPS4LjJM2fwSrNBWBhtOjiI" # 引号内填写 YouTube API Key.

[feeds]
  [feeds.ID1]
  # ID1 为最终生成的RSS文件的名称，可以修改
  url = "www.youtube.com/channel/UCtAIPjABiQD3qjlEl1T5VpA" # 文昭 引号内填写Youtube频道/组织/用户/播放列表的链接，注意不要有 http:// 部分！！！
  page_size = 3 # 每次更新节目时的分页大小
  update_period = "20h" # 更新周期，例如: "60m", "4h", "2h45m"
  quality = "low" # 文件品质高-high，或低-low
  format = "audio" # 文件类型音频-audio，视频-video，通常播客使用音频类型即可
  # filters = { title = "111", not_title = "222", description = "333", not_description = "444" } # [可选项，使用时去除行首井号]过滤节目，只保留「标题含有111，不含222，描述包含 333，不含 444 的节目」
  clean = { keep_last = 3 } # 自动清理，只保留最近10期节目
  # 可以配置多个 feeds
  [feeds.ID2]
  url = "www.youtube.com/channel/UC2IpIQXiLrMV3EAMjfKbcUw" # 雪石看世界
  page_size = 3
  update_period = "1h"
  quality = "low"
  format = "audio"
  clean = { keep_last = 5 } # 自动清理，只保留最近10期节目

  [feeds.ID3]
  url = "www.youtube.com/channel/UCJncdiH3BQUBgCroBmhsUhQ" # GuanchaNews观察者网
  page_size = 3
  update_period = "3h"
  quality = "high"
  format = "video"
  clean = { keep_last = 3 } # 自动清理，只保留最近10期节目

  [feeds.ID4]
  url = "www.youtube.com/channel/UCghLs6s95LrBWOdlZUCH4qw" # stone记
  page_size = 3
  update_period = "1h"
  quality = "low"
  format = "audio"
  clean = { keep_last = 3 } # 自动清理，只保留最近10期节目

  [feeds.ID5]
  url = "www.youtube.com/channel/UCr_F4Y9iboUKlg_ZPm4jkVQ" # 老梁
  page_size = 3
  update_period = "13h"
  quality = "low"
  format = "video"
  clean = { keep_last = 3 } # 自动清理，只保留最近10期节目

  [feeds.ID6]
  url = "www.youtube.com/channel/UC-RJVvzcNw4_nMmTRYta3Qw" # 坦克猫
  page_size = 3
  update_period = "4h"
  quality = "low"
  format = "audio"
  clean = { keep_last = 3 } # 自动清理，只保留最近10期节目

  [feeds.ID7]
  url = "www.youtube.com/channel/UCiOR3zQCU-tLza5g1MuqABA" # 寰宇全視界
  page_size = 3
  update_period = "1h"
  quality = "low"
  format = "video"
  clean = { keep_last = 5 } # 自动清理，只保留最近10期节目

  [feeds.ID8]
  url = "www.youtube.com/channel/UCa6ERCDt3GzkvLye32ar89w" # 江峰时刻
  page_size = 3
  update_period = "12h"
  quality = "low"
  format = "audio"
  clean = { keep_last = 3 } # 自动清理，只保留最近10期节目

  [feeds.ID9]
  url = "www.youtube.com/channel/UCS1kMgt4igpfjL5lhGvo-fw" # SchelleyYuki
  page_size = 3
  update_period = "40h"
  quality = "high"
  format = "video"
  clean = { keep_last = 2 } # 自动清理，只保留最近10期节目

  [feeds.ID10]
  url = "www.youtube.com/channel/UCSs4A6HYKmHA2MG_0z-F0xw" # 李永乐老师
  page_size = 3
  update_period = "24h"
  quality = "high"
  format = "video"
  clean = { keep_last = 2 } # 自动清理，只保留最近10期节目

  [feeds.ID11]
  url = "www.youtube.com/channel/UCIXOIjR2mp8tHz78DE0vj2A" # 徐晓冬北京格斗狂人
  page_size = 3
  update_period = "20h"
  quality = "low"
  format = "video"
  clean = { keep_last = 2 } # 自动清理，只保留最近10期节目

  [feeds.ID12]
  url = "www.youtube.com/channel/UCTmkJjSjrtpH2HAIpZ-qFRQ" # Zhou's Talkshow 周周侃
  page_size = 3
  update_period = "13h"
  quality = "low"
  format = "audio"
  clean = { keep_last = 3 } # 自动清理，只保留最近10期节目

  [feeds.ID13]
  url = "www.youtube.com/channel/UCii04BCvYIdQvshrdNDAcww" # WukongDaily
  page_size = 3
  update_period = "20h"
  quality = "high"
  format = "video"
  clean = { keep_last = 2 } # 自动清理，只保留最近10期节目

  [feeds.ID14]
  url = "www.youtube.com/channel/UCLZBXiS9ZrIXgKBs_SMfGBQ" # 少康戰情室
  page_size = 3
  update_period = "1h"
  quality = "low"
  format = "video"
  clean = { keep_last = 5 } # 自动清理，只保留最近10期节目

  [feeds.ID15]
  url = "www.youtube.com/channel/UCpPswAyGzdRwWmiW5oTNnvA" # BIGDONGDONG
  page_size = 2
  update_period = "24h"
  quality = "high"
  format = "video"
  clean = { keep_last = 1 } # 自动清理，只保留最近10期节目

  [feeds.ID16]
  url = "www.youtube.com/channel/UCG6ADYIl4GxaQib1HKIsRNg" # 大康有话说
  page_size = 3
  update_period = "1h"
  quality = "low"
  format = "audio"
  clean = { keep_last = 2 } # 自动清理，只保留最近10期节目

  [feeds.ID17]
  url = "www.youtube.com/channel/UCAq_xQV8pJ2Q_KOszzaYPBg" # 崔永元
  page_size = 3
  update_period = "20h"
  quality = "high"
  format = "video"
  clean = { keep_last = 2 }

  [feeds.ID18]
  url = "www.youtube.com/channel/UC9Q8KmHEHhDl_2LSiQNGLaQ" # 大劉說說
  page_size = 3
  update_period = "4h"
  quality = "low"
  format = "video"
  clean = { keep_last = 5 }

  [feeds.ID19]
  url = "www.youtube.com/channel/UCaMih5WXqoXq7Hg0S_XJdOg" # Vedio Talk
  page_size = 3
  update_period = "8h"
  quality = "high"
  format = "video"
  clean = { keep_last = 2 }

  [feeds.ID20]
  url = "www.youtube.com/channel/UC5uh3zVGmvyQoks_LxBJ-5Q" # 中国青年郑国成
  page_size = 3
  update_period = "2h"
  quality = "low"
  format = "audio"
  clean = { keep_last = 10 }

  [feeds.ID21]
  url = "www.youtube.com/channel/UCPMqbkR35zZV1ysWGXJPW-w" # 新聞看點 李沐陽
  page_size = 3
  update_period = "4h"
  quality = "low"
  format = "video"
  clean = { keep_last = 6 }

  [feeds.ID22]
  url = "www.youtube.com/channel/UCThvTxQ-zF_ENso9LD_uHOQ" # 狗哥狗哥DogChinaShow
  page_size = 3
  update_period = "4h"
  quality = "low"
  format = "audio"
  clean = { keep_last = 5 }

  [feeds.ID23]
  url = "www.youtube.com/channel/UCuCELS5-48uP1plTxlOJZQQ" # 無色覺醒
  page_size = 3
  update_period = "4h"
  quality = "low"
  format = "audio"
  clean = { keep_last = 5 }

  [feeds.ID24]
  url = "www.youtube.com/channel/UC2cRwTuSWxxEtrRnT4lrlQA" # Mediastorm影视飓风
  page_size = 3
  update_period = "12h"
  quality = "high"
  format = "video"
  clean = { keep_last = 1 }

  [feeds.ID25]
  url = "www.youtube.com/channel/UCt5zpwa264A0B-gaYtv1IpA" # 美国之音中文网
  page_size = 3
  update_period = "1h"
  quality = "low"
  format = "video"
  clean = { keep_last = 10 }

  [feeds.ID26]
  url = "www.youtube.com/channel/UCiLtBk8dChPldOho8uTZHhQ" # 阿雷科技
  page_size = 2
  update_period = "24h"
  quality = "high"
  format = "video"
  clean = { keep_last = 1 }

  [feeds.ID27]
  url = "www.youtube.com/channel/UCT1YrR_CLpwosODYagzhm7Q" # Vincent Zhong
  page_size = 3
  update_period = "4h"
  quality = "high"
  format = "video"
  clean = { keep_last = 1 }

  [feeds.ID28]
  url = "www.youtube.com/channel/UCYfJG6cGfW84FVLuy7semEg" # Guan Video观视频工作室
  page_size = 3
  update_period = "4h"
  quality = "high"
  format = "video"
  clean = { keep_last = 3 }

  [feeds.ID29]
  url = "www.youtube.com/channel/UCf4YPrRO2clAH-zJcomJaGw" # 先看评测
  page_size = 3
  update_period = "4h"
  quality = "high"
  format = "video"
  clean = { keep_last = 2 }

  [feeds.ID30]
  url = "www.youtube.com/channel/UCAC204niCVvtDRDFYSiK7Cg" # 科技美学
  page_size = 3
  update_period = "4h"
  quality = "high"
  format = "video"
  clean = { keep_last = 1 }

  [feeds.ID31]
  url = "www.youtube.com/channel/UC6ISXkvMtP_Je1ITiYIJSSA" # FView Official Channel
  page_size = 3
  update_period = "4h"
  quality = "high"
  format = "video"
  clean = { keep_last = 1 }

  [feeds.ID32]
  url = "www.youtube.com/channel/UCc_eHNe78W0fLFOXswOdnzw" # 嘿丝科技官方频道
  page_size = 3
  update_period = "4h"
  quality = "high"
  format = "video"
  clean = { keep_last = 1 }

  [feeds.ID33]
  url = "www.youtube.com/channel/UC9v3JGut2Z1PxrXEpGzgEAA" # TESTV
  page_size = 3
  update_period = "4h"
  quality = "high"
  format = "video"
  clean = { keep_last = 1 }

  [feeds.ID34]
  url = "www.youtube.com/channel/UCq2RQ6j_e7dbEwnvQjCKMkA" # Yu Qian chat
  page_size = 3
  update_period = "4h"
  quality = "low"
  format = "video"
  clean = { keep_last = 2 }

  [feeds.ID35]
  url = "www.youtube.com/channel/UCM33VtveeEmfA6LC7tH30Xw" # 袁腾飞
  page_size = 3
  update_period = "4h"
  quality = "low"
  format = "video"
  clean = { keep_last = 2 }

  [feeds.ID36]
  url = "www.youtube.com/channel/UCnyPxKrdrXDjlBBucRAIuuA" # 波士顿自干五-圆脸传媒
  page_size = 3
  update_period = "1h"
  quality = "low"
  format = "video"
  clean = { keep_last = 2 }

  [feeds.ID37]
  url = "www.youtube.com/channel/UCJMEiNh1HvpopPU3n9vJsMQ" # 理科男士K一米
  page_size = 3
  update_period = "1h"
  quality = "high"
  format = "video"
  clean = { keep_last = 3 }



[database]
  badger = { truncate = true, file_io = true } # 数据库配置，无需改动

[downloader]
self_update = true # 自动更新下载程序

# 程序运行日志配置，无需改动
[log]
filename = "podsync.log"
max_size = 50 # MB
max_age = 30 # days
max_backups = 7
compress = true```

# Backlinks
## [2021-11-29~2021-12-03](<2021-11-29~2021-12-03.md>)
- [December 1st, 2021](<December 1st, 2021.md>)

