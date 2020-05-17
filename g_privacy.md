---
layout: page
title: 隐私极客
permalink: /privacy/
---

***

**隐私极客，Privacy Geek。**

关于隐私，「[黑客伦理与信息时代精神](https://book.douban.com/subject/1071093)」写到：「言论自由和隐私权一直是很重要的黑客理想，网络的发展也一直与此一致。隐私不只是一个伦理问题，它还是一个技术问题」。

**希望从今以后，隐私不会再只是茶余饭后一笑而过的谈资。**

读者有五个渠道订阅本专栏，第一是本博客，RSS 订阅点击 [此处](https://dbarobin.com/feed.xml)；第二是公众号，微信搜索「隐私极客」；第三是在线小册，[https://privacy.dbarobin.com](https://privacy.dbarobin.com)；第四是 Telegram Channel，名叫 [隐私极客](https://t.me/privacygeek)；第五是 Mixin Channel，下载安装 [Mixin Messenger](https://mixin.one/messenger) 之后，访问 [https://mixin.one/codes/27ca9fb0-bbae-4260-ad37-97ec9f7fcbeb](https://mixin.one/codes/27ca9fb0-bbae-4260-ad37-97ec9f7fcbeb) 订阅。博客以文章为主，Telegram Channel 内容多样化且碎片化（图片、音频、视频、文字、链接等），Mixin Channel 作为同步渠道。归档页面点击 [此处](https://dbarobin.com/privacy/)。

#### 归档
***

<ul class="tags-box">
{% if site.posts != empty %}
    {% for tag in site.tags %}
        {% if tag[1].size > 1%}
            {% if tag[0] == 'Privacy' %}
                {% for post in tag[1] %}
                    <time datetime="{{ post.date | date:"%Y-%m-%d" }}">{{ post.date | date:"%Y-%m-%d" }}</time> &raquo;
                    <a href="{{ site.baseurl }}{{ post.url }}" title="{{ post.title }}">{{ post.title }}</a><br />
                {% endfor %}
            {% endif %}
        {% endif %}
    {% endfor %}
{% else %}
    <span>No posts</span>
{% endif %}
</ul>

<p class="post-meta">
   <span id="busuanzi_container_page_pv">
      By Robin Wen, Viewd <span id="busuanzi_value_page_pv"></span> times.
   </span>
</p>