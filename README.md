# python-flask-demo-app

## 要件

- Linux
- [uv](https://docs.astral.sh/uv/getting-started/installation/)

## 利用

本番用のサービスで動かす。

```bash
# bash
uv run gunicorn main:app
```

> **注記**
>
> gunicornはWindowsでは動作しません。WSLやLinux環境で実行してください。

## 開発

### Flaskで動かす

開発用のサービスで動かす。

```bash
# bash
uv run main.py
```

### Gunicorn + Flaskで動かす

サービスを動かす。

```bash
# bash

read -p "Enter github username: " -rs username
```

```bash
# bash @ Ubuntu

# Install Podman
sudo apt-get update
sudo apt-get -y install podman

# Build and run the container
podman build -t python-flask-demo-app:0.1 .
podman run -d -p 8000:8000 python-flask-demo-app:0.1

# Test the container
curl -s http://localhost:8000/
```

### Nginx + Gunicorn + Flaskで動かす

サービスを動かす。

```bash
# bash

# Run the container with podman-compose
uv run podman-compose up -d

# Test the container
curl -s http://localhost:1080/
```

### イメージをpushする

必要な情報を入力する。

```bash
# bash

read -p "Enter github username: " -rs username

read -p "Enter container registry pat: " -rs container_registry_pat
export CR_PAT="${container_registry_pat}"
unset $container_registry_pat
```

レジストリにログインする。

```bash
# bash
echo $CR_PAT | podman login ghcr.io -u "${username}" --password-stdin
```

イメージをビルドしてpushする。

```bash
# bash
podman tag python-flask-demo-app:0.1 ghcr.io/${username}/python-flask-demo-app:0.1
podman push ghcr.io/${username}/python-flask-demo-app:0.1
```

pushしたコンテナ・レジストリのリポジトリを一覧表示する、URLを表示する。

```bash
# bash
echo "https://github.com/${username}?tab=packages"
```

### Kubernetes にデプロイする

```bash
# bash
read -p "Enter email: " -r email
```

```bash
# bash

cd kubernetes

# Create namespace
kubectl apply -f namespace.yaml

# Create docker-registry secret
kubectl create secret docker-registry regcred \
--docker-server=ghcr.io \
--docker-username="${username}" \
--docker-password="${CR_PAT}" \
-n flask-app

# Deploy the application
kubectl apply -k .

# Test the deployment
curl -H "Host: localhost" http://localhost/
```

## TODO

- helm
