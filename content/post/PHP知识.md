---
title: PHP知识
tags: [IT/PHP]
cssclass:
image-auto-upload: true
date: 2022-10-18 19:43:16
lastmod: 2022-10-25 08:40:51
---
# PHP知识
## 使用变量调用类的方法
```php
$this->{'get'.$this->cacheType}($name, $default);
```