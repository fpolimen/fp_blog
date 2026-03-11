---
layout: post
title:  "Welcome to Jekyll!"
date:   2020-07-21 07:18:50 +0200
categories: jekyll update
---

I have been meaning to start a technical blog for a while. The trigger was a conversation about serverless architectures and functional programming that made me realise I had a coherent set of ideas worth writing down.

The choice fell on [Jekyll](https://jekyllrb.com/) for a simple reason: this blog is itself an experiment in serverless thinking. A static site generator produces immutable artefacts — pages — that are deployed once and served without any server-side state. No database, no session, no application server. Pure functions from source to HTML.

## How Jekyll works

A Jekyll site is a collection of Markdown files and Liquid templates. Running `bundle exec jekyll serve` starts a local web server and watches for changes:

```bash
bundle install
bundle exec jekyll serve
```

Posts live in the `_posts` directory and must follow this naming convention:

```
YEAR-MONTH-DAY-title.MARKUP
```

Front matter at the top of each file sets metadata — layout, title, date, categories:

```yaml
---
layout: post
title:  "My post"
date:   2020-07-21 07:18:50 +0200
categories: jekyll
---
```

Jekyll also supports syntax highlighting out of the box via Rouge. Here is a Ruby example:

{% highlight ruby %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}

## What this blog is about

The posts that will follow explore a single thread: the idea that **serverless computing and functional programming are natural companions**, and that this pairing has concrete consequences for how we deploy machine learning models as production services.

The next post picks up a vision that HP articulated in the late 90s and asks what it looks like today.

[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
