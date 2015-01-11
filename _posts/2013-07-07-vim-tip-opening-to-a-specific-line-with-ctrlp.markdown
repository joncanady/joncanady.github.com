---
layout: post
title: "Vim Tip: Opening to a Specific Line with CtrlP"
categories: software
---

If you're using Vim to write your software, you should definitely be using
[CtrlP](http://kien.github.io/ctrlp.vim/), at the *very least* as a fuzzy file
finder.

Now, if you're clever, you might do something like "read the docs to understand
your tools better".  Since I'm not, it took me a while to figure this one out! Say
you're opening a file `request.rb` and you're going to jump directly to line 24.
Type `request.rb:24` in the CtrlP buffer and it'll take you there automatically.

Tiny tip, but it'll save a little time during long debug sessions.
