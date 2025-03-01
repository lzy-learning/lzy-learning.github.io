---
layout: post    # specified the layout current markdown file uses
title: "Setting Up a Jekyll Blog on Github Pages: A Beginner's Guide"    # the title of the essay
subtitle: "Installing Ruby, Jekyll, and Getting Your Blog Online"    # the descirption of current essay
cover-img: https://arturomoncadatorres.com/multimedia/images/jekyll_github/jekyll_github.png # large background picture displayed on the top of the essay
thumbnail-img: https://raw.githubusercontent.com/github/explore/80688e429a7d4ef2fca1e82350fe8e3517d3494d/collections/github-pages-examples/github-pages-examples.png    # preview image of the essay on the article list page
share-img: /assets/img/path.jpg # the preview image when the essay is shared to social media like Twitter
# gh-repo: daattali/beautiful-jekyll  # the github repository displayed in the page
# gh-badge: [star, fork, follow]  # whether display "Star", "Fork" and "Follow" button, which are all related to repo operation
comments: true  # allow reader comment on this article
mathjax: true   # whether enable MathJax, which is used to render mathematical formulas
tags: [jekyll, github-pages]    # convenient for readers to search for articles based on tags
author: Lin Zhiyang # the author anme
---

This is my first time writing a blog--and even more challenging, writing in English. I'd like to share my expreience installing ruby and Jekyll on Windows and setting up a blog site using Github Pages. 

### Installing Ruby on Windows

Ruby is a popular programming language for building web applications, known for its simplicity and ease of use. If you're working with Jekyll, it's better to install an older version of Ruby, like `ruby 3.0.5`, since many Jekyll gems are not compatible with newer versions, and a lot of Jekyll templates are desigend with older version in mind. 

You can download the Ruyb installer from [rubyinstaller.org](https://rubyinstaller.org/), which is tailered for Windows. Once installed, be sure to check the `Run 'ridk install'` option, which installs MSYS2 that provides essential compilers and libraries for building gems with native extensions. If you accidentally skip this step, don't worry! You can always run it later in PowerSHell with administrator privileges.

### Installing Jekyll and bundler

After installing ruby, it's a good idea to verify the environment variables are correctly set up. Simply run `ruby -v` or `gem -v` in the command prompt to check if `path/to/ruby/bin` has been added to your system's search path.

Once confirmed, install Jekyll and Bundler by running:

```shell
gem install bundler jekyll
```

`Bundler` is a gem that helps manage your Ruby project dependencies, while `Jekyll` is a well-known static site generator used to create simple, fast and customizable websites. Here are some essential commands you'll likely use:
1. `bundle install`: Installs all the gems listed in your `Gemfiles`.
2. `bundle exec <command>`: Runs commands within the context of your installed bundle.
3. `jekyll new <project name>`: Creates a new Jekyll site using a template.
4. `jekyll serve`: Starts a local server at 'http://localhost:4000`, allowing you to preview your site.
5. `jekyll build`: Generates static site files (HTML, CSS, JS, etc.) from your content and template.

I did not encounter any issues while installing Bundler and Jekyll, but if you run into errors, I recommand checking your internet connection or trying a different Ruby version. 

### Setting up a static website

If you are familiar with Jekyll or Liquid (Jekyll's template language), you might prefer building a custom site from scratch. But if you've new to Jekyll and just want to get started quickly, the eariest approach is to fork a pre-designed template from [Jekyll Themes](https://jekyllthemes.io/free). I went with latter option since I was complete beginner.

I chose the [beautiful-jekyll](https://github.com/daattali/beautiful-jekyll) template because of its simplicity and elegant design. To get started, fork the repository and clone it to your local machine. Then, navigate to the project directory and install dependencies with:

```shell
bundle install
```

This particular template doesn't require many gems, so don't worry about it taking up to much disk space. Once the installation is complete, you can launch the site locally using:

```shell
bundle exec jekyll serve
```

This commands first builds the site and generates static files inside `_site` directory. It then starts a local server at `http://127.0.0.1:4000`, which you'll see displayed in the terminal. Open this link in your browser and you'll be able to view your site.

However, I did run into an issue where some resources weren't loading correctly due to missing dependencies, If you're using `beautiful-jekyll`, you may need to add `tzinfo` and `tzinfo-data` to your `Gemfiles`(located in the root directory of the project):

```
gem 'tzinfo'
gem 'tzinfo-data'
```

These gems handle time zone information, and they may not be included by default in `ruby 3.0.5`.

### Hosting on Github Pages

For detail guide on Github Pages, check out the [Github Pages tutorial](https://docs.github.com/en/pages/getting-started-with-github-pages/creating-a-github-pages-site). I'll just cover the basic here.

There are two ways to set up a Github Pages site. I chose the simpler method: creating a repository named `username.github.io` and moving my Jekyll files into it. A few seconds later, my site was live at `https://username.github.io`.

Whenever you update your blog, simply test it locally and push the changes to your remote repository:

```shell
git add .
git commit -m "Updated blog post"
git push origin main
```

### Final Thoughts

Attempting to share my experience through writing is exciting, but my limited English skills make it quit time-consuming. My expressions might not be perfect, but I hope this process helps me improve both my technical abilities and my English writing skills.

Happy blogging! 🎉