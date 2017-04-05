---
title: "Непрерывная интеграция и доставка Jenkins c помощью Kubernetes в Службе контейнеров Azure | Документация Майкрософт"
description: "Автоматизация процесса непрерывной интеграции и доставки с помощью Jenkins для развертывания и обновления контейнерного приложения на платформе Kubernetes в Службе контейнеров Azure"
services: container-service
documentationcenter: 
author: chzbrgr71
manager: johny
editor: 
tags: acs, azure-container-service, jenkins
keywords: "Docker, контейнеры, Kubernetes, Azure, Jenkins"
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/23/2017
ms.author: briar
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 3d206ebb6deeaa40f8e792ec12304c99c0abe684
ms.lasthandoff: 03/29/2017


---

# <a name="jenkins-integration-with-azure-container-service-and-kubernetes"></a>Интеграция Jenkins со Службой контейнеров Azure и Kubernetes 
В этом руководстве мы поэтапно рассмотрим процесс настройки непрерывной интеграции многоконтейнерного приложения с кластером Kubernetes в Службе контейнеров Azure с помощью платформы Jenkins. Рабочий процесс обновляет образ контейнера в Docker Hub и модули Kubernetes с помощью развертывания. 

## <a name="high-level-process"></a>Общий процесс
Основные шаги, описанные в этой статье: 
- установка кластера Kubernetes в службе контейнеров Azure;
- настройка Jenkins и настройка доступа к службе контейнеров;
- создание рабочего процесса Jenkins;
- тестирование полного цикла процесса непрерывной интеграции и доставки.

## <a name="install-a-kubernetes-cluster"></a>Установка кластера Kubernetes
    
Разверните кластер Kubernetes в Службе контейнеров Azure с помощью описанных ниже шагов. Подробную документацию можно найти [здесь](container-service-kubernetes-walkthrough.md).

### <a name="step-1-create-a-resource-group"></a>Шаг 1. Создание группы ресурсов
```azurecli
RESOURCE_GROUP=my-resource-group
LOCATION=westus

az group create --name=$RESOURCE_GROUP --location=$LOCATION
```

### <a name="step-2-deploy-the-cluster"></a>Шаг 2. Развертывание кластера
> [!NOTE]
> Для следующих шагов понадобится локальный открытый ключ SSH, хранимый в папке ~/.ssh.
>

```azurecli
RESOURCE_GROUP=my-resource-group
DNS_PREFIX=some-unique-value
CLUSTER_NAME=any-acs-cluster-name

az acs create \
--orchestrator-type=kubernetes \
--resource-group $RESOURCE_GROUP \ 
--name=$CLUSTER_NAME \
--dns-prefix=$DNS_PREFIX \ 
--ssh-key-value ~/.ssh/id_rsa.pub \
--admin-username=azureuser \
--master-count=1 \
--agent-count=5 \
--agent-vm-size=Standard_D1_v2
```

## <a name="set-up-jenkins-and-configure-access-to-container-service"></a>Настройка Jenkins и настройка доступа к службе контейнеров

