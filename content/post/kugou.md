---
title: "酷狗音乐爬虫"
date: 2021-02-24T14:08:30+08:00
lastmod: 2021-02-24T14:08:30+08:00
draft: false
keywords: [酷狗音乐爬虫]
description: "酷狗音乐爬虫"
tags: [go]
categories: [go]
---

![code](https://images.pexels.com/photos/169573/pexels-photo-169573.jpeg?auto=compress&cs=tinysrgb&dpr=2&h=750&w=1260)

### music

最近很少用 `deepin` 在线或者使用客户段听音乐了，喜欢下载到本地听那些熟悉的歌曲。

很多歌不是这个平台没版权就是那个要付费。也是顾虑到网易云9级用户的面子，不经常去其他平台听歌，在 `GitHub` 上有个项目，项目地址：https://github.com/nondanee/UnblockNeteaseMusic。可以只使用网易云的客户端听大部分平台的音乐，对于没有版权的歌曲会使用其他平台的 `api` 来播放。用着总算有个解决方案了。但是在 `linux` 客户端上，网易云程序没有设置代理的功能，而`linux` 上又要弄证书什么的，一个客户端就懒的折腾了，主要是折腾了几天没有很好的解决方案，就一直搁置了。

也是经常使用 `you-get` 的缘故，会经常把电影或视频下载下来，同时也支持很多国内视频与音乐平台，you-get 项目地址：https://github.com/soimort/you-get

`you-get` 使用起来也比较简单，将链接放进去就好了，但是国内如果不使用代理，速度十分的慢。关于代理的使用

```bash
you-get -x 127.0.0.1:7890 'https://www.youtube.com/watch?v=jNQXAC9IVRw'
```

对于视频来说每次找到一个视频，复制链接，下载。还算可以接受，但是对于音乐，只有短短的几分钟，每次要下新的歌曲都要进行打开浏览器搜索，复制链接。干脆自己写一个程序。

由于是打算做命令行程序，就是用 `Go` 来写吧，一个小程序，就不使用 `go mod` 来做了，直接一个 `main` 搞定。此项目地址：https://github.com/ayuayue/kugou

本来打算通过命令行来查询歌曲，返回歌曲的链接，然后配合 `you-get` 来进行下载，这样就多了一步，类似于使用管道符的方式。

又打算直接通过 `go` 来调用 `you-get` 来下载，但是这样前提需要安装 `you-get` 并且代理不好在程序中配置，只好配置临时或全局的终端代理。

最后打算直接在程序中完成歌曲的搜索，歌词及歌曲下载。这样就不依赖于外部的东西。

对于歌曲下载`api` 的获取，使用 `you-get` 是通过 `-d` 参数，开启调试。会显示出请求的地址以及音乐的地址，由此可以的到歌曲详情的 `api`

Api

1.  查询歌曲   http://msearchcdn.kugou.com/api/v3/search/song

    eg: http://msearchcdn.kugou.com/api/v3/search/song?keyword=花海

    | 参数    | 值类型 |
    | ------- | ------ |
    | keyword | 字符串 |

2.  歌曲详情   https://www.kugou.com/yy/index.php?r=play/getdata

    eg: https://www.kugou.com/yy/index.php?r=play/getdata?hash=xxxx&album_id=xxx&mid=123

    | 参数     | 值类型 |
    | -------- | ------ |
    | hash     | 字符串 |
    | album_id | 字符串 |
    | mid      | 123    |

有了这两个 `API` 剩下的就简单了。

开始编码

```go
package main

import (
	"encoding/json"
	"flag"
	"fmt"
	"io"
	"io/ioutil"
	"net/http"
	"net/url"
	"os"
)

type Req struct {
	Status  int    `json:"status"`
	Error   string `json:"error"`
	Data    Data   `json:"data"`
	Errcode int    `json:"errcode"`
}
type Data struct {
	Info []Info `json:"info"`
}
type Info struct {
	Hash       string `json:"hash"`
	AlbumID    string `json:"album_id"`
	SingerName string `josn:"singername"`
	SongName   string `json:"songname"`
}
type Song struct {
	Status  int      `json:"status"`
	ErrCode int      `json:"err_code"`
	Data    SongData `json:"data"`
}
type SongData struct {
	AudioName string `json:"audio_name"`
	Lyrics    string `json:"lyrics"`
	PlayURL   string `json:"play_url"`
}

var SearchApi = "http://msearchcdn.kugou.com/api/v3/search/song"
var SongApi = "https://www.kugou.com/yy/index.php?r=play"

func query() Req {
	var name string
	flag.StringVar(&name, "name", "", "歌曲名")
	flag.Parse()
	//fmt.Println(name)
	client, err := http.NewRequest(http.MethodGet, SearchApi, nil)

	if err != nil {
		panic("请求失败")
	}
	params := make(url.Values)
	params.Add("keyword", name)
	client.URL.RawQuery = params.Encode()
	//fmt.Println(client)
	req, err := http.DefaultClient.Do(client)

	defer func() {
		req.Body.Close()
	}()
	if req.StatusCode != 200 {
		panic("返回错误响应")
	}
	body, _ := ioutil.ReadAll(req.Body)
	r := Req{}
	json.Unmarshal(body, &r)
	//fmt.Printf("%#v",r)
	return r
}

func (r *Req) List() {
	if r.Status != 1 {
		panic(r.Error)
	}
	songs := r.Data.Info
	for k, song := range songs {
		fmt.Println(k+1, song.SongName, "--", song.SingerName)
	}
}
func (r *Req) GetLink(num int) []string {
	songApi := "https://www.kugou.com/song"
	songs := r.Data.Info
	hash := songs[num-1].Hash
	album_id := songs[num-1].AlbumID
	fmt.Printf("在线播放链接：%s/#hash=%s&album_id=%s\n", songApi, hash, album_id)
	return []string{
		hash, album_id,
	}
}
func (s *Song) Download() {
	if s.Status != 1 {
		panic("获取歌曲信息失败")
	}
	lyrics := s.Data.Lyrics
	fmt.Println("开始下载歌词")
	lrcFileName := fmt.Sprintf("%s.lrc", s.Data.AudioName)
	songName := fmt.Sprintf("%s.mp3", s.Data.AudioName)
	lf, err := os.Create(lrcFileName)
	if err != nil {
		panic(err)
	}
	defer func() { _ = lf.Close() }()
	_, err = lf.Write([]byte(lyrics))
	if err != nil {
		panic(err)
	}
	fmt.Println("下载歌词完成")
	fmt.Println("开始下载歌曲")
	req, err := http.Get(s.Data.PlayURL)
	if err != nil {
		panic("请求失败")
	}
	defer func() {
		req.Body.Close()
	}()
	if req.StatusCode != 200 {
		panic("返回错误响应")
	}
	sf, err := os.Create(songName)
	if err != nil {
		panic(err)
	}
	defer func() { _ = sf.Close() }()
	_, err = io.Copy(sf, req.Body)
	if err != nil {
		panic(err)
	}
}
func main() {
	var num int
	req := query()
	req.List()
	fmt.Println("请输入要选择的歌曲链接的序号：")
	fmt.Scanln(&num)
	params := req.GetLink(num)
	song := GetSong(params)
	song.Download()
}
func GetSong(params []string) Song {
	url := fmt.Sprintf("%s/getdata&hash=%s&album_id=%s&mid=123", SongApi, params[0], params[1])
	req, err := http.Get(url)
	if err != nil {
		panic("请求失败")
	}
	defer func() {
		req.Body.Close()
	}()
	if req.StatusCode != 200 {
		panic("返回错误响应")
	}
	body, _ := ioutil.ReadAll(req.Body)
	song := Song{}
	json.Unmarshal(body, &song)
	return song
}

```

编译(我本地是 linux 环境)

```bash
# linux
go build -o path ./main.go #path 路径
# windows 
CGO_ENABLED=0 GOOS=windows GOARCH=amd64 go build -o path main.go
```

