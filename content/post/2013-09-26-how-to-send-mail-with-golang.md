---
title: '#golang メールを送信するには'
author: kwmt
layout: post
date: 2013-09-26
url: /index.php/2013/09/26/how-to-send-mail-with-golang/
pdrp_attributionLocation:
  - end
categories:
  - golang
tags:
  - 1.2 rc1
  - golang
  - Go言語
  - net/smtp

---
## 最初に

Go言語でのメール送信方法について、[SMTP(ポート25番)を使った方法][1]と[Gmailを使ってメールを送信する方法(SMTP over TLS)][2]をメモしておこうと思います。

<a href="http://golang.org/pkg/net/smtp/" target="_blank">net/smtp</a>パッケージを使用します。
  
基本的には、go-wikiページにある <a href="http://bit.ly/1dFcSBs" target="_blank">SendingMail</a>を参考にしています。

<a href="http://www.puni.net/~mimori/smtp/ref.html" target="_blank">SMTPコマンドリファレンス</a>のような仕様を読みながら、<a href="http://golang.org/pkg/net/smtp/" target="_blank">net/smtp</a>パッケージのソースを読んだら理解が深まりました。感想としては、そのままwrapしてるだけって感じでした。

<!--more-->

## SMTP(ポート25番)を使った方法 {#smtp.section}

{{< gist 6699756 >}}

## Gmailを使ってメールを送信する方法(SMTP over TLS) {#smtpovertls.section}

{{< gist 6680028 >}}

## 参考

  * <a href="http://bit.ly/1dFcSBs" target="_blank">SendingMail &#8211; go-wiki &#8211; Using the smtp package to send an email.</a>
  * <a href="http://www.puni.net/~mimori/smtp/ref.html" target="_blank">SMTPコマンドリファレンス</a>
  * <a href="https://support.google.com/mail/troubleshooter/1668960?rd=1#ts=1665018,1665141,2769074" target="_blank">Gmal ヘルプ &#8211; IMAP と POP3 の開始方法</a>

 [1]: #smtp
 [2]: #smtpovertls