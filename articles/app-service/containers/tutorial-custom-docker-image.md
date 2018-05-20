---
title: Применение пользовательского образа Docker для платформы "Веб-приложения для контейнеров" (Azure) | Документация Майкрософт
description: Использование пользовательского образа Docker для платформы "Веб-приложения для контейнеров".
keywords: служба приложений azure, веб-приложение, docker, контейнер
services: app-service
documentationcenter: ''
author: SyntaxC4
manager: SyntaxC4
editor: ''
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/24/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 887ed316605ab423159ef0d2e07f0960c702ed8b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
---
# <a name="use-a-custom-docker-image-for-web-app-for-containers"></a>Использование пользовательского образа Docker для платформы "Веб-приложения для контейнеров".

Платформа [Веб-приложения для контейнеров](app-service-linux-intro.md) предоставляет встроенные образы Docker на базе Linux с поддержкой определенных версий, включая PHP 7.0 и Node.js 4.5. Платформа "Веб-приложения для контейнеров" использует технологию контейнеров Docker для размещения встроенных и пользовательских образов в качестве платформы как услуги. Из этого руководства вы узнаете, как создать пользовательский образ Docker и развернуть его на платформе "Веб-приложения для контейнеров". Этот шаблон используется, если встроенные образы не содержат нужный язык или для приложения требуется определенная конфигурация, которую не предоставляют встроенные образы.

Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * Развертывание пользовательского образа Docker в Azure.
> * Настройка переменных среды для запуска контейнера.
> * Обновление и повторное развертывание образа Docker.
> * Подключение контейнера с помощью SSH.
> * Развертывание частного образа Docker в Azure.

