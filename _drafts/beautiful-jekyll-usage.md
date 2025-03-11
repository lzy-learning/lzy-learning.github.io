---
layout: post  # specified the layout current markdown file uses
title: "Usage of jekyll template: beautiful-jekyll"   # the title of the essay
subtitle: There's lots to learn!    # the descirption of current essay
tags: [test, beautiful-jekyll]
# cover-img: https://arturomoncadatorres.com/multimedia/images/jekyll_github/jekyll_github.png # large background picture displayed on the top of the essay
# thumbnail-img: https://raw.githubusercontent.com/github/explore/80688e429a7d4ef2fca1e82350fe8e3517d3494d/collections/github-pages-examples/github-pages-examples.png    # preview image of the essay on the article list page
# share-img: /assets/img/path.jpg # the preview image when the essay is shared to social media like Twitter
# gh-repo: daattali/beautiful-jekyll  # the github repository displayed in the page
# gh-badge: [star, fork, follow]  # whether display "Star", "Fork" and "Follow" button, which are all related to repo operation
# tags: [jekyll, github-pages]    # convenient for readers to search for articles based on tags
comments: true      # allow reader comment on this article
# mathjax: true   # whether enable MathJax, which is used to render mathematical formulas
author: Lin Zhiyang
---

The essay will introduce the basic knowledge of Jekyll, include its project structure, its command and how to use it to build a beautiful blog website. As I'm not familiar with Jekyll, the content below will not cover writing liquid to form a website from scratch. From the essay, you can learn the standard structure of Jekyll project, basic syntax of Liquid and some common configurations about Jekyll template [beautiful-jekyll](https://github.com/daattali/beautiful-jekyll)

### Jekyll project structure

When you run `jekyll new <site_name>`, the jekyll default template will be generated in current directory. But the files or directories under the generated folder are not complete for a standard Jekyll project. Normally, a Jekyll project would contains the following (The structure comes from official website of [Jekyll](https://jekyllrb.com/docs/structure/), and I add some content): 

```
.
├── _config.yml
├── _data
│   └── members.yml
├── _drafts
│   └── on-simplicity-in-technology.md
├── _includes
│   ├── footer.html
│   └── header.html
├── _layouts
│   ├── default.html
│   └── post.html
├── _posts
│   └── YEAR-MONTH-DAY-ESSAYTITLE.md
├── _sass
│   ├── _base.scss
│   └── _layout.scss
├── _site
├── .jekyll-cache
│   └── Jekyll
│       └── Cache
│           └── [...]
├── .jekyll-metadata
├── Gemfile
├── Appraisals
├── .jekyll-metadata
└── index.html # can also be an 'index.md' with valid front matter
```
{: .box-success}
This is a demo post to show you how to write blog posts with markdown. I strongly encourage you to [take 5 minutes to learn how to write in markdown](https://markdowntutorial.com/) - it'll teach you how to transform regular text into bold/italics/tables/etc.<br/>I also encourage you to look at the [code that created this post](https://raw.githubusercontent.com/daattali/beautiful-jekyll/master/_posts/2020-02-28-sample-markdown.md) to learn some more advanced tips about using markdown in Beautiful Jekyll.

**Here is some bold text**

## Here is a secondary heading

[This is a link to a different site](https://deanattali.com/) and [this is a link to a section inside this page](#local-urls).

Here's a table:

| Number | Next number | Previous number |
| :------ |:--- | :--- |
| Five | Six | Four |
| Ten | Eleven | Nine |
| Seven | Eight | Six |
| Two | Three | One |

You can use [MathJax](https://www.mathjax.org/) to write LaTeX expressions. For example:
When \\(a \ne 0\\), there are two solutions to \\(ax^2 + bx + c = 0\\) and they are $$x = {-b \pm \sqrt{b^2-4ac} \over 2a}.$$

How about a yummy crepe?

![Crepe](https://beautifuljekyll.com/assets/img/crepe.jpg)

It can also be centered!

![Crepe](https://beautifuljekyll.com/assets/img/crepe.jpg){: .mx-auto.d-block :}

Here's a code chunk:

~~~
var foo = function(x) {
  return(x + 5);
}
foo(3)
~~~

And here is the same code with syntax highlighting:

```javascript
var foo = function(x) {
  return(x + 5);
}
foo(3)
```

And here is the same code yet again but with line numbers:

{% highlight javascript linenos %}
var foo = function(x) {
  return(x + 5);
}
foo(3)
{% endhighlight %}

## Boxes
You can add notification, warning and error boxes like this:

### Notification

{: .box-note}
**Note:** This is a notification box.

### Warning

{: .box-warning}
**Warning:** This is a warning box.

### Error

{: .box-error}
**Error:** This is an error box.

## Local URLs in project sites {#local-urls}

When hosting a *project site* on GitHub Pages (for example, `https://USERNAME.github.io/MyProject`), URLs that begin with `/` and refer to local files may not work correctly due to how the root URL (`/`) is interpreted by GitHub Pages. You can read more about it [in the FAQ](https://beautifuljekyll.com/faq/#links-in-project-page). To demonstrate the issue, the following local image will be broken **if your site is a project site:**

![Crepe](/assets/img/crepe.jpg)

If the above image is broken, then you'll need to follow the instructions [in the FAQ](https://beautifuljekyll.com/faq/#links-in-project-page). Here is proof that it can be fixed:

![Crepe]({{ '/assets/img/crepe.jpg' | relative_url }})
