---
layout: page
title: About
description: 有梦想的咸鱼
keywords: HuangHenry
comments: false #不接受评论
menu: 关于
permalink: /about/
---

我是 HuangHenry，码而生，码而立。

仰慕「优雅编码的艺术」。

坚信熟能生巧，做一条有梦想的咸鱼, Love OU。

## 联系

<url>
{% for website in site.data.social %}
<li>{{website.sitename}}:<a href="{{website.url}}" target="_blank">@{{website.name}}</a></li>
<!-- - {{ website.sitename }}：[@{{ website.name }}]({{ website.url }}) -->
  {% endfor %}
</url>

## Skill Keywords

{% for category in site.data.skills %}

### {{ category.name }}

<div class="btn-inline">
{% for keyword in category.keywords %}
<button class="btn btn-outline" type="button">{{ keyword }}</button>
{% endfor %}
</div>
{% endfor %}
