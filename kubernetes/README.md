# README

## 構成図

```mermaid
flowchart TB

curl
subgraph kubernetes1["kubernetes cluster"]
  subgraph namespace1["ns-flask-app-dev"]
    svcNginx1
    subgraph pods11["pods"]
      poNginx11
    end
    svcFlaskApp1
    subgraph pods12["pods"]
      poFlask11
    end
  end
  subgraph namespace2["ns-flask-app-prod"]
    svcNginx2
    subgraph pods21["pods"]
      poNginx21
      poNginx22
    end
    svcFlaskApp2
    subgraph pods22["pods"]
      poFlask21
      poFlask22
    end
  end
end

curl --> ingress1 --> svcNginx1 --> poNginx11
poNginx11 --> svcFlaskApp1 --> poFlask11

curl --> ingress2 --> svcNginx2 --> poNginx21 & poNginx22
poNginx21 & poNginx22 --> svcFlaskApp2 --> poFlask21 & poFlask22
```

## 手順

```bash
# bash

# 開発環境をデプロイします。
cd kubernetes
kubectl apply -k ./overlays/dev

# 動作確認します。
curl -H "Host: app-dev.example.com" http://localhost/
```

```bash
# bash

# 本番環境をデプロイします。
cd kubernetes
kubectl apply -k ./overlays/prod

# 動作確認します。
curl -H "Host: app-prod.example.com" http://localhost/
```
