# dify-template

## 環境構築
Amazon linux2023を立ち上げてdocker等をインストールする
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

インスタンスのIPアドレスを使いたいドメインに紐付ける


### difyインストール
.envは必要に応じて編集する。
```bash
cd docker
cp .env.example .env
docker compose up -d
```

### メモリが不安な場合はswapを作成
```bash
dd if=/dev/zero of=/swapfile bs=1M count=2048
chmod 600 /swapfile
mkswap /swapfile
swapon /swapfile
echo '/swapfile swap swap defaults 0 0' >> /etc/fstab
```

