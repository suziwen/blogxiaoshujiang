---
title: 2017-9-17 关于 Evernote(印象笔记) 停止使用 Developer Token,小书匠客户端不能绑定的解决方案
tags: [Developer Token, 印象笔记,小书匠]
createDate: 2017-9-17
updateDate: 2017-9-17
grammar_cjkRuby: true
slug: /evernote_deprecated_developer_token_soluction
---

[toc!?direction=lr&root=evernote/印象笔记]

## 问题

由于 Evernote(印象笔记) 停止使用 Developer Token, 造成小书匠客户端用户无法再申请到新的 token,进而不能选择 Evernote(印象笔记)做为第三方存储

## 解决方案

1. 访问 http://markdown.xiaoshujiang.com
2. `小书匠主按钮>绑定>Evernote(印象笔记)` 
3. 按照提示绑定成功后，点击 Evernote(印象笔记)条目右边相应的`设置`按钮 ![enter description here][1] <i class="fa fa-gears"></i>,会在弹出的界面里显示当前绑定使用的 token 
    ![enter description here][2]
4. 将该 token 值复制粘贴到小书匠客户端 Evernote(印象笔记) 绑定里需要填入 token 的输入框内，完成客户端绑定

## 注

1. 注意区分 Evernote 和 印象笔记，不要把 Evernote 的 token 放入印象笔记里。
2. 通过 web 申请的 token 因为权限比较小，会有一些限制，比如保存文件的大小，或者 api 调用频率


  [1]: ./images/1505579634678.jpg
  [2]: ./images/1505579320962.jpg