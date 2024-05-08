# dify-template

## 環境構築

### サーバーの立ち上げとdockerのインストール
Amazon Linux2023を立ち上げてdockerをインストールしてdocker composeを使えるようにする。
Amazon Linux2023はEC2インスタンスでもLightsailでもどちらでも良い。
インスタンスには必ずpublic IPアドレスを割り当てる事。
80, 443ポートを解放する。
全てrootで行う必要があるがセキュリティが気になる場合は別途ユーザーを作って作業する。
その場合コマンドによってはsudoをつける必要がある。また、作ったユーザーにdockerを実行する権限を付与する必要がある。

```bash
dnf update
dnf install -y docker git
gpasswd -a $(whoami) docker
systemctl start docker
systemctl enable docker.service
systemctl enable containerd.service
echo 'export DOCKER_CONFIG=${DOCKER_CONFIG:-/usr/local/lib/docker}' >> ~/.bash_profile
exec $SHELL -l
mkdir -p $DOCKER_CONFIG/cli-plugins
curl -SL https://github.com/docker/compose/releases/download/v2.27.0/docker-compose-linux-x86_64 -o $DOCKER_CONFIG/cli-plugins/docker-compose
chmod +x /usr/local/lib/docker/cli-plugins/docker-compose
```

### ドメインの設定
インスタンスのIPアドレスを使いたいドメインに紐付ける。
**SSL証明書を発行するために必要なので必ずこれ以降の作業の前に設定する事。**
これが正しく設定設定されていないと、SSL証明書の発行に失敗する。
SSL証明書はこの後にdockerで立ち上げるhttps-portalのコンテナがLet's Encryptを使って自動で発行、更新をしてくれる。


### difyインストール
.envは必要に応じて編集する。
DOMAINは必ず上の項目で設定したドメインにする。
パスワードは適宜変更、シークレットキーは`openssl rand -base64 42`で生成する。

```bash
cd docker
cp .env.example .env
docker compose up -d
```

### メモリが不安な場合はswapを作成
立ちあがるコンテナが多いため2GBのインスタンスでギリギリなのでswapは作成したほうがいいかもしれない。

```bash
dd if=/dev/zero of=/swapfile bs=1M count=2048
chmod 600 /swapfile
mkswap /swapfile
swapon /swapfile
echo '/swapfile swap swap defaults 0 0' >> /etc/fstab
```

