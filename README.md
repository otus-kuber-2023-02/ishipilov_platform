# ishipilov_platform

## kubernetes-prepare
- Склонировал репозиторий
- Создал необходимые директории и файлы
- Выполнил коммит и пуш
- Создал Pull Request

## kubernetes-intro
- Установил и запустил Docker
```
curl -fsSL https://get.docker.com/ | sh
sudo systemctl start docker
sudo usermod -aG docker $USER && newgrp docker
```
- Установил kubectl
```
curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl
chmod +x kubectl
sudo mv kubectl /usr/local/bin/
```
- Установил и запустил minikube
```
sudo apt install -y curl wget apt-transport-https
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
minikube start --driver=docker
```
- Создал Dockerfile, создал из него образ и запушил на Docker hub
- Создал манифест для пода Web используя обрал с Docker hub и запустил под
- Убадился в работоспособности пода пробросив порт
```
kubectl port-forward --address 0.0.0.0 pod/web 8000:8000
```
#### Hipster Shop
- Склонировал репозиторий
- Собрал образ hipster-frontend и запушил на Docker hub
- Под находился в статусе Error из-за отсутсвия переменных окружений
- Создал новый манифест и применил его

### Разберитесь почему все pod в namespace kube-system восстановились после удаления
```
kubectl get pods -n kube-system
NAME                               READY   STATUS    RESTARTS       AGE
coredns-787d4945fb-rtl57           1/1     Running   2 (16m ago)    6d5h
etcd-minikube                      1/1     Running   3 (3h3m ago)   6d5h
kube-apiserver-minikube            1/1     Running   3 (3h3m ago)   6d5h
kube-controller-manager-minikube   1/1     Running   10             6d5h
kube-proxy-z69jh                   1/1     Running   2 (3h3m ago)   6d5h
kube-scheduler-minikube            1/1     Running   3 (3h3m ago)   6d5h
storage-provisioner                1/1     Running   4 (7m2s ago)   3h39m
```
Kubelet, который управляет pod'ами, их контейнерами, образами и т.д. запущен как сервис systemd. При запуске кластера запускаются необходимые контейнеры.
- core-dns управляется через Deployment, его восстанавливает ReplicaSet
- etcd, kube-apiserver, kube-controller-manager, kube-scheduler - являются static pod и запускаются из /etc/kubernetes/manifests/
- kube-proxy управляется и создается контроллером Daemonset
