---
title: "Использование ACR для кластера DC/OS Azure | Документация Майкрософт"
description: "Использование реестра контейнеров Azure с кластером DC/OS в Службе контейнеров Azure"
services: container-service
documentationcenter: 
author: julienstroheker
manager: dcaro
editor: 
tags: acs, azure-container-service, acr, azure-container-registry
keywords: "Docker, контейнеры, микрослужбы, Mesos, Azure, FileShare, cifs"
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/23/2017
ms.author: juliens
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: a8a3716f8d03b596285026426c7514b7b642cb25
ms.lasthandoff: 03/31/2017


---
# <a name="use-acr-with-a-dcos-cluster-to-deploy-your-application"></a>Использование ACR в кластере DC/OS для развертывания приложения

В этой статье рассматриваются способы использования закрытого реестра контейнеров, такого как ACR (реестр контейнеров Azure), совместно с кластером DC/OS. Используя ACR, вы можете сохранять образы в частном хранилище и самостоятельно управлять ими, например отслеживать версии и (или) применять обновления.

Чтобы перейти к использованию этого примера, вам потребуется следующее: 
* Кластер DC/OS, настроенный в Службе контейнеров Azure. Ознакомьтесь с разделом [Развертывание кластера службы контейнеров Azure](container-service-deployment.md).
* Развернутая Служба контейнеров Azure. Изучите статьи [Создание частного реестра контейнеров Docker с помощью портала Azure](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-portal) или [Создание частного реестра контейнеров Docker с помощью Azure CLI 2.0](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-azure-cli).
* Общая папка, настроенная внутри кластера DC/OS. Подробнее см. статью [Создание и подключение файлового ресурса к кластеру DC/OS](container-service-dcos-fileshare.md).
* Умение развернуть образ Docker в кластере DC/OS с помощью [веб-интерфейса](container-service-mesos-marathon-ui.md) или [REST API](container-service-mesos-marathon-rest.md).

## <a name="manage-the-authentication-inside-your-cluster"></a>Управление аутентификацией в кластере

Обычно, чтобы получить образ из закрытого реестра или передать образ в реестр, вам прежде всего нужно пройти проверку подлинности. Чтобы сделать это, выполните команду `docker login` в командной строке для любого клиентского процесса Docker, которому нужно использовать закрытый реестр.
В рабочей среде, например в нашем кластере DC/OS, вам, как правило, нужна возможность получать образы от любого узла. Это означает, что процесс проверки подлинности нужно автоматизировать, чтобы не запускать команды отдельно на каждой машине. Как вы хорошо понимаете, при большом размере кластера выполнение этих операций вручную станет трудоемким и затруднительным. 

Предположим, что у вас уже настроена [общая папка в кластере DC/OS](container-service-dcos-fileshare.md), и теперь мы применим ее следующим образом.

### <a name="from-any-client-machine-recommended-method"></a>На любом клиентском компьютере (предпочтительный метод)

Следующие команды можно выполнять в любой среде (Windows, Mac или Linux).

