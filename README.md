1.在AWS建立一個EC2
2.使用vscode使用ssh連上我剛建立的EC2 公有ip為54.144.23.183

================
安裝 Docker

更新套件列表：
sudo apt update

安裝 Docker：
sudo apt install -y apt-transport-https ca-certificates curl
sudo apt install -y docker.io

啟用並啟動 Docker 服務：
sudo systemctl enable --now docker

將用戶添加到 Docker 群組：
sudo usermod -aG docker $USER
newgrp docker

================
安裝 kubectl

下載 kubectl：
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"

安裝 kubectl：
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl

驗證安裝：
kubectl version --client --output=yaml

================
安裝 Minikube

下載 Minikube：
curl -LO https://github.com/kubernetes/minikube/releases/latest/download/minikube-linux-amd64

安裝 Minikube：
sudo install minikube-linux-amd64 /usr/local/bin/minikube && rm minikube-linux-amd64

驗證 Minikube 安裝：
minikube version

使用 Docker 驅動啟動 Minikube：
minikube start --driver=docker

================
驗證 Minikube 集群

檢查集群狀態：
kubectl cluster-info
kubectl get nodes
kubectl get pods
![image](https://github.com/user-attachments/assets/acfae9c9-b636-42f5-9d65-b63e0760aa18)

檢查 Minikube 儀表板：
minikube dashboard

================
建立學生學號的 HTML 頁面

創建專案資料夾：
mkdir student-id-app
cd student-id-app

創建 HTML 檔案顯示學號：
<!DOCTYPE html>
<html lang="zh-TW">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>M11307005</title>
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <div class="container">
    <h1>M11307005</h1>
    <p class="student-id">M11307005</p>
  </div>
</body>
</html>

================
容器化應用程式

在專案目錄中創建 Dockerfile：
echo 'FROM nginx:alpine
COPY ./index.html /usr/share/nginx/html/index.html' > Dockerfile

建立 Docker 映像檔：
docker build -t student-id-app .

在本地測試 Docker 容器：
docker run -d -p 8080:80 student-id-app

=================
推送 Docker 映像檔到 Docker Hub

登入 Docker Hub：
docker login -u "your-username" -p "your-password"

標籤 Docker 映像：
docker tag student-id-app your-username/student-id-app:latest

推送映像檔到 Docker Hub：
docker push your-username/student-id-app:latest

=================
建立 Kubernetes 部署與服務


創建 Kubernetes 部署 YAML 檔 student-id-deployment.yaml，內容如下：
apiVersion: apps/v1
kind: Deployment
metadata:
  name: student-id-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: student-id
  template:
    metadata:
      labels:
        app: student-id
    spec:
      containers:
      - name: student-id-container
        image: your-username/student-id-app:latest
        ports:
        - containerPort: 80

應用部署：
kubectl apply -f student-id-deployment.yaml

創建 Kubernetes 服務 YAML 檔 student-id-service.yaml，內容如下：
apiVersion: v1
kind: Service
metadata:
  name: student-id-service
spec:
  selector:
    app: student-id
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: NodePort

應用服務：
kubectl apply -f student-id-service.yaml

驗證 Kubernetes 資源

檢查 Pod 的狀態：
kubectl get pods

檢查服務的狀態：
kubectl get svc

===============
讓服務對外開放

到AWS網頁，進入安全群組設置，把port 30080寫入。
![image](https://github.com/user-attachments/assets/4f206c4a-0ae2-4448-b8d0-214ed8180513)

嘗試通過以下網址訪問應用：
http:/54.144.23.183/:30080

#註 我好像打不開網頁
