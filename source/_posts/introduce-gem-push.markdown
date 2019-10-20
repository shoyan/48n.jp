---
layout: post
title: "RubyGems.orgに反映させるRakeタスクの紹介"
date: 2016-10-25 14:17:29 +0900
comments: true
categories: ruby gem
description: "Gemを更新した場合はRubyGems.orgに反映させる手続きが必要だ。Gemを更新することはそんなに多くないので（私の場合）よく手順を忘れてしまう。毎回手順を探すのはストレスなので以下のようなRakeタスクを作成した。"
---

Gemを更新した場合はRubyGems.orgに反映させる手続きが必要だ。
Gemを更新することはそんなに多くないので（私の場合）よく手順を忘れてしまう。
毎回手順を探すのはストレスなので以下のようなRakeタスクを作成した。

```ruby
require "bundler/gem_tasks"
require "rspec/core/rake_task"

RSpec::Core::RakeTask.new(:spec)

task :default => :spec

namespace "gem" do
  desc 'Build a gem package'
  task :build do
    sh "gem build your_gem_name.gemspec"
  end

  desc 'Push a gem package'
  task :push do
    Rake::Task["gem:build"].execute
    sh "gem push your_gem_name-#{YourGemName::VERSION}.gem"
  end
end
```

公開する時は以下のタスクを実行すればいい。

```
$ rake gem:push
```

以下は実際のソースだ。
参考にしてほしい。

- https://github.com/shoyan/git_find_committer/blob/master/Rakefile