### <a name="step-1-install-jenkins"></a>Шаг 1. Установка Jenkins
1. Создайте виртуальную машину Azure с Ubuntu 16.04 LTS. 
2. Установите Jenkins, используя эти [инструкции](https://wiki.jenkins-ci.org/display/JENKINS/Installing+Jenkins+on+Ubuntu).
3. Более подробное руководство находится на сайте [howtoforge.com](https://www.howtoforge.com/tutorial/how-to-install-jenkins-with-apache-on-ubuntu-16-04).
4. Обновите группу безопасности сети Azure, чтобы разрешить порт 8080, и выберите общедоступный IP-адрес на порте 8080 для управления Jenkins в браузере.
5. Начальный пароль администратора хранится в папке /var/lib/jenkins/secrets/initialAdminPassword.
6. Установите Docker на компьютер с Jenkins с помощью этих [инструкций](https://docs.docker.com/cs-engine/1.13/#install-on-ubuntu-1404-lts-or-1604-lts). Это позволит выполнять команды Docker в заданиях Jenkins.
7. Настройте разрешения Docker таким образом, чтобы предоставить Jenkins возможность доступа к конечной точке.

    ```bash
    sudo chmod 777 /run/docker.sock
    ```
8. Установите интерфейс командной строки `kubectl` на платформе Jenkins. Дополнительные сведения см. в статье [Installing and Setting up kubectl](https://kubernetes.io/docs/tasks/kubectl/install/) (Установка и настройка Kubectl).

    ```bash
    curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl

    chmod +x ./kubectl

    sudo mv ./kubectl /usr/local/bin/kubectl
    ```

### <a name="step-2-set-up-access-to-the-kubernetes-cluster"></a>Шаг 2. Настройка доступа к кластеру Kubernetes

> [!NOTE]
> Описанные ниже действия можно выполнить несколькими способами. Мы советуем использовать самый простой для вас.
>

1. Скопируйте файл конфигурации `kubectl` на компьютер с Jenkins.

    ```bash
    export KUBE_MASTER=<your_cluster_master_fqdn>
        
    sudo scp -3 -i ~/.ssh/id_rsa azureuser@$KUBE_MASTER:.kube/config user@<your_jenkins_server>:~/.kube/config
        
    sudo ssh user@<your_jenkins_server> sudo chmod 777 /home/user/.kube/config

    sudo ssh -i ~/.ssh/id_rsa user@<your_jenkins_server> sudo chmod 777 /home/user/.kube/config
        
    sudo ssh -i ~/.ssh/id_rsa user@<your_jenkins_server> sudo cp /home/user/.kube/config /var/lib/jenkins/config
    ```
        
2. Из среды с Jenkins проверьте, что кластер Kubernetes доступен.
    

## <a name="create-a-jenkins-workflow"></a>Создание рабочего процесса Jenkins

### <a name="prerequisites"></a>Предварительные требования

- Учетная запись GitHub для репозитория кода.
- Учетная запись Docker Hub для хранения и обновления образов.
- Контейнерное приложение, которое можно повторно создать и обновить. Вы можете использовать пример контейнерного приложения, написанного на языке Golang: https://github.com/chzbrgr71/go-web. 

> [!NOTE]
> Описанные ниже действия необходимо выполнить, используя собственную учетную запись GitHub. Вы можете спокойно клонировать указанный выше репозиторий, однако для настройки доступа к объектам webhook и Jenkins необходимо использовать собственную учетную запись.
>

### <a name="step-1-deploy-initial-v1-of-application"></a>Шаг 1. Развертывание первоначального приложения версии 1
1. Создайте приложение на компьютере разработчика с помощью приведенных ниже команд. Замените имя репозитория `myrepo` на свое собственное.
    
    ```bash
    git clone https://github.com/chzbrgr71/go-web.git
    cd go-web
    docker build -t myrepo/go-web .
    ```

2. Отправьте образ в Docker Hub.

    ```bash
    docker login
    docker push myrepo/go-web
    ```

3. Выполните развертывание в кластере Kubernetes.
    
    > [!NOTE] 
    > Измените файл `go-web.yaml`, чтобы обновить образ контейнера и репозиторий.
    >
        
    ```bash
    kubectl create -f ./go-web.yaml --record
    ```
### <a name="step-2-configure-jenkins-system"></a>Шаг 2. Настройка системы Jenkins
1. Щелкните **Manage Jenkins** (Управление Jenkins)  > **Configure System** (Настройка системы).
2. В разделе **GitHub** выберите **Add GitHub Server** (Добавить сервер GitHub).
3. Оставьте значение поля **API URL** (URL-адрес API) без изменений.
4. В разделе **Credentials** (Учетные данные) добавьте учетные данные Jenkins с помощью пункта **Secret text** (Секретный текст). Мы советуем использовать персональные маркеры доступа GitHub, которые можно настроить с помощью параметров учетной записи пользователя GitHub. Дополнительные сведения см. [здесь](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).
5. Нажмите кнопку **Test connection** (Проверить подключение), чтобы убедиться, что оно настроено правильно.
6. В разделе **Global Properties** (Глобальные свойства) добавьте переменную среды `DOCKER_HUB` и укажите пароль Docker Hub. (Это полезно в этом демонстрационном примере, однако в рабочем сценарии необходимо использовать более безопасный метод.)
7. Щелкните Save (Сохранить).

![Доступ к Jenkins на GitHub](media/container-service-kubernetes-jenkins/jenkins-github-access.png)

### <a name="step-3-create-the-jenkins-workflow"></a>Шаг 3. Создание рабочего процесса Jenkins
1. Создайте элемент Jenkins.
2. Введите имя (например, go-web) и выберите **Freestyle Project** (Любой проект). 
3. Выберите **проект GitHub** и укажите URL-адрес репозитория GitHub.
4. В разделе **Source Code Management** (Управление исходным кодом) укажите URL-адрес и учетные данные репозитория GitHub. 
5. Добавьте **шаг сборки** типа **Execute shell** (Запустить оболочку) и используйте следующий текст:

    ```bash
    WEB_IMAGE_NAME="myrepo/go-web:kube${BUILD_NUMBER}"
    docker build -t $WEB_IMAGE_NAME .
    docker login -u <your-dockerhub-username> -p ${DOCKER_HUB}
    docker push $WEB_IMAGE_NAME
    ```

6. Добавьте другой **шаг сборки** типа **Execute shell** (Запустить оболочку) и используйте следующий текст:

    ```bash
    WEB_IMAGE_NAME="myrepo/go-web:kube${BUILD_NUMBER}"
    kubectl set image deployment/go-web go-web=$WEB_IMAGE_NAME --kubeconfig /var/lib/jenkins/config
    ```

![Шаги сборки Jenkins](media/container-service-kubernetes-jenkins/jenkins-build-steps.png)
    
7. Сохраните элемент Jenkins и протестируйте его с помощью параметра **Build Now** (Собрать сейчас).

### <a name="step-4-connect-github-webhook"></a>Шаг 4. Подключение объекта webhook GitHub
1. В созданном элементе Jenkins щелкните **Configure** (Настройка).
2. В разделе **Build Triggers** (Создание тригерров) выберите **GitHub hook trigger for GITScm polling** (Обработчик триггера Github для опроса GITScm) и щелкните **Save** (Сохранить). Это автоматически настроит объект webhook GitHub.
3. В репозитории GitHub для go-web щелкните **Settings > Webhooks** (Параметры > Объекты webhook).
4. Убедитесь, что URL-адрес webhook для Jenkins успешно добавлен. URL-адрес должен заканчиваться на github-webhook.

![Конфигурация webhook для Jenkins](media/container-service-kubernetes-jenkins/jenkins-webhook.png)

## <a name="test-the-cicd-process-end-to-end"></a>Тестирование полного цикла процесса непрерывной интеграции и доставки

1. Обновите код репозитория и синхронизируйте его с репозиторием GitHub.
2. С помощью консоли Jenkins проверьте **историю сборки** и убедитесь, что задание выполнено. Просмотрите выходные данные консоли, чтобы увидеть подробные сведения.
3. Просмотрите сведения об обновленном развертывании с помощью Kubernetes:

    ```bash
    kubectl rollout history deployment/go-web
    ```

## <a name="next-steps"></a>Дальнейшие действия

- Выполните развертывание реестра контейнеров Azure и сохраните образы в безопасном репозитории. Ознакомьтесь с [документацией по реестру контейнеров Azure](https://docs.microsoft.com/azure/container-registry).
- Создайте более сложный рабочий процесс, который включает в себя параллельное развертывание и автоматическое тестирование в Jenkins.
- Дополнительные сведения о непрерывной интеграции и доставке Jenkins при использовании Kubernetes см. в [блоге Jenkins](https://jenkins.io/blog/2015/07/24/integrating-kubernetes-and-jenkins/).

