---
layout: post
title: "ActionMailerの添付ファイルをRspecでテストする"
date: 2016-06-08 18:41:16 +0900
comments: true
categories: rails
description: "ActionMailerで添付ファイルを送るようにしたのですが、そのテストをするときの情報があまりなかったのでまとめました。Rspecを使ってテストするサンプルコードを紹介します。"
---

ActionMailerで添付ファイルを送るようにしたのですが、そのテストをするときの情報があまりなかったのでまとめました。

以下のようにテストしました。


```ruby
RSpec.describe AppMailer, type: :mailer do

  before(:all) do
    Archive::Zip.archive(
      "tmp/app.zip",
      "README.md")
  end

  after(:all) do
    FileUtils.rm "tmp/app.zip"
  end

  let(:mail) do
    AppMailer.send_email(zip_path)
  end

  it 'assings the attachment file' do
    attachment = mail.attachments[0]
    expect(attachment).to be_a_kind_of(Mail::Part)
    expect(attachment.content_type).to be_start_with('application/zip')
    expect(attachment.filename).to eq "app.zip"
  end
end

```

まず、before(:all)でテストに使うzipファイルを作成します。  
zipファイルは[archive-zip](https://github.com/javanthropus/archive-zip)を使って作成しています。  
アーカイブするファイルは何でもよいですが、この記事ではREADME.mdとしています。  
テストで作成したzipファイルはafter(:all)で消しています。  
mail.attachments[0]に添付ファイルが入っているので、その種類やファイル名を確認しています。

## 参考リンク

- [rails-how-to-test-that-actionmailer-sent-a-specific-attachment](http://stackoverflow.com/questions/14286179/rails-how-to-test-that-actionmailer-sent-a-specific-attachment)
