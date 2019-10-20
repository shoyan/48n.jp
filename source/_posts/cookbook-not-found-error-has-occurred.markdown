---
layout: post
title: "cookbook_fileリソースでCookbookNotFoundが発生した"
date: 2016-04-18 13:43:40 +0900
comments: true
categories: Chef
---
cookbook_fileでCookbookNotFoundというエラーがでた。  
しかし、どこをどうみても合っているようにしか見えず、2時間ほどハマった。

エラーは以下。


```
  * cookbook_file[/etc/nginx/nginx.conf] action create

    ================================================================================
    Error executing action `create` on resource 'cookbook_file[/etc/nginx/nginx.conf]'
    ================================================================================

    Chef::Exceptions::CookbookNotFound
    ----------------------------------
    Cookbook cookbooks not found. If you're loading cookbooks from another cookbook, make sure you configure the dependency in your metadata

    Resource Declaration:
    ---------------------
    # In /var/chef/cache/cookbooks/cookbooks/recipes/default.rb

     25: cookbook_file '/etc/nginx/nginx.conf' do
     26:   source 'nginx.conf'
     27:   owner 'root'
     28:   group 'root'
     29:   mode '0755'
     30: end
     31:

```

原因はmetadata.rbの名前。  
cookbooks/site という構成でcookbookを作成しているのだが、cookbooks/site/metadata.rbのnameが’cookbooks'となっていた。  
これによりcookbook_fileリソースがcookbooksというcookbookを参照していたが、そんなものはないのでCookbookNotFoundエラーが発生していた。  
nameを'site'と変更してやることでうまくいくようになった。
