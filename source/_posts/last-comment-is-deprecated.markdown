---
layout: post
title: "Rails 4.2.7.1 に更新した後に [DEPRECATION] last_comment is deprecated. が発生した"
date: 2016-08-23 13:37:27 +0900
comments: true
categories: ruby gem rails
description: "Railsを4.2.7.1にアップデートをしてrspecを実行すると、以下のエラーが発生するようになった。[DEPRECATION] last_comment is deprecated.  Please use last_description instead."
---

Railsを4.2.7.1にアップデートをしてrspecを実行すると、以下のエラーが発生するようになった。

```
$ bundle exec rspec spec
[DEPRECATION] `last_comment` is deprecated.  Please use `last_description` instead.
[DEPRECATION] `last_comment` is deprecated.  Please use `last_description` instead.
[DEPRECATION] `last_comment` is deprecated.  Please use `last_description` instead.
[DEPRECATION] `last_comment` is deprecated.  Please use `last_description` instead.
[DEPRECATION] `last_comment` is deprecated.  Please use `last_description` instead.
......................................................................................................

Finished in 2.33 seconds (files took 8.78 seconds to load)
102 examples, 0 failures
```

grepしてみたところ、rspecで使用していた。

```
$ hw last_comment vendor/bundle
vendor/bundle/ruby/2.2.0/gems/rspec-core-3.2.2/lib/rspec/core/rake_task.rb
84:        desc "Run RSpec code examples" unless ::Rake.application.last_comment
```

rspecをアップデートしたら出なくなった。

```
$ bundle update rspec-rails

$ bundle exec rspec spec
......................................................................................................

Finished in 2.33 seconds (files took 8.85 seconds to load)
102 examples, 0 failures
```
