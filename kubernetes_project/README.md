1. Разворот инфрастурктуры kubernetes на baremetal:
	для Мастера (k8s-master)
	для Воркеров:
	k8s-worker1
	k8s-worker2
	k8s-worker3

	1.1 на всех машинах выключить firewall
		systemctl stop firewalld
		Проверить статус
		ufw status
		Очистить правила вх/исх пакетов
		iptables -F 
		Проверить
		iptables -L
	1.2 на мастере Генерация ключа ssh
		cd /root
		ssh-keygen -t rsa (на все вопросы нажать Enter)
		ssh-agent bash
		ssh-add ~/.ssh/id_rsa

		Далее на всех машинах прописать открытый ключ в authorized_keys в папке /root/.ssh
		ssh-copy-id root@remote_host
		
	1.3 на мастере Установить необходимые утилиты
		sudo apt-get update
		sudo apt-get install python3-pip
		sudo apt-get install python3-virtualenv

	1.4 Клонировать репозиторий kubespray
		git clone https://github.com/kubernetes-sigs/kubespray.git

	1.5 Установить kubespray
		VENVDIR=kubespray-venv
		KUBESPRAYDIR=kubespray
		ANSIBLE_VERSION=2.12
		virtualenv  --python=$(which python3) $VENVDIR
		source $VENVDIR/bin/activate
		cd $KUBESPRAYDIR
		pip install -U -r requirements-$ANSIBLE_VERSION.txt

	1.6 Создать свою директорию для настроек
		cp -rfp inventory/sample inventory/mycluster
		Содержимое файла /root/kubespray/mycluster/inventory.ini заменить содержимом файла kubernetes_project/infra/inventory.ini
		Содержимое файла /root/kubespray/mycluster/groups_vars_k8s_cluster/addons.yml заменить содержимом файла kubernetes_project/infra/addons.yml
		Содержимое файла /root/kubespray/mycluster/groups_vars_k8s_cluster/k8s-cluster.yml заменить содержимом файла kubernetes_project/infra/k8s-cluster.yml 

	1.7 Запустить разворот
		ansible-playbook -i inventory/mycluster/inventory.ini  --become --become-user=root cluster.yml
		Результат PLAY RECAP должен быть без ошибок (failed)

	1.8 Проверка кластера
		kubectl config get-clusters
		kubectl cluster-info
		kubectl get  nodes
		kubectl get services --all-namespaces

	1.9 Настройка дашборда
		Проверить установку
		kubectl get svc kubernetes-dashboard -n kube-system
		Создать аккаунт и связать кластерную роль
		kubectl create serviceaccount kubernetes-dashboard -n kube-system 
		kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard --user=clusterUser
		В конфигурации дашборда переопределить тип с ClusterIP на NodePort:
		Сформировать токен
		kubectl -n kube-system create token kubernetes-dashboard
	
	1.10 Установка helm
		curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
		chmod 700 get_helm.sh
		./get_helm.sh
		helm version

2. Мониторинг:
	helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
	helm repo update
	kubectl create namespace monitoring
	helm install  prometheus prometheus-community/kube-prometheus-stack

3. Логирование:
	3.1 Создать namespace
		kubectl apply -f observability/namespace.yaml
	3.2 Создать storageclass
		kubectl apply -f observability/storageclass.yaml
	3.2 Создать сервисы
		kubectl apply -f observability/service.yaml
	3.3 Создать statefulset
		kubectl apply -f observability/statefulset.yaml
	3.4 Создать pv
		kubectl apply -f observability/pv.yaml
	3.5 Проверить, что pvc в статусе bound
		kubectl get pvc -n elastic-system
	3.6 Создать deployment
		kubectl apply -f observability/deployment.yaml
	3.7 Проверить, что поды в статусе running
		kubectl get po -n elastic-system
	3.8 Создать fluentd
		kubectl apply -f observability/fluentd.yaml

4. CI/CD ArgoCD
	kubectl create namespace argocd
	kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
	kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'