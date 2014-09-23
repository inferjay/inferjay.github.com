---
layout: post
title: "keytool 错误 java.io.IOException: incorrect AVA format"
date: 2014-09-23 19:45:35 +0800
comments: true
categories: 
---

今天在打包的时候碰到了 `keytool 错误 java.io.IOException: incorrect AVA format` 错误，还以为是混淆规则加的有问题导致的，看日志窗口也没有任何错误日志，只好请出Google大神，Google的一下错误信息，发现是有由于在创建Keystore时填写的 `First and Last Name` 、`Organizational Unit` 、`Organization` 、 `City or Locality` 、`State or Province` 等信息中是不允许有 `,` 符号的，将这些信息中的逗号去掉后就可以了。

![image](/images/notes_images/incorrect-AVA-format-error.png "incorrect AVA format Error")