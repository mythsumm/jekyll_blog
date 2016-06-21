---
layout: default
---
{% for post in site.posts limit: 1 %}
<div>
  <ul class="listing">
  <article class="content">
    <section class="title">
      <h2><a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a></h2>
    </section>
    <section class="meta">
    <span class="time">
      <time datetime="{{ post.date | date:"%Y-%m-%d" }}">{{ post.date | date:"%Y-%m-%d" }}</time>
    </span>
    {% if post.tags %}
    <span class="tags">
      {% for tag in post.tags %}
      <a href="/tags.html#{{ tag }}" title="{{ tag }}">#{{ tag }}</a>
      {% endfor %}
    </span>
    {% endif %}
    </section>
    <section class="post">
    {{ post.content }}
    </section>
    </article>
  </ul>

  <ul class="listing main-listing">
    <li class="listing-seperator"><a href="{{ site.baseurl }}/archive.html">Long long ago</a></li>
  </ul>
</div>
<script id="dsq-count-scr" src="//ethanliao.disqus.com/count.js" async></script>
<div id="disqus_thread"></div>
<script>
    /**
     *  RECOMMENDED CONFIGURATION VARIABLES: EDIT AND UNCOMMENT THE SECTION BELOW TO INSERT DYNAMIC VALUES FROM YOUR PLATFORM OR CMS.
     *  LEARN WHY DEFINING THESE VARIABLES IS IMPORTANT: https://disqus.com/admin/universalcode/#configuration-variables
     */

    var disqus_config = function () {
        this.page.url = "{{ site.url }}{{ post.url }}";  // Replace PAGE_URL with your page's canonical URL variable
        this.page.identifier = "{{ post.id }}"; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
    };

    (function () {  // DON'T EDIT BELOW THIS LINE
        var d = document, s = d.createElement('script');

        s.src = '//ethanliao.disqus.com/embed.js';

        s.setAttribute('data-timestamp', +new Date());
        (d.head || d.body).appendChild(s);
    })();
</script>
<noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript" rel="nofollow">comments
    powered by Disqus.</a></noscript>
{% endfor %}