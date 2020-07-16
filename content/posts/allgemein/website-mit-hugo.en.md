---
title: "Building a Website with Hugo -- Basics"
date: 2020-05-19T20:20:55.280Z
draft: false
toc: true
tocNum: false
tags:
- Hugo
- Website
---

This blog is created with [Hugo][1]. In this post I tell you what it's all about and why I chose this method.

## What is Hugo?

Hugo claims to be the world's fastest framework for creating static websites.
Hugo is programmed in **Go** and available for all common operating systems. 

The articles are written in **Markdown** and then &ldquo;compiled&rdquo; to create the HTML pages. The appearance is determined via **Themes**.

This makes the workflow comparable to that of $\LaTeX$.

## Why Hugo?

With most content management systems like Wordpress, Drupal, Typo3, etc. it always bothered me that you have to set up a more or less powerful backend.
You always need PHP or Python and a database, even if you just want to write a simple blog.

On the [Self Hosted][2] Podcast I then heard about Hugo.

## How to get started?

### Preparations
There are various installation methods for Hugo. I downloaded the program as a ZIP file on my Windows computer, on Linux I installed it with Snap. Details can be found on the [Hugo website][3].

It's not mandatory, but using Git makes it a little easier. Therefore, in this article I assume that you have installed Git and have a Github account.

### Create Site
The basic structure of the page is created with the following command on the console:

```console
hugo new site meinblog
```

Hugo creates a subdirectory with the name `meinblog` and this content:

```console
archetypes/
config.toml
content/
data/
layouts/
static/
themes/
```

Next you should choose a theme from the [Hugo Theme List][4]. I tried some and finally decided on the MemE theme. The easiest way to install is with Git as described in [README][5].
You have to adapt the `config.toml` to your own needs. Another article on this topic follows.

### Create Article
Articles are created in the `content` directory as .md files. You can optionally create subfolders as categories. You can create the file manually or use this Hugo command:

```console
hugo new posts/my-first-post.md
```

The advantage over manual creation is that a frontmatter is also created. This is the configuration area of the article where appearance and behavior can be customized. Another article on this topic follows.

At this stage you can start the integrated server and display the (empty) page:

```console
hugo server -D
```

The option `-D` at this point is important so that articles marked as draft are also created and displayed.

Now you can write your articles. If the server is running in the background, the page is automatically regenerated and updated in the browser as soon as you change and save a file.

### Publish the Site
When you are finished with your articles, you call `hugo` without any other options. This creates the finished website with all necessary files in the `public` subdirectory. This directory can now be hosted on any web server. The workflow for hosting as a github.io page is shown on the [Hugo website][6].

[1]: https://gohugo.io/ "Hugo"
[2]: https://selfhosted.show/ "Self-Hosted Podcast"
[3]: https://gohugo.io/getting-started/installing "Hugo Installation"
[4]: https://themes.gohugo.io/ "Hugo Themes List"
[5]: https://github.com/reuixiy/hugo-theme-meme "MemE Readme"
[6]: https://gohugo.io/hosting-and-deployment/hosting-on-github/ "Hosting-on-Github"