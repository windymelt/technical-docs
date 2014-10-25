# UNIX系マシンでYubikeyを用いたログインの設定をする
## 必要なもの
[ ] Yubikey
[ ] UNIX系マシン(e.g. Linux, OS X, FreeBSD)

## 問題
* SELinux稼動下のマシンでは認証が正しく行えないため、Yubikey認証を行うときは無効にすること。

## 手順
### PAMのパッケージが提供されている場合のインストール
#### RHEL/CentOS
```
$ sudo yum install pam_yubico
```

#### Ubuntu
```
$ sudo add-apt-repository ppa:yubico/stable
$ sudo apt-get update
$ sudo apt-get install libpam-yubico
```

#### FreeBSD
```
$ cd /usr/ports/security/pam_yubico
$ make install clean
```

### PAMのパッケージが提供されていない場合のインストール
1. 次節を参考にlibykclient (ykclient.h, libykclient.so) およびこれに含まれる libpam-dev (security/pam_appl.h, libpam.so) をインストールしておく

#### libykclient および libpam-dev のインストール
1-1. libykclientにはCurl, libyubikeyが必要であるから、これから前もってインストールする。
1-2. Curlをインストールしておく(割愛)
1-3. 次節を参考にlibyubikeyを入手する

##### libyubikey
1-3-0. 別の作業用ディレクトリに移動する
1-3-1. [https://developers.yubico.com/yubico-c/](https://developers.yubico.com/yubico-c/)ページの左ペインの「Releases」からlibyubikeyをダウンロードし、展開する
1-3-2. 以下のコマンドを実行し、libyubikeyをビルドする

```
$ ./configure
$ make check
$ sudo make install
```

これでlibyubikeyのビルドが完了した。これからykclientをビルドする。

1-4. [https://developers.yubico.com/yubico-c-client/](https://developers.yubico.com/yubico-c-client/)ページの左ペインの「Releases」からykclientをダウンロードし、展開する
1-5. 以下のコマンドを実行し、ykclientをビルドする

```
$ ./configure
$ make check
$ sudo make install
```

1-6. もし、新機能であるチャレンジレスポンス オフライン認証を利用したいときはyubikey-personalizationプロジェクトに含まれるlibykpers-1が必要になるので、これをインストールする。
1-6-0. 別の作業用ディレクトリに移動する
1-6-1. [https://developers.yubico.com/yubikey-personalization/](https://developers.yubico.com/yubikey-personalization/)ページの左ペインの「Releases」からyubikey-personalizationをダウンロードし、展開する
1-6-2. 以下のコマンドを実行し、yubikey-personalizationをビルドする

```
$ ./configure
$ make check install
```

これで必要なファイルが揃ったのでyubico-pamのビルドに移る。

2. [https://developers.yubico.com/yubico-pam/Releases/](https://developers.yubico.com/yubico-pam/Releases/)から最新版のzipファイルをダウンロードし、展開する
3. 以下のコマンドを実行し、yubico-pamをビルドする。
```
$ ./configure
$ make check install
```

>`./configure --without-ldap`とするとLDAPサポートを無効化する。

4. 以下のコマンドを実行し、得られた`pam_yubico.so`を`/lib/security/`に移動する。

```
mv /usr/local/lib/security/pam_yubico.so /lib/security/
```

## PAMを設定する
まずユーザとyubikeyのIDとを対応させる必要がある。対応付けには、passwdのように集中管理する方法と、sshでのauthorized_keysのようにユーザごとに分散管理する方法とがある。ここでは、後者である分散管理のスタイルを採る。

1. `~/.yubico/authorized_yubikeys`を作成し、以下のとおり記述する。

```
ユーザID:YubikeyのトークンID[:第2のトークンID[:第3のトークンID...]]
```

>YubikeyのトークンIDは、標準ではYubikeyが発行する文字列の先頭12文字である。OTPの長さを標準以外に設定している場合は、パスワードはビット単位で長さを変更できる都合上、ID部とパスワード部との両方が、境界の同じ一つの文字上に記録されることがある。この場合は、[http://demo.yubico.com/php-yubico/Modhex_Calculator.php]に行って「OTP」を指定し、テキストボックスにOTPを発行させると「Modhex encoded」の欄にIDが表示される。

サポートされるPAMパラメータについては、参考URLを参照すること。

ここでは、yubikeyを利用したシングルファクター認証の方法について述べる。

2. rootで/etc/pam.d/loginを開く。
3. `@include common-auth`という文言があるため、この直上に以下の通り記述する。

```
auth sufficient pam_yubico.so id=16
```

>idは適当な数字を記入すればよい。この数字はクライアントを特定するためのものであり、あなたがYubico社の認証を使わずに自前のYubikey認証サーバを用意して使う場合に利用するものである。Yubikeyによる認証ができればよく、高度のセキュリティを必要とするのでなければ、自前でサーバを用意する必要はない。

>PAMファイルの記述と書き込むファイルを変えることで、二要素認証としたりGDMに対応させたりすることができるが、ここでは割愛する。

## 参考URL
[https://developers.yubico.com/yubico-pam/](https://developers.yubico.com/yubico-pam/)
