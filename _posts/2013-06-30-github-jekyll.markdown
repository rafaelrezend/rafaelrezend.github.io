---
layout: post
title: Journal = GitHub + Jekyll
---

I've been using [GitHub Pages](http://pages.github.com/) as a personal webpage for quite some time already when, very recently, a friend of mine presented me a blog deployed using Jekyll. The setup is very easy, as seen in [this post](http://www.ksornberger.com/blog/blogging-with-jekyll-and-github/) from Kevin Sornberger, and content can be added in a straightforward manner afterwards.

My [own repository](https://github.com/rafaelrezend/rafaelrezend.github.io) may be a good reference of a very simplistic blog. However, HTML and CSS can do wonders(!!!), so you are not limited to so simple view.

Specifically, the structure required to present this blog is the following (so, just ignore the folders which are not listed below):

	|----_includes
	|        '----post_header.html
	|----_layouts
	|        |----layout.html
	|        '----post.html
	|----_posts
	|        '----2013-06-31-this-blog.markdown
	|----stylesheets
	|        '----journal.css
	|----_config.yml
	'----journal.html

As an example of how simple it works, the `journal.html` file contains the following header:

	---
	layout: layout
	title: "rafaelrezend"
	---

Which mean that this page will be generated using the layout pointed out to `_layouts/layout.html`.
Similarly, every post uses the layout at `_layouts/post.html`.

The date and permalink are given by the filename itself: `2013-06-31-this-blog.markdown`

Finally, new content can be simply created using markdown language and pushed to GitHub. The deployment is automatic, since GitHub is running Jekyll underneath.

More on this topic can be found in [GitHub help](https://help.github.com/articles/using-jekyll-with-pages) and [Jekyll website](http://jekyllrb.com/docs/github-pages/).

I hope it is useful!
