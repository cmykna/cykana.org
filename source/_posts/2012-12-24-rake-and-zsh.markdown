---
layout: post
title: "Rake and Zsh"
date: 2012-12-24 16:04
comments: true
categories: 
---
Found some [good advice](http://travisjeffery.com/b/2012/01/zshs-extended-glob-and-octopresss-new-post-script/) for getting zsh to play nice with rake. Turn off zsh's greedy globbing for just Rake by putting this in your .zshrc:
`alias rake="noglob rake"`
