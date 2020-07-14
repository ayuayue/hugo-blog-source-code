---
title: "增加标签云代替原有标签和分类效果"
date: 2020-07-14T13:38:22+08:00
draft: false
tags: [前端]
categories: [前端]
---
### 更换`hugo`博客主题的原有标签
更换标签显示以及分类显示,使用 `highcharts` 的 `wordcloud` 来实现一个更好看的标签云,并增加点击事件,实现原来的点击效果.[官方案例](https://jshare.com.cn/demos/IOrsq7?hc-theme=grid-light).[英文文档](https://api.highcharts.com.cn/en/highcharts/series.wordcloud.html)

#### 按官方顺序将`js`文件导入或者使用`cdn`引入
本文使用官方提供的`https cdn`,注意`https`网站引用`http`会有问题

### 修改主题文件下的 `even/layouts/_default/terms.html` 文件
主要是判断`url`的`name`,分 `tags` 和 ` categories `两种情况进行处理
全部文件如下:
```html
{{- define "title" }}{{ T .Data.Plural }} - {{ .Site.Title }}{{ end -}}
{{- define "content" -}}
{{ $name := .Data.Plural -}}
{{ $terms := .Data.Terms.ByCount -}}
{{ $length := len $terms -}}
<script src="https://code.highcharts.com.cn/highcharts/8.1.2/highcharts.js"></script>
<script src="https://code.highcharts.com.cn/highcharts/8.1.2/modules/exporting.js"></script>
<script src="https://code.highcharts.com.cn/highcharts/8.1.2/modules/wordcloud.js"></script>
<script src="https://code.highcharts.com.cn/highcharts/8.1.2/modules/oldie.js"></script>
{{ if eq $name "categories" -}}
<div class="terms">
  <div class="terms-title">
    {{ if eq $length 0 -}}
    {{ T "zeroCategoryCounter" }}
    {{- else -}}
    {{ T "categoryCounter" $length }}
    {{- end }}
  </div>
    <div id=categories></div>
    <script>
      var terms = {{ $terms }};
      length = terms.length
      var str = "";
      for (i = 0; i < length; i++) {
        str += terms[i].Name + ","
      }
      var text = str;
      var data = text.split(/[,\. ]+/g)
        .reduce(function (arr, word) {
          var obj = arr.find(function (obj) {
            return obj.name === word;
          });
          if (obj) {
            obj.weight += 1;
          } else {
            obj = {
              name: word,
              weight: 1
            };
            arr.push(obj);
          }
          return arr;
        }, []);
      Highcharts.chart('categories', {
        //highcharts logo
        credits: { enabled: false },
        series: [{
          type: 'wordcloud',
          data: data,
          rotation: 0,//字体不旋转
          maxFontSize: 10,//最大字体
          minFontSize: 1,//最小字体
          style: {
            fontFamily: "微软雅黑",
            fontWeight: '500'
          }
        }],
        title: {
          text: ''
        },
        //点击事件方法
        plotOptions: {
          series: {
            cursor: 'pointer',
            events: {
              click: function (e) {
                // 单条数据
                var path = e.point.options.name;
                window.location.href = "/categories/" + path;
              }
            }
          }
        },
      });
    </script>
</div>
{{- else if eq $name "tags" -}}
<div class="terms">
  <div class="terms-title">
    {{ if eq $length 0 -}}
    {{ T "zeroTagCounter" }}
    {{- else -}}
    {{ T "tagCounter" $length }}
    {{- end }}
  </div>
  <div id=tags></div>
  <script>
    var terms = {{ $terms }};
    length = terms.length
    var str = "";
    for (i = 0; i < length; i++) {
      str += terms[i].Name + ","
    }
    var text = str;
    var data = text.split(/[,\. ]+/g)
      .reduce(function (arr, word) {
        var obj = arr.find(function (obj) {
          return obj.name === word;
        });
        if (obj) {
          obj.weight += 1;
        } else {
          obj = {
            name: word,
            weight: 1
          };
          arr.push(obj);
        }
        return arr;
      }, []);
    Highcharts.chart('tags', {
      //highcharts logo
      credits: { enabled: false },
      series: [{
        type: 'wordcloud',
        data: data,
        rotation: 0,//字体不旋转
        maxFontSize: 10,//最大字体
        minFontSize: 1,//最小字体
        style: {
          fontFamily: "微软雅黑",
          fontWeight: '500'
        }
      }],
      title: {
        text: ''
      },
      //点击事件方法
      plotOptions: {
        series: {
          cursor: 'pointer',
          events: {
            click: function (e) {
              // 单条数据
              var path = e.point.options.name;
              window.location.href = "/tags/" + path;
            }
          }
        }
      },
    });
  </script>
</div>

{{- end }}
{{- end }}
```