---
layout: page
title: Archive
---

{% for post in site.posts %}
  * {{ post.date | date_to_string }} &raquo; [ {{ post.title }} ]({{ post.url }}) []({{ post.url }}#disqus_thread)
{% endfor %}

<script type="text/javascript">
    var disqus_shortname = 'benlindsey';
    (function () {
        var s = document.createElement('script'); s.async = true; s.type = 'text/javascript';
        s.src = '//' + disqus_shortname + '.disqus.com/count.js';
        (document.getElementsByTagName('HEAD')[0] || document.getElementsByTagName('BODY')[0]).appendChild(s);
    }());
</script>
