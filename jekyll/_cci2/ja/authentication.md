---
layout: classic-docs
title: "認証"
short-title: "認証"
description: "LDAP認証の構成"
categories:
  - administration
order: 9
---
このドキュメントでは、CircleCIでOpenLDAPまたはActive Directory資格情報によるユーザー認証を有効化し、構成とテストを行う方法について説明します。

## 前準備

- LDAPサーバーとActive Directoryをインストールし、構成します。
- GitHub Enterpriseが構成済みで、組織およびプロジェクトのソースとしてユーザーからアクセス可能なことを確認します。
- [『TerraformのあるAmazon WebサービスにCircleCI 2.0をインストールする』]({{ site.baseurl }}/2.0/aws/)ドキュメントに従い、CircleCI 2.0の新しいインスタンスを、既存ユーザーなしでインストールします。 **注: **既存のインストールではLDAPはサポートされません。クリーンインストールでのみLDAPを使用できます。 
- [CircleCIサポート](https://support.circleci.com)に連絡して、自分のサーバーにCircleCIをインストールする機能要求を提出します。

**注: **この構成の完了後、すべてのユーザーはLDAP資格情報でCircleCIにログインする必要があります。 各ユーザーは、CircleCIにログインした後で、[アカウント] ページの [接続] ボタンをクリックし、自分のGitHubアカウントに接続して認証を受けます。

## LDAP認証の構成

ここでは、管理コンソール(複製)でLDAPを構成する手順について説明します。

1. 新たにインストールされたCircleCI 2.0インスタンスの管理コンソールに、`admin`ユーザーとしてログインします。
2. [設定] ページの [LDAP] ボタンをクリックします。

[OpenLDAP] または [Active Directory] を選択します。 3. LDAPインスタンスのホスト名とポート番号を入力します。 4. 暗号化タイプを選択します(プレーンテキストは非推奨です)。 5. [ユーザーの検索] フィールドに、`cn=<admin>,dc=<example>,dc=<org>` の形式で、LDAP adminユーザー名を入力します。ここで、`admin`、`example`、`org`は、自社のデータセンターに応じた適切な値に置き換えます。 6. [検索パスワード] フィールドに、LDAP adminのパスワードを入力します。 7. [ユーザー検索DN] フィールドに、`ou=<users>`の形式で適切な値を入力します。`users`は、LDAPインスタンスで使用されている値に置き換えます。 8. [ユーザー名] フィールドに、ユーザーが使用する適切な固有の識別子、たとえば`mail`を入力します。 9. [グループメンバーシップ] フィールドに、適切な値を入力します。 デフォルトの値は、OpenLDAPでは`uniqueMember`、Active Directoryでは`member`です。 このフィールドには、グループのメンバー`dn`が一覧表示されます。 10. [グループオブジェクトクラス] フィールドに、適切な値を入力します。 デフォルトの値は、OpenLDAPでは`groupOfUniqueNames`、Active Directoryでは`group`です。 [`objectClass`] フィールドの値は、 `dn`がグループであることを示します。 11. (オプション) [ユーザー名のテスト] および [パスワードのテスト] フィールドに、テストするLDAPユーザーのテスト用電子メールアドレスとパスワードを入力します。 12. 設定を保存します。

ログインするユーザーは、CircleCIアプリケーションの [アカウント] ページに転送され、ユーザーは [接続] ボタンを使用して自分のGitHubアカウントに接続する必要があります。 [接続] をクリックすると、ページにLDAPセクションが表示され、ユーザーの情報(電子メールアドレスなど)が表示されます。その後で、ユーザーはGitHubアカウントの認証に転送されます。 After authenticating their GitHub account users are directed to the **Job page** to use CircleCI.

**注:** LDAPで認証を受けたユーザーが、その後でLDAP/ADから削除された場合、そのユーザーはログインしている間はCircleCIにアクセスできます(これはCookieに関係する理由からです)。 ユーザーがログアウトするか、Cookieが失効すると、そのユーザーは再度ログインできなくなります。 ユーザーがプロジェクトを参照できるか、ビルドを実行できるかは、そのユーザーのGitHubアクセス許可により定義されます。 このため、GitHubアクセス許可がLDAP/ADのアクセス許可と同期していれば、LDAP/ADユーザーが削除されると、CircleCIの表示またはアクセス権限も自動的に消失します。