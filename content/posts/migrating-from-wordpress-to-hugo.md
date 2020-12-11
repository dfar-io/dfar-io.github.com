---
title: "Migrating From WordPress to Hugo"
date: 2020-12-03T19:10:30-05:00
draft: false
---

I decided to clean up my website and move to a static site for a few reasons:

* Start using MarkDown more frequently, as I've grown to really like it for documentation.
* Cut down on costs of hosting the WordPress site.
* Simplify the overall design.

In the migration process, I had to do a few things to get the site fully migrated over.

## Deploying to Github Pages using Github Actions

First, I created the Hugo site by following the
[Quick Start Guide](https://gohugo.io/getting-started/quick-start/) at Hugo,
picking a theme, then committing the changes to my Github in a repo named <username>.github.io. _I also added a
_.gitignore_ file that wasn't included._

After that, I created a Github Action workflow using
[this Github Action](https://github.com/peaceiris/actions-hugo), deployed the
site using the workflow, then enabled Github Pages in the repo settings
(selecting the _gh-pages_ branch generated from the CI/CD process). At this point,
you can verify the site is up using the repo name above as the URL.

Finally, I set up DNS records for my apex domain (dfar.io without a www) using
[this guide](https://docs.github.com/en/free-pro-team@latest/github/working-with-github-pages/managing-a-custom-domain-for-your-github-pages-site#configuring-an-apex-domain)
and set up the domain in the repo settings.

## Copying Data from WordPress to Hugo

To migrate my content, I installed the [WordPress to Hugo Exporter](https://github.com/SchumacherFM/wordpress-to-hugo-exporter), exported out the data, and added it to my `content/` folder. I had to go through and fix a decent amount of the posts, but overall was pretty quick to get years of blog posts converted over.