1. Убедитесь, что у вас есть все эти компоненты:
  * TAR-средство
    * [Windows](http://gnuwin32.sourceforge.net/packages/gtar.htm)
  * Docker 
    * [Windows](https://www.docker.com/docker-windows)
    * [MAC](https://www.docker.com/docker-mac)
    * [Ubuntu](https://www.docker.com/docker-ubuntu)
    * [Прочее](https://www.docker.com/get-docker)
  * Общая папка, подключенная внутри кластера [с помощью этого метода](container-service-dcos-fileshare.md).

2. Выполните проверку подлинности в службе ACR, выполнив в любой оболочке терминала следующую команду: `sudo docker login --username=<USERNAME> --password=<PASSWORD> <ACR-REGISTRY-NAME>.azurecr.io`. Переменные `USERNAME`, `PASSWORD` и `ACR-REGISTRY-NAME` в этой команде нужно заменить значениями, приведенными на портале Azure.

3. Обратите внимание на интересный факт. При выполнении операции `docker login` значения сохраняются на локальном компьютере в домашней папке пользователя (`cd ~/.docker` в Mac и Linux или `cd %HOMEPATH%` в Windows). Мы будем сжимать содержимое этой папки с помощью команды `tar czf`.

4. Последним шагом нам нужно скопировать TAR-файл, который мы только что создали, в общую папку, [которая указана в списке предварительных требований](container-service-dcos-fileshare.md). Это можно сделать в командной строке Azure с помощью следующей команды: `az storage file upload -s <shareName> --account-name <storageAccountName> --account-key <storageAccountKey> -source <pathToTheTarFile>`

Ниже представлен полный пример (для среды Windows), основанный на следующих настройках.
* Имя ACR: **`demodcos`**
* Имя пользователя: **`demodcos`**
* Пароль: **`+js+/=I1=L+D=+eRpU+/=wI/AjvDo=J0`**
* Имя учетной записи хранения: **`anystorageaccountname`**
* Ключ учетной записи хранения: **`aYGl6Nys4De5J3VPldT1rXxz2+VjgO7dgWytnoWClurZ/l8iO5c5N8xXNS6mpJhSc9xh+7zkT7Mr+xIT4OIVMg==`**
* Имя общего ресурса, созданного в учетной записи хранения: **`share`**
* Путь к TAR-архиву для передачи: **`%HOMEPATH%/.docker/docker.tar.gz`**

```bash
# Changing directory to the home folder of the default user
cd %HOMEPATH%

# Authentication into my ACR
docker login --username=demodcos --password=+js+/=I1=L+D=+eRpU+/=wI/AjvDo=J0 demodcos.azurecr.io

# Tar the contains of the .docker folder
tar czf docker.tar.gz .docker

# Upload the tar archive in the fileshare
az storage file upload -s share --account-name anystorageaccountname --account-key aYGl6Nys4De5J3VPldT1rXxz2+VjgO7dgWytnoWClurZ/l8iO5c5N8xXNS6mpJhSc9xh+7zkT7Mr+xIT4OIVMg== --source %HOMEPATH%/docker.tar.gz
```

### <a name="from-the-master-not-recommended-method"></a>На главном компьютере (мы не рекомендуем использовать этот метод)

Нежелательно выполнять такие операции на главном компьютере, чтобы потенциальные ошибки не влияли сразу на всю среду.

1. Сначала установите SSH-подключение к главному узлу (или первому главному узлу) кластера DC/OS. Например, выполните команду `ssh userName@masterFQDN –A –p 22`, где masterFQDN — полное доменное имя главного узла. [Дополнительные сведения по этой ссылке](https://docs.microsoft.com/azure/container-service/container-service-connect#connect-to-a-dcos-or-swarm-cluster)

2. Запустите проверку подлинности в службе ACR, выполнив следующую команду: `sudo docker login --username=<USERNAME> --password=<PASSWORD> <ACR-REGISTRY-NAME>.azurecr.io`. Переменные `USERNAME`, `PASSWORD` и `ACR-REGISTRY-NAME` в этой команде нужно заменить значениями, приведенными на портале Azure.

3. Обратите внимание на интересный факт. При выполнении операции `docker login` значения сохраняются на локальном компьютере в домашней папке пользователя (`~/.docker`). Мы будем сжимать содержимое этой папки с помощью команды `tar czf`.

4. Последним шагом нам нужно скопировать TAR-файл, который мы только что создали, в общую папку. Эта операция позволит использовать эти учетные данные на всех виртуальных машинах нашего кластера и успешно авторизоваться в реестре контейнеров Azure.

Ниже представлен полный пример, основанный на следующих настройках.
* Имя ACR: **`demodcos`**
* Имя пользователя: **`demodcos`**
* Пароль: **`+js+/=I1=L+D=+eRpU+/=wI/AjvDo=J0`**
* Точки подключения внутри кластера: **`/mnt/share`**

```bash
# Changing directory to the home folder of the default user
cd ~

# Authentication into my ACR
sudo docker login --username=demodcos --password=+js+/=I1=L+D=+eRpU+/=wI/AjvDo=J0 demodcos.azurecr.io

# Tar the contains of the .docker folder
sudo tar czf docker.tar.gz .docker

# Copy of the tar file in the file share of my cluster
sudo cp docker.tar.gz /mnt/share
```


## <a name="deploy-an-image-from-acr-with-marathon"></a>Развертывание образа из ACR с помощью Marathon

Мы предполагаем, что вы уже поместили в реестр контейнеров образы, которые хотите развернуть. См. статью [Отправка первого образа в частный реестр контейнеров Docker с помощью интерфейса командной строки Docker](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-docker-cli).

Предположим, что нам нужно развернуть **простой веб-образ** с тегом **2.1** из частного реестра, который размещается в Azure (ACR). Мы будем использовать следующую конфигурацию:

```json
{
  "id": "myapp",
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "demodcos.azurecr.io/simple-web:2.1",
      "network": "BRIDGE",
      "portMappings": [
        { "hostPort": 0, "containerPort": 80, "servicePort": 10000 }
      ],
      "forcePullImage":true
    }
  },
  "instances": 3,
  "cpus": 0.1,
  "mem": 65,
  "healthChecks": [{
      "protocol": "HTTP",
      "path": "/",
      "portIndex": 0,
      "timeoutSeconds": 10,
      "gracePeriodSeconds": 10,
      "intervalSeconds": 2,
      "maxConsecutiveFailures": 10
  }],
  "labels":{
    "HAPROXY_GROUP":"external",
    "HAPROXY_0_VHOST":"YOUR FQDN",
    "HAPROXY_0_MODE":"http"
  },
  "uris":  [
       "file:///mnt/share/docker.tar.gz"
   ]
}
```

> [!NOTE] 
> Как вы видите, место хранения учетных данных здесь указывается с помощью **идентификаторов URI**.
>

## <a name="next-steps"></a>Дальнейшие действия
* Узнайте больше об [управлении контейнерами DC/OS](container-service-mesos-marathon-ui.md).
* Управление контейнерами DC/OS с помощью [REST API Marathon](container-service-mesos-marathon-rest.md).
