**[>>>> English Version <<<<](README_EN.md)**


#### Table of contents
(toc is generated by [ghtoc](https://github.com/sk1418/ghtoc))
- [简介](#introduction)
- [依赖](#dependencies)
- [功能](#features)
- [安装](#installation)
- [使用](#usage)
	- [海外IP代理支持](#proxy-setting)
        - [获得中国代理服务器](#how-to-get-china-proxy)
- [Screenshots](#screenshots)
- [Change logs](#changelog)


## Introduction

zhuaxia 是一个基于命令行的虾米音乐 ( www.xiami.com 以下简称[虾])和网易云音乐( music.163.com 以下简称[易]) 多线程批量下载工具


**zhuaxia** 的开发调试环境:
- python 2.7.6


## Dependencies

- requests module
- mutagen module
- beautifulsoup4 module
- pycrypto module

## Features
- 自动识别解析URL. 目前支持：
	- [虾] 歌曲，专辑，精选集，用户收藏[todo], 歌手热门
	- [易] 歌曲，专辑，歌单，歌手热门
- 下载歌手热门歌曲:数量可配置(<=0 无限制 [虾]只下载第一页) ，默认Top10。 配置项: `download.artist.topsong`，需要艺人页面链接
- 支持包含音乐资源URL的文件作为输入进行批量下载. URL可以是[虾]和[易]混合, 可以不同音乐类型混合 (`-f` 参数)
- 当以文件作为输入批量下载时, 多线程(可配置线程池)解析URL
- 多线程(可配置线程池)下载歌曲
- [虾]支持以VIP账户登录下载高音质(320kbps) mp3, 并不消耗VIP的下载额度 (`-H` 选项)
- [易]支持下载高音质(320kbps) mp3 (`-H` 选项)
- 进度显示 (色彩高亮，终端宽度改变自动适应，总进度，下载线程进度...)
- mp3文件重命名, 更新mp3 meta信息，自动下载专辑封面, 专辑文本介绍(仅[虾])...等等
- [虾]配置项`china.proxy.http=ip:port` 来设置国内的代理服务进行解析和下载。详见："Usage -> 海外IP下载资源" 一节
- 加入实验性`-p`选项，尝试解决频繁请求被服务器ban的问题
- 中英文命令行界面. 配置项 `lang=en|cn` 默认中文(`cn`)
- 下载歌曲的同时下载歌词，保存为同名`lrc`文件 (`-l`选项)
- 从`v3.0.0`开始zhuaxia维护一个下载历史记录, 支持增量下载(`-i` 选项). 增量下载时, 曾下载过的歌曲将被忽略
- 支持下载历史的导出(`-e`),清空(`-d`)
- 所有下载完成时显示/保存本次下载详情



## Installation

Archlinux 用户, zhuaxia可以从AUR中获取

稳定版本(master branch)：

稳定版本：

	yaourt -S zhuaxia

最新git版本(bleeding branch):

	yaourt -S zhuaxia-git

其他用户:

	sudo python setup.py install

## Usage

- 配置文件， 第一次运行`zx`后， 在`$HOME/.zhuaxia/` 会有配置文件 `zhuaxia.conf` 配置参数有中文说明

- 使用：


		zhuaxia (抓虾) -- 抓取[虾米音乐]和[网易云音乐]的 mp3 音乐

		[CONFIG FILE:] $HOME/.zhuaxia/zhuaxia.conf
					   缺省配置文件会在第一次运行zhuaxia时自动生成

		[OPTIONS]
			-H : 首选HQ质量(320kbps),
				> 虾米音乐 <
					- 配置文件中需给出正确登录信箱和密码, 登录用户需拥有VIP身份
					- 用户需在xiami vip设置页面设置默认高音质
					- 此选项对不满足上两项情况无效，仍下载128kbps资源
				> 网易音乐 <
					-无需特殊要求,直接下载高音质资源


			-h : 显示帮助

			-l : 下载歌曲的lrc格式歌词

			-f : 从文件下载

			-i : 增量下载. zhuaxia依靠历史记录来判定一首歌是否曾被下载
				 曾经被下载过的歌曲将被略过. 判断一首歌曲是否被下载过靠3个属性:
				 song_id(虾米或网易的歌曲id), source (虾米/网易), quality (H/L)

			-e : 导出当前下载历史记录到文件
				 如果这个选项被使用, 其它选项将被忽略

			-d : 清空当前下载历史记录
				 如果这个选项被使用, 其它选项将被忽略. 
				 -e 和-d 选项不能同时使用

			-v : 显示版本信息

			-p : (实验性选项)使用代理池下载
				在下载/解析量大的情况下，目标服务器会对禁止频繁的请求，所以zhuaxia可以自动获取 代理来解析和下载资源。因为获取的代理速度/可靠性不一，下载可能会缓慢或不稳定。

		[USAGE]

			zx [OPTION] <URL>
				: 下载指定URL资源, 抓虾自动识别链接, 支持
					- [虾] 歌曲，专辑，精选集，用户收藏,艺人TopN
					- [易] 歌曲，专辑，歌单，艺人TopN
				例子：
				  zx "http://www.xiami.com/space/lib-song/u/25531126"
				  zx "http://music.163.com/song?id=27552647"

			zx [OPTION] -f <file>
				: 多个URL在一个文件中，每个URL一行。 URLs可以是混合[虾]和[易]的不同类型音乐资源。例子：

				  >$ cat /tmp/foo.txt
					http://music.163.com/artist?id=5345
					http://www.xiami.com/song/1772130322
					http://music.163.com/album?id=2635059
					http://www.xiami.com/album/32449
				  >$ zx -f /tmp/foo.txt

			Other Examples:

					下载歌曲和歌词:
						zx -l "http://music.163.com/song?id=27552647"

					增量下载, 并下载歌词
						zx -li "http://music.163.com/song?id=27552647"

					导出下载历史记录. 文件会被保存在配置文件设置的"download.dir"目录中
						zx -e

					清空所有下载历史记录
						zx -d
         

### Proxy setting

**海外IP下载资源**

xiami.com 和music.163都屏蔽了海外ip的资源下载请求。针对163的资源, zhuaxia 目前使用了一个还没检查IP的链接, 截止目前(2015-11-12), 扔可以直接下载.但是不保证长效。彻底解决xiami和163的海外下载问题，请看下面的选项：

在配置文件中添加（如果不存在的话）`china.proxy.http=ip:port`, (以前叫`xiami.proxy.http`) 可以让zhuaxia通过代理来解析和下载资源。
例如：

	china.proxy.http=127.0.0.1:8080

这里`ip:port`构成的http代理是国内的代理服务器。 如果你的机器已经是国内的ip，请注释或删除这个选项。

**为兼容用户已存在的配置文件,老的`xiami.proxy.http` 选项还可使用,代理将对xiami,163同时生效. 但建议修改配置文件使用新的名字. 新旧两个proxy选项不能同时使用.**

#### How to get China Proxy

获取国内代理的简单方法：

到http://proxy-list.org/ 搜索China的代理就好。

或者 

利用这个脚本 [cnProxy.py](https://github.com/sk1418/myScripts/blob/master/python/cnProxy.py) 获取按速度排名前N (默认5) 的中国代理服务器.

## Screenshots

- downloading (gif animation)
![progress](https://raw.github.com/sk1418/sharedResources/master/zhuaxia/progress.gif)

- parse input file
![file view](https://raw.github.com/sk1418/sharedResources/master/zhuaxia/fileParse.gif)

- parse url
![url view](https://raw.github.com/sk1418/sharedResources/master/zhuaxia/urlParse.png)

## Changelog

[Click to check Change logs](CHANGELOG.txt)



