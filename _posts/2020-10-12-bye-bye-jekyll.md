---
layout: post
title:  "Bye Bye Jekyll"
date:   2020-10-12 07:18:50 +0200
categories: generators
---

After a few months with Jekyll, I decided to move to [Hugo](https://gohugo.io/).

This is not a criticism of Jekyll. Jekyll works well, has a mature ecosystem, and integrates neatly with GitHub Pages. For someone coming from a Ruby background it is probably the natural choice. But a few things accumulated into a decision.

# Why the switch

## Build speed

Jekyll is written in Ruby and builds sequentially. On a small blog like this one the difference is imperceptible. But I work on larger documentation sites and the gap is real: Hugo builds in milliseconds; Jekyll can take tens of seconds on a site with hundreds of pages. Developing with a fast feedback loop matters.

## A single binary

Hugo ships as a single compiled binary with no runtime dependencies. Installing it means downloading one file. Jekyll requires Ruby, Bundler, and a Gemfile — which works fine on a developer machine but adds friction in CI environments and on machines where Ruby is not the primary language.

## Go templates vs Liquid

Liquid is simple and readable. Go templates are more powerful and more explicit about what they are doing. After spending time in both, I find Go templates easier to reason about for complex layouts — even if the syntax is initially less approachable.

# What carries over

The mental model is identical. Both generators use:

- Markdown content files with YAML front matter
- A layouts/templates layer that wraps content
- A static assets directory
- A configuration file at the root

Migrating content from Jekyll to Hugo is mostly mechanical: adjust the front matter keys, move files to the expected directories, and replace Jekyll-specific Liquid tags with their Hugo equivalents.

# What this means for the blog

The posts published here remain as they are. The migration will produce a Hugo version of the site with the same content and a cleaner theme. The Jekyll source stays in the repository as a reference.

The broader point is consistent with the theme of this blog: the right tool is the one that gets out of the way fastest. Jekyll was the right tool to start. Hugo is the right tool to continue.
