+++
date = "2018-05-20T18:10:45+09:00"
title = "GitHub PagesのカスタムドメインがHTTPSをサポートしたのでメモ"
draft = false

+++

[GitHub PagesのカスタムドメインがHTTPSをサポートした](https://blog.github.com/2018-05-01-github-pages-custom-domains-https/)ので、その設定の忘却録です

GitHub Pagesのレポジトリの設定をみると、

> Unavailable for your site because your domain is not properly configured to support HTTPS

といわれて、Enforce HTTPSにチェックできなかった。
それを対策するには、下記のHELPページに書かれているが、メモしておく。

https://help.github.com/articles/setting-up-an-apex-domain/



1.お名前.comのDNS設定に行き、AレコードのIPアドレスを追加または変更する。

```
185.199.108.153
185.199.109.153
185.199.110.153
185.199.111.153
```

<div align="center">
<img src="/images/2018/05/oname-dns.png" />
</div>


2.反映確認

```
% dig +noall +answer kwmt27.net
kwmt27.net.		300	IN	A	185.199.108.153
kwmt27.net.		300	IN	A	185.199.109.153
kwmt27.net.		300	IN	A	185.199.110.153
kwmt27.net.		300	IN	A	185.199.111.153
```

3.Custom domainを一旦削除して再度登録しなおす。

> Add your custom domain to your GitHub Pages site. If you're updating the IP address of an existing A record, first remove and then re-add your custom domain to the repository you’re using to publish your Pages site to trigger the process of enabling HTTPS. For more information, see "Adding or removing a custom domain for your GitHub Pages site."


とあるように、github pagesのレポジトリの設定で、Custom domainを一旦削除して再度登録しなおす。（削除は、ドメインを空にしてsaveする。）


このあと、レポジトリの設定を更新すると、

> Not yet available for your site because the certificate has not finished being issued

と表示されるが、しばらく待てばチェックできるようになった。

<div align="center">
<img src="/images/2018/05/enforce-https.png" />
</div>

## httpsで確認したら表示がくずれた

これは、hugoの設定でbaseurlの変更が必要だった

https://github.com/kwmt/hugo_gh_blog/commit/a22e5b2a75d48fd682731f832db8c3f97a0f4f7c



## できた

<div align="center">
<img src="/images/2018/05/custom-domain-https-finish.png" />
</div>