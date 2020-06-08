# App Explained Blog

To build the blog locally:

```
bundle exec jekyll serve
```

This will be locally accessible on:
http://localhost:4000

## Structure

New blog entries will be in the `_posts` directory. Each blog entry is a markdown file with the follows the following file name convention:

```
YEAR-MONTH-DAY-TITLE.markdown
```

Code snippets will be highlighted with the highlight tag:

```
{% highlight kotlin %}
fun sayHello(name: String): String {
    return "Hello $name"
}
{% endhighlight %}
```
