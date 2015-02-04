---
layout: post
title: Migrating to Jekyll
date: 2015-01-20
categories:
- blog
published: true
---

For a while I've been working on migrating my blog over from Wordpress to [Github Pages](https://pages.github.com/) and [Jekyll](http://jekyllrb.com/). 

## What
So, Jekyll is a static site generator written in Ruby. I uses a template engine called Liquid which supports a number of markdown flavors, as well as HTML. You can therefore write your blog posts in HTML or markdown, and then make Jekyll generate a static HTML page for you.

## Why
A number reasons is behind this decision. I was really never that happy with my Wordpress solution, I had a hard time with the themes. Actually I never found a theme that I really liked. So my reasons to migrate can be outlined in the following bullets.

- I really had a hard time working with the Wordpress themes, I couldn't find one that fitted my needs and I found customizations hard.
- I'm a markdown lover. It really fits my needs. I feel much more productive writing beautiful posts and notes using markdown. I try to use it everywhere I can. I recently got markdown support in Confluence from Atlassian. We use Confluence for documents at work.
- I like that I can sit down and write my blog posts in my favorite markdown editor, currently [MarkdownPad](http://www.markdownpad.com/), and just git commit and push it to my blog.
- One of the things that was really hard for me when using Wordpress, was how code is rendered. I tried different approaches, but neither seemed to fit my needs.
- All the themes I tried out, did not render nicely on mobile or tablet.

So therefore I migrated to GitHub Pages. 

## How
Basically I followed [Hadi Hariri's guide](http://hadihariri.com/2013/12/24/migrating-from-wordpress-to-jekyll/). Although Hadi's post is based on installing Jekyll on OSX (Linux and Unix is also supported out of the box), and I wanted to install this on Windows, his guide is good starting point. 

To install Jekyll on Windows I followed [Julian Thilo's guide](http://jekyll-windows.juthilo.com/). Once you're done with Hadi's and Julian's guides, you have all your blog posts in the `_posts` folder as HTML.

I then spend some time finding a [Jekyll theme](http://jekyllthemes.org/) that would cover most of my needs. At this point I could start tweaking the `_config.yml` and the templates in `_layout`. You can use your HTML and CSS skills to modify and tweak your blog to look exactly as you would like. 

I write my blog posts in markdown, and the Liquid templates takes care of how it is rendered. 

## Done?
No. I'm still not completely satisfied with the looks of the blog. I'm still in the progress of changing bits and pieces. One of the things I would really like, is an archive feature, to where I can reference for specific categories. I also need to change the landing page. I don't like the current 'list' view. There are other more or less advanced changes that is would like to apply.

Throw me a comment of what you think about the new layout of my blog. 