[!INCLUDE [Free trial note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>предварительным требованиям

Для работы с этим учебником необходимы указанные ниже компоненты.

* [Git.](https://git-scm.com/downloads)
* [Docker](https://docs.docker.com/get-started/#setup)
* [Учетная запись Docker Hub](https://docs.docker.com/docker-id/).

## <a name="download-the-sample"></a>Скачивание примера приложения

В окне терминала выполните следующую команду, чтобы клонировать репозиторий с примером приложения на локальный компьютер, а затем перейдите в каталог, в котором содержится образец кода.

```bash
git clone https://github.com/Azure-Samples/docker-django-webapp-linux.git --config core.autocrlf=input
cd docker-django-webapp-linux
```

## <a name="build-the-image-from-the-docker-file"></a>Создание образа на основе файла Docker

В репозитории Git просмотрите файл _Dockerfile_. Этот файл описывает среду Python, необходимую для запуска приложения. Образ также настраивает сервер [SSH](https://www.ssh.com/ssh/protocol/) для обеспечения безопасного взаимодействия между контейнером и узлом.

```docker
FROM python:3.4

RUN mkdir /code
WORKDIR /code
ADD requirements.txt /code/
RUN pip install -r requirements.txt
ADD . /code/

# ssh
ENV SSH_PASSWD "root:Docker!"
RUN apt-get update \
        && apt-get install -y --no-install-recommends dialog \
        && apt-get update \
    && apt-get install -y --no-install-recommends openssh-server \
    && echo "$SSH_PASSWD" | chpasswd 

COPY sshd_config /etc/ssh/
COPY init.sh /usr/local/bin/
    
RUN chmod u+x /usr/local/bin/init.sh
EXPOSE 8000 2222
#CMD ["python", "/code/manage.py", "runserver", "0.0.0.0:8000"]
ENTRYPOINT ["init.sh"]
```

Чтобы создать образ Docker, выполните команду `docker build`, указав имя _mydockerimage_ и тег _v1.0.0_. Замените _\<docker-id>_ идентификатором учетной записи Docker Hub.

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

После выполнения команды должен появиться результат, аналогичный приведенному ниже.

```
# The output from the commands in this article has been shortened for brevity.

Sending build context to Docker daemon  5.558MB
Step 1/13 : FROM python:3.4
 ---> 9ff45ddb54e9
Step 2/13 : RUN mkdir /code
 ---> Using cache
 ---> f3f3ac01db0a
Step 3/13 : WORKDIR /code
 ---> Using cache
 ---> 38b32f15b442
.
.
.
Step 13/13 : ENTRYPOINT init.sh
 ---> Running in 5904e4c70043
 ---> e7cf08275692
Removing intermediate container 5904e4c70043
Successfully built e7cf08275692
Successfully tagged cephalin/mydockerimage:v1.0.0
```

Проверьте, работает ли сборка, запустив контейнер Docker. Выполните команду [​​`docker run`](https://docs.docker.com/engine/reference/commandline/run/), указав имя и тег образа. Обязательно укажите порт с помощью аргумента `-p`.

```bash
docker run -p 2222:8000 <docker-ID>/mydockerimage:v1.0.0
```

Убедитесь, что веб-приложение и контейнер функционируют правильно, перейдя по адресу `http://localhost:2222`.

![Локальное тестирование веб-приложения](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

> [!NOTE] 
> Можно также подключиться к контейнеру приложения непосредственно с локального компьютера разработки с помощью SSH, SFTP или Visual Studio Code (для динамической отладки приложений Node.js). См. дополнительные сведения об [удаленной отладке и SSH в службе приложений в Linux](https://aka.ms/linux-debug).
>

## <a name="push-the-docker-image-to-docker-hub"></a>Отправка образа Docker в Docker Hub

Реестр — это приложение, которое содержит образы и предоставляет образ служб и службы контейнеров. Чтобы сделать образ общедоступным, необходимо передать его в реестр. 

<!-- Depending on your requirements, you may have your docker images in a Public Docker Registry, such as Docker Hub, or a Private Docker Registry such as Azure Container Registry. Select the appropriate tab for your scenario below (your selection will switch multiple tabs on this page). -->

> [!NOTE]
> Дополнительные сведения о передаче образа в частный реестр Docker см. в разделе [Использование образа Docker из любого частного реестра (необязательно)](#use-a-docker-image-from-any-private-registry-optional).

<!--## [Docker Hub](#tab/docker-hub)-->

Docker Hub — это реестр образов Docker, который позволяет размещать ваши собственные репозитории, как общедоступные, так и частные. Чтобы передать пользовательский образ Docker в общедоступный центр Docker Hub, используйте команду [`docker push`](https://docs.docker.com/engine/reference/commandline/push/) и укажите полное имя и тег образа. Полное имя и тег выглядят как в следующем примере:

```
<docker-id>/image-name:tag
```

Чтобы отправить образ, вам нужно выполнить вход в центр Docker Hub с помощью команды [`docker login`](https://docs.docker.com/engine/reference/commandline/login/). Вместо _\<docker-id>_ укажите имя вашей учетной записи, а когда в консоли появится запрос, введите пароль.

```bash
docker login --username <docker-id>
```

При успешном выполнении входа появится сообщение "Вход выполнен". После входа можно передать образ в центр Docker Hub с помощью команды [`docker push`](https://docs.docker.com/engine/reference/commandline/push/).

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

Убедитесь, что передача завершилось успешно, проверив выходные данные команды.

```
The push refers to a repository [docker.io/<docker-id>/mydockerimage:v1.0.0]
c33197c3f6d4: Pushed
ccd2c850ee43: Pushed
02dff2853466: Pushed
6ce78153632a: Pushed
efef3f03cc58: Pushed
3439624d77fb: Pushed
3a07adfb35c5: Pushed
2fcec228e1b7: Mounted from library/python
97d2d3bae505: Mounted from library/python
95aadeabf504: Mounted from library/python
b456afdc9996: Mounted from library/python
d752a0310ee4: Mounted from library/python
db64edce4b5b: Mounted from library/python
d5d60fc34309: Mounted from library/python
c01c63c6823d: Mounted from library/python
v1.0.0: digest: sha256:21f2798b20555f4143f2ca0591a43b4f6c8138406041f2d32ec908974feced66 size: 3676
```

<!--
# [Private Registry](#tab/private-registry)

// Move Private Registry instructions here when Tabbed Conceptual bug is fixed

---
-->

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-app-to-azure"></a>Развертывание приложения в Azure

С помощью веб-приложения Azure можно разместить собственные приложения Linux в облаке. Чтобы создать веб-приложение для контейнеров, выполните команды Azure CLI, которые создают группу, план обслуживания, а затем само веб-приложение. 

### <a name="create-a-resource-group"></a>Создание группы ресурсов

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-a-linux-app-service-plan"></a>Создание плана службы приложений Linux

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)] 

### <a name="create-a-web-app"></a>Создание веб-приложения

В Cloud Shell создайте [веб-приложение](app-service-linux-intro.md) в рамках плана службы приложений `myAppServicePlan` с помощью команды [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create). Замените _<appname>_ уникальным именем приложения, а _\<docker-ID>_ — идентификатором Docker.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --deployment-container-image-name <docker-ID>/mydockerimage:v1.0.0
```

Когда веб-приложение будет создано, в Azure CLI отобразится примерно следующее:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="configure-environment-variables"></a>Настройка переменных среды

В большинстве образов Docker есть переменные среды, которые нужно настроить. Если вы используете существующий образ Docker, созданный кем-то другим, образ может использовать порт, отличный от порта 80. Чтобы сообщить Azure, какой порт использует образ, примените параметр приложения `WEBSITES_PORT`. На странице GitHub с [примером кода Python в этом руководстве](https://github.com/Azure-Samples/docker-django-webapp-linux) показано, что для параметра `WEBSITES_PORT` необходимо задать значение _8000_.

Чтобы задать параметры приложения, выполните команду [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) в Cloud Shell. Параметры приложения чувствительны к регистру и используются с разделителями-пробелами.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WEBSITES_PORT=8000
```

<!-- Depending on your requirements, you may have your docker images in a Public Docker Registry, such as Docker Hub, or a Private Docker Registry, such as Azure Container Registry. Select the appropriate tab for your scenario below: -->

> [!NOTE]
> Дополнительные сведения о выполнении развертывания из частного реестра Docker см. в разделе [Использование образа Docker из любого частного реестра (необязательно)](#use-a-docker-image-from-any-private-registry-optional).

<!-- # [Docker Hub](#tab/docker-hub)-->

<!-- # [Private Registry](#tab/private-registry)

// Place Private Registry text back here once Tabbed Conceptual bug is fixed

---
-->

### <a name="test-the-web-app"></a>Тестирование веб-приложения

Убедитесь, что веб-приложение работает, открыв его (`http://<app_name>azurewebsites.net`). 

![Тестирование настройки порта веб-приложения](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="change-web-app-and-redeploy"></a>Изменение и повторное развертывание веб-приложения

Откройте файл app/templates/app/index.html в локальном репозитории Git. Найдите первый элемент HTML и измените его.

```python
<nav class="navbar navbar-inverse navbar-fixed-top">
    <div class="container">
      <div class="navbar-header">         
        <a class="navbar-brand" href="#">Azure App Service - Updated Here!</a>       
      </div>            
    </div>
  </nav> 
```

После того как вы изменили и сохранили файл Python, необходимо перестроить и передать новый образ Docker. Затем перезапустите веб-приложение, чтобы изменения вступили в силу. Используйте те же команды, которые использовались ранее в этом руководстве. Можно перейти к разделам о [создании образа на основе файла Docker](#build-the-image-from-the-docker-file) и [отправки образа Docker в центр Docker Hub](#push-the-docker-image-to-docker-hub). [Протестируйте веб-приложение](#test-the-web-app).

## <a name="connect-to-web-app-for-containers-using-ssh"></a>Подключение к веб-приложению для контейнеров с помощью SSH

SSH обеспечивает безопасный обмен данными между клиентом и контейнером. Чтобы пользовательский образ Docker поддерживал SSH, встройте его в файл Dockerfile. Включите SSH в самом файле Docker. Инструкции SSH уже добавлены в образец файла dockerfile, поэтому вы можете следовать им, используя собственный пользовательский образ.

* Инструкция [RUN](https://docs.docker.com/engine/reference/builder/#run) вызывает `apt-get`, а затем устанавливает пароль `"Docker!"` для учетной записи root.

    ```docker
    ENV SSH_PASSWD "root:Docker!"
    RUN apt-get update \
            && apt-get install -y --no-install-recommends dialog \
            && apt-get update \
      && apt-get install -y --no-install-recommends openssh-server \
      && echo "$SSH_PASSWD" | chpasswd 
    ```

    > [!NOTE]
    > Эта конфигурация не разрешает внешние подключения к контейнеру. SSH доступен только на сайте Kudu и на сайте SCM. Сайты Kudu и SCM проходят проверку подлинности с учетными данными для публикации.

* Инструкция [COPY](https://docs.docker.com/engine/reference/builder/#copy) указывает ядру Docker копировать файл [sshd_config](http://man.openbsd.org/sshd_config) в каталог */etc/ssh/*. Файл конфигурации должен быть создан на основе этого файла [sshd_config](https://github.com/Azure-App-Service/node/blob/master/6.11.1/sshd_config).

    ```docker
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > Файл *sshd_config* должен содержать приведенные ниже элементы. 
    > * `Ciphers` должен содержать по крайней мере один элемент в этом списке: `aes128-cbc,3des-cbc,aes256-cbc`.
    > * `MACs` должен содержать по крайней мере один элемент в этом списке: `hmac-sha1,hmac-sha1-96`.

* Инструкция [EXPOSE](https://docs.docker.com/engine/reference/builder/#expose) предоставляет порт 2222 в контейнере. Даже при указании пароля привилегированного пользователя порт 2222 недоступен из Интернета. Это внутренний порт, который доступен контейнерам внутри мостовой сети виртуальной частной сети. После этого команды скопируют сведения о конфигурации SSH и запустят службу `ssh`.

    ```docker
    EXPOSE 8000 2222
    ```

* Убедитесь, что [служба SSH запущена](https://github.com/Azure-App-Service/node/blob/master/6.9.3/startup/init_container.sh) с помощью скрипта оболочки в каталоге /bin.
 
    ```bash
    #!/bin/bash
    service ssh start
    ```
     
### <a name="open-ssh-connection-to-container"></a>Открытие SSH-подключения к контейнеру

Платформа "Веб-приложения для контейнеров" не разрешает устанавливать внешние подключения к контейнеру. SSH можно использовать только на сайте Kudu, к которому можно перейти по адресу `https://<app_name>.scm.azurewebsites.net`.

Чтобы установить подключение, перейдите по ссылке `https://<app_name>.scm.azurewebsites.net/webssh/host` и войдите, используя учетную запись Azure.

Затем вы будете перенаправлены на страницу с интерактивной консолью. 

Вы можете проверить выполнение некоторых приложений в контейнере. Чтобы просмотреть контейнер и проверить выполняющиеся процессы, выполните команду `top` в командной строке.

```bash
top
```

Команда `top` выведет все запущенные процессы в контейнере.

```
PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND
 1 root      20   0  945616  35372  15348 S  0.0  2.1   0:04.63 node
20 root      20   0   55180   2776   2516 S  0.0  0.2   0:00.00 sshd
42 root      20   0  944596  33340  15352 S  0.0  1.9   0:05.80 node /opt/s+
56 root      20   0   59812   5244   4512 S  0.0  0.3   0:00.93 sshd
58 root      20   0   20228   3128   2664 S  0.0  0.2   0:00.00 bash
62 root      20   0   21916   2272   1944 S  0.0  0.1   0:03.15 top
63 root      20   0   59812   5344   4612 S  0.0  0.3   0:00.03 sshd
65 root      20   0   20228   3140   2672 S  0.0  0.2   0:00.00 bash
71 root      20   0   59812   5380   4648 S  0.0  0.3   0:00.02 sshd
73 root      20   0   20228   3160   2696 S  0.0  0.2   0:00.00 bash
77 root      20   0   21920   2304   1972 R  0.0  0.1   0:00.00 top
```

Поздравляем! Вы настроили пользовательский образ Docker для платформы "Веб-приложение для контейнеров".

## <a name="use-a-private-image-from-docker-hub-optional"></a>Использование частного образа из центра Docker Hub (необязательно)

В разделе [Создание веб-приложения](#create-a-web-app) вы указали образ в Docker Hub с помощью команды `az webapp create`. Этот способ подходит для общедоступного образа. Чтобы использовать частный образ, нужно настроить идентификатор учетной записи Docker и пароль в веб-приложении Azure.

В Cloud Shell выполните команды `az webapp create` и [`az webapp config container set`](/cli/azure/webapp/config/container?view=azure-cli-latest#az_webapp_config_container_set). Замените *\<app_name>*, _\<docker-id>_ и _\<password>_, указав свой идентификатор Docker и пароль.

```azurecli-interactive
az webapp config container set --name <app_name> --resource-group myResourceGroup --docker-registry-server-user <docker-id> --docker-registry-server-password <password>
```

Команда показывает вывод, похожий на следующую строку JSON, которая указывает на то, что изменение конфигурации выполнено успешно:

```json
[
  {
    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
    "slotSetting": false,
    "value": "false"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
    "slotSetting": false,
    "value": "<docker-id>"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
    "slotSetting": false,
    "value": null
  },
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "value": "DOCKER|<image-name-and-tag>"
  }
]
```

## <a name="use-a-docker-image-from-any-private-registry-optional"></a>Использование образа Docker из любого частного реестра (необязательно)

Из этого раздела вы узнаете, как использовать образ Docker из частного реестра на платформе "Веб-приложения для контейнеров". В качестве примера мы будем использовать реестр контейнеров Azure. Действия по использованию других реестров идентичны. 

Реестр контейнеров Azure — это управляемая служба Docker из Azure для размещения частных образов. Развертывания могут быть любого типа, включая [Docker Swarm](https://docs.docker.com/engine/swarm/), [Kubernetes](https://kubernetes.io/) и платформу "Веб-приложения для контейнеров". 

### <a name="create-an-azure-container-registry"></a>Создание реестра контейнеров Azure

В Cloud Shell создайте реестр контейнеров Azure с помощью команды [`az acr create`](/cli/azure/acr?view=azure-cli-latest#az_acr_create). Передайте имя, группу ресурсов и `Basic` для SKU. Доступные номера SKU: `Classic`, `Basic`, `Standard` и `Premium`.

```azurecli-interactive
az acr create --name <azure-container-registry-name> --resource-group myResourceGroup --sku Basic --admin-enabled true
```

В результате будут возвращены следующие выходные данные:

```
 - Finished ..
Create a new service principal and assign access:
  az ad sp create-for-rbac --scopes /subscriptions/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name> --role Owner --password <password>

Use an existing service principal and assign access:
  az role assignment create --scope /subscriptions/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name> --role Owner --assignee <app-id>
{
  "adminUserEnabled": false,
  "creationDate": "2017-08-09T04:21:09.654153+00:00",
  "id": "/subscriptions/<subscriptionId>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name>",
  "location": "westeurope",
  "loginServer": "<azure-container-registry-name>.azurecr.io",
  "name": "<azure-container-registry-name>",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "myazurecontainerre042025"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

### <a name="log-in-to-azure-container-registry"></a>Вход в реестр контейнеров Azure

Для передачи образа в реестр необходимо предоставить учетные данные, чтобы реестр принял образ. Эти учетные данные можно получить, выполнив команду [`az acr show`](/cli/azure/acr?view=azure-cli-latest#az_acr_show) в Cloud Shell. 

```azurecli-interactive
az acr credential show --name <azure-container-registry-name>
```

Команда выведет два пароля, которые могут использоваться с именем пользователя.

```json
<
  "passwords": [
    {
      "name": "password",
      "value": "{password}"
    },
    {
      "name": "password2",
      "value": "{password}"
    }
  ],
  "username": "<registry-username>"
}
```

В окне терминала на локальном компьютере войдите в реестр контейнеров Azure с помощью команды `docker login`. Для входа укажите имя сервера. Используйте формат `{azure-container-registry-name>.azurecr.io`. Когда в консоли появится запрос, введите пароль.

```bash
docker login <azure-container-registry-name>.azurecr.io --username <registry-username>
```

Убедитесь, что вход выполнен успешно. 

### <a name="push-an-image-to-azure-container-registry"></a>Отправка образа в реестр контейнеров Azure

> [!NOTE]
> Если вы используете собственный образ, добавьте для него тег, как показано ниже:
> ```bash
> docker tag <azure-container-registry-name>.azurecr.io/mydockerimage
> ```

Отправьте образ с помощью команды `docker push`. Присвойте образу имя реестра, за которым следует имя и тег образа.

```bash
docker push <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

Убедитесь, что контейнер успешно добавлен в реестр, получив список репозиториев ACR. 

```azurecli-interactive
az acr repository list -n <azure-container-registry-name>
```

Если этот образ есть в списке, значит `mydockerimage` находится в реестре.

```json
[
  "mydockerimage"
]
```

### <a name="configure-web-app-to-use-the-image-from-azure-container-registry-or-any-private-registry"></a>Настройка веб-приложения для использования образа из реестра контейнеров Azure (или любого частного реестра)

Можно настроить платформу "Веб-приложения для контейнеров" для запуска контейнера, хранящегося в реестре контейнеров Azure. Использование реестра контейнеров Azure идентично использованию любого частного реестра, включая ваш собственный.

В Cloud Shell выполните команду [`az acr credential show`](/cli/azure/acr/credential?view=azure-cli-latest#az_acr_credential_show), чтобы отобразить имя пользователя и пароль для реестра контейнеров Azure. Скопируйте имя пользователя и один из паролей, чтобы использовать их для настройки веб-приложения на следующем шаге.

```bash
az acr credential show --name <azure-container-registry-name>
```

```json
{
  "passwords": [
    {
      "name": "password",
      "value": "password"
    },
    {
      "name": "password2",
      "value": "password2"
    }
  ],
  "username": "<registry-username>"
}
```

В Cloud Shell выполните команду [`az webapp config container set`](/cli/azure/webapp/config/container?view=azure-cli-latest#az_webapp_config_container_set), чтобы назначить пользовательский образ Docker для веб-приложения. Замените значения *\<app_name>*, *\<docker-registry-server-url>*, _\<registry-username>_ и _\<password>_. Для реестра контейнеров Azure значение *\<docker-registry-server-url>* указывается в формате `https://<azure-container-registry-name>.azurecr.io`. При использовании любого реестра помимо Docker Hub имя образа должно начинаться с полного доменного имени (FQDN) реестра. Имя реестра контейнеров Azure будет выглядеть следующим образом: `<azure-container-registry>.azurecr.io/mydockerimage`. 

```azurecli-interactive
az webapp config container set --name <app_name> --resource-group myResourceGroup --docker-custom-image-name <azure-container-registry-name>.azurecr.io/mydockerimage --docker-registry-server-url https://<azure-container-registry-name>.azurecr.io --docker-registry-server-user <registry-username> --docker-registry-server-password <password>
```

> [!NOTE]
> Обязательно укажите `https://` в *\<docker-registry-server-url>*.
>

Команда показывает вывод, похожий на следующую строку JSON, которая указывает на то, что изменение конфигурации выполнено успешно:

```json
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "slotSetting": false,
    "value": "mydockerimage"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_URL",
    "slotSetting": false,
    "value": "<azure-container-registry-name>.azurecr.io"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
    "slotSetting": false,
    "value": "<registry-username>"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
    "slotSetting": false,
    "value": null
  }
]
```

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Создание в Azure веб-приложения Docker Python с подключением к базе данных PostgreSQL](tutorial-docker-python-postgresql-app.md)
