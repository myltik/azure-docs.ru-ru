---
title: "Применение пользовательского образа Docker для веб-приложений Azure для контейнеров | Документация Майкрософт"
description: "Как применять пользовательский образ Docker для веб-приложений Azure для контейнеров."
keywords: "служба приложений azure, веб-приложение, docker, контейнер"
services: app-service
documentationcenter: 
author: SyntaxC4
manager: SyntaxC4
editor: 
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/03/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 760772d1d1c79dd4a1114c36971de0b3693ab74f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="use-a-custom-docker-image-for-azure-web-app-for-containers"></a>Применение пользовательского образа Docker для веб-приложений Azure для контейнеров

Платформа [Веб-приложения для контейнеров](app-service-linux-intro.md) предоставляет встроенные образы Docker на базе Linux с поддержкой определенных версий, включая PHP 7.0 и Node.js 4.5. Платформа "Веб-приложения для контейнеров" использует технологию контейнеров Docker для размещения встроенных образов и пользовательских образов в качестве платформы как услуги. В этом руководстве описано, как создать пользовательский образ Docker для веб-приложений для контейнеров, если нет встроенного образа для нужного языка или в случае, если для приложения требуется определенная конфигурация, которую не предоставляет встроенный образ.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством необходимы указанные ниже компоненты.

* [Git.](https://git-scm.com/downloads)
* Активная [подписка Azure](https://azure.microsoft.com/pricing/free-trial/).
* [Docker](https://docs.docker.com/get-started/#setup)
* [Учетная запись Docker Hub](https://docs.docker.com/docker-id/).

[!INCLUDE [Free trial note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Скачивание примера приложения

В окне терминала выполните следующую команду, чтобы клонировать репозиторий с примером приложения на локальный компьютер, а затем перейдите в каталог, в котором содержится образец кода.

```bash
git clone https://github.com/Azure-Samples/use-custom-docker-image.git
cd use-custom-docker-image
```

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="build-the-image-from-the-docker-file"></a>Создание образа на основе файла Docker

Следующий файл Docker содержит среду Python, необходимую для запуска приложения. Образ также настраивает сервер [SSH](https://www.ssh.com/ssh/protocol/) для обеспечения безопасного взаимодействия между контейнером и узлом.

```docker
# Use an official Python runtime as a parent image
FROM python

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
ADD . /app

# Run python's package manager and install the flask package
RUN pip install flask

# Configure ports
EXPOSE 2222 80

# Run apt-get, to install the SSH server
RUN apt-get update \ 
    && apt-get install -y --no-install-recommends openssh-server \
    && echo "root:Docker!" | chpasswd

#Copy the sshd_config file to its new location
COPY sshd_config /etc/ssh/

# Start the SSH service
RUN service ssh start

# Copy init_container.sh to the /bin directory
COPY init_container.sh /bin/
    
# Run the chmod command to change permissions on above file in the /bin directory
RUN chmod 755 /bin/init_container.sh 

# run commands in init_container.sh
CMD ["/bin/init_container.sh"]
```

Чтобы создать образ Docker, выполните команду `docker build`, указав имя `mydockerimage` и тег `v1.0.0`. Замените `<docker-id>` идентификатором вашей учетной записи Docker Hub.

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

После выполнения команды должен появиться результат, аналогичный приведенному ниже.

```bash
# The output from the commands in this article has been shortened for brevity.

Sending build context to Docker daemon  23.04kB
Step 1/13 : FROM python
 ---> 968120d8cbe8
Step 2/13 : WORKDIR /app
 ---> Using cache
 ---> dd6fdca5aa65
Step 3/13 : ADD . /app
 ---> e05c8f4beeae
Get:1 http://security.debian.org jessie/updates InRelease [63.1 kB]
Ign http://deb.debian.org jessie InRelease
Get:2 http://deb.debian.org jessie-updates InRelease [145 kB]
Get:3 http://deb.debian.org jessie Release.gpg [2373 B]
Fetched 9988 kB in 7s (1266 kB/s)
Reading package lists...
Building dependency tree...
Reading state information...
The following extra packages will be installed:
  init-system-helpers libwrap0 openssh-sftp-server
Suggested packages:
  ssh-askpass rssh molly-guard ufw monkeysphere
Recommended packages:
  tcpd xauth ncurses-term
The following NEW packages will be installed:
  init-system-helpers libwrap0 openssh-server openssh-sftp-server
0 upgraded, 4 newly installed, 0 to remove and 3 not upgraded.
Need to get 442 kB of archives.
After this operation, 1138 kB of additional disk space will be used.
Get:1 http://deb.debian.org/debian/ jessie/main libwrap0 amd64 7.6.q-25 [58.5 kB]
Creating SSH2 RSA key; this may take some time ...
2048 f0:e9:fb:69:de:62:a4:5c:a3:7c:b3:41:e9:2e:96:a3 /etc/ssh/ssh_host_rsa_key.pub (RSA)
Creating SSH2 DSA key; this may take some time ...
1024 4a:5e:89:bd:aa:2d:71:bb:0e:3a:32:94:fb:c0:b1:4d /etc/ssh/ssh_host_dsa_key.pub (DSA)
Processing triggers for systemd (215-17+deb8u7) ...
 ---> 5b416a7dcdca
Removing intermediate container 283b3b4623d7
Step 13/13 : CMD python app.py
 ---> Running in 1c776e5e0772
 ---> 1bfc1bbc968d
Removing intermediate container 1c776e5e0772
Successfully built 1bfc1bbc968d
Successfully tagged <docker-id>/myDockerImage:v1.0.0
```

Проверьте, работает ли сборка, запустив контейнер Docker. Выполните команду [​​docker run](https://docs.docker.com/engine/reference/commandline/run/), указав имя и тег образа. Необходимо также указать порт с помощью аргумента `-p`.

```bash
docker run -p 80:2222 <docker-ID>/mydockerimage:v1.0.0
```

Убедитесь, что веб-приложение и контейнер функционируют правильно, открыв веб-приложение локально.

![Локальное тестирование веб-приложения](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

## <a name="push-a-docker-image-to-docker-hub"></a>Отправка образа Docker в центр Docker Hub

Реестр — это приложение, которое содержит образы и предоставляет образ служб и службы контейнеров. Чтобы сделать образ общедоступным, необходимо передать его в реестр. 

<!-- Depending on your requirements, you may have your docker images in a Public Docker Registry, such as Docker Hub, or a Private Docker Registry such as Azure Container Registry. Select the appropriate tab for your scenario below (your selection will switch multiple tabs on this page). -->

> [!NOTE]
> Дополнительные сведения о передаче образа в частный реестр Docker см. в разделе [Передача образа Docker в частный реестр](#push-a-docker-image-to-private-registry-optional).

<!--## [Docker Hub](#tab/docker-hub)-->

Docker Hub — это реестр образов Docker, который позволяет размещать ваши собственные репозитории, как общедоступные, так и частные. Чтобы передать пользовательский образ Docker в общедоступный центр Docker Hub, используйте команду [​​docker push](https://docs.docker.com/engine/reference/commandline/push/) и укажите полное имя и тег образа. Полное имя и тег выглядят как в следующем примере:

```bash
<docker-id>/image-name:tag
```

Если вы не вошли в центр Docker Hub, сделайте это с помощью команды [​​docker login](https://docs.docker.com/engine/reference/commandline/login/), прежде чем передавать образ.

```bash
docker login --username <docker-id> --password <docker-hub-password>
```

При успешном выполнении входа появится сообщение "Вход выполнен". После входа можно передать образ в центр Docker с помощью команды [docker push](https://docs.docker.com/engine/reference/commandline/push/).

```bash
docker push <docker-id>/mydockerimage:v1.0.0 .
```

Убедитесь, что передача завершилось успешно, проверив выходные данные команды.

```bash
The push refers to a repository [docker.io/<docker-id>/python-flask]
e9aa2c6d0f34: Pushed
0fdcb490aeec: Pushed
08ae61c7869c: Pushed
2548e7db2a94: Mounted from library/python
325b9d6f2920: Pushed
815acdffadff: Mounted from library/python
97108d083e01: Mounted from library/python
5616a6292c16: Mounted from library/python
f3ed6cb59ab0: Mounted from library/python
654f45ecb7e3: Mounted from library/python
2c40c66f7667: Mounted from library/python
v1: digest: sha256:a910d5b77e6960c01745a87c35f3d1a13ba73231ac9a4664c5011b1422d59b60 size: 2632
```

<!--
# [Private Registry](#tab/private-registry)

// Move Private Registry instructions here when Tabbed Conceptual bug is fixed

---
-->

## <a name="create-web-app-for-containers"></a>Создание веб-приложения для контейнеров

С помощью веб-приложения Azure можно разместить собственные приложения Linux в облаке. Чтобы создать веб-приложение для контейнеров, выполните команды Azure CLI, которые создают группу, план обслуживания, а затем само веб-приложение. Сначала запустите команду [​​az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) и передайте расположение и уникальное имя.

```azurecli-interactive
az group create --location "West Europe" --name myResourceGroup
```

Должен появиться результат, аналогичный приведенному ниже.

```json
{
  "id": "/subscriptions/432849d3e4-4f90-a782-87c11e-5e59d6dd/resourceGroups/myResourceGroup",
  "location": "westeurope",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

Выполните команду [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create), указав имя группы, чтобы помочь создать план обслуживания приложения. Необходимо присвоить ему уникальное имя и задать параметр `--is-linux`.

```azurecli-interactive
az appservice plan create --name myServicePlan --resource-group myResourceGroup --is-linux
```

Должны отобразиться результаты, как в следующем примере:

```json
  {- Starting...
  "adminSiteName": null,
  "appServicePlanName": "myServicePlan",
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/resourceGroups/myResourceGroup/provide
rs/Microsoft.Web/serverfarms/myServicePlan",
  "kind": "linux",
  "location": "West Europe", 
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capabilities": null,
    "capacity": 1,
    "tier": "Basic"
  },
  "status": "Ready",
  "subscription": "",
  "tags": null,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
}
```

Теперь, когда создана группа ресурсов и план обслуживания, запустите команду [​​az webapp create](https://docs.microsoft.com/cli/azure/webapp#az_webapp_create) для создания веб-приложения. Обратите внимание, что стек времени выполнения имеет версию Python 3.4, а веб-приложение использует группу ресурсов и план обслуживания, настроенные ранее.

```azurecli-interactive
az webapp create -g myResourceGroup -p myServicePlan -n <web-app-name> --runtime "python|3.4" 
```

Результат выполнения команды для создания веб-приложения выглядит следующим образом:

```json
{- Starting ..
  "availabilityState": "Normal",
   "enabled": true,
  "enabledHostNames": [
    "<web-app-name>.azurewebsites.net",
    "<web-app-name>.scm.azurewebsites.net"
  ],
  "ftpPublishingUrl": "ftp://waws-prod-am2-085.ftp.azurewebsites.windows.net/site/wwwroot",
  "gatewaySiteName": null,
  "hostNameSslStates": [
    {
      "hostType": "Standard",
      "name": "<web-app-name>.azurewebsites.net",
    },
  ],
  "hostNames": [
    "<web-app-name>.azurewebsites.net"
  ],
  "hostNamesDisabled": false,
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/5e59d6dd-d3e4-4f90-a782-43284987c11e/resourceGroups/myResourceGroup/providers/Microsoft.
Web/sites/<web-app-name>",
  "lastModifiedTimeUtc": "2017-08-08T21:09:33.693333",
  "location": "West Europe",
  "name": "<web-app-name>",
  "outboundIpAddresses": "13.81.108.99,52.232.76.83,52.166.73.203,52.233.173.39,52.233.159.48",
  "resourceGroup": "myResourceGroup"
}

```

В большинстве веб-приложений есть параметры, которые необходимо настроить. Если вы используете существующий образ Docker, созданный кем-то другим, для образа может потребоваться порт, отличный от порта 80. Чтобы установить `WEBSITES_PORT`, выполните команду [az webapp config](https://docs.microsoft.com/cli/azure/webapp/config/appsettings), как показано в следующем примере кода:

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <web-app-name> --settings WEBSITES_PORT=2222
```

> [!NOTE]
> Параметры приложения учитывают регистр.
>

Убедитесь, что веб-приложение работает, открыв его. Не забудьте указать номер порта.

![Тестирование настройки порта веб-приложения](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="configure-web-app-to-use-docker-container-from-docker-hub"></a>Настройка веб-приложения для использования контейнера Docker из Docker Hub

Команда [az webapp config](https://docs.microsoft.com/cli/azure/webapp/config) позволяет использовать пользовательский образ Docker.

<!-- Depending on your requirements, you may have your docker images in a Public Docker Registry, such as Docker Hub, or a Private Docker Registry, such as Azure Container Registry. Select the appropriate tab for your scenario below: -->

> [!NOTE]
> Дополнительные сведения о выполнении развертывания из частного реестра Docker см. в разделе [Настройка веб-приложения для использования контейнера Docker из частного реестра](#configure-web-app-to-use-docker-container-from-a-private-registry-optional).

<!-- # [Docker Hub](#tab/docker-hub)-->

Чтобы настроить веб-приложение для использования общедоступного реестра Docker, передайте имя приложения, группу ресурсов, имя образа и URL-адрес с помощью команды [​​az webapp config container set](https://docs.microsoft.com/cli/azure/webapp/config/container#az_webapp_config_container_set).

```azurecli-interactive
az webapp config container set --name <web-app-name> --resource-group myResourceGroup --docker-custom-image-name mydockerimage
--docker-registry-server-url <docker-id>/myContainerRegistry
```

При успешном изменении конфигурации отобразятся общие сведения о контейнере.

```bash
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "slotSetting": false,
    "value": "{docker-id}/mydockerimage:v1.0.0"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
    "slotSetting": false,
    "value": "{docker-id}"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
    "slotSetting": false,
    "value": null
  }
]
```

<!-- # [Private Registry](#tab/private-registry)

// Place Private Registry text back here once Tabbed Conceptual bug is fixed

---
-->

## <a name="test-the-application-in-azure"></a>Тестирование приложения в Azure

Перед началом тестирования необходимо перезапустить веб-приложение, используя команду [az webapp restart](https://docs.microsoft.com/cli/azure/webapp#az_webapp_restart), чтобы изменения конфигурации вступили в силу.

```azurecli-interactive
az webapp restart --name <web-app-name> --resource-group myResourceGroup
```

Команда перезапуска перезапускает веб-приложение без участия пользователя, поэтому вы не увидите обратной связи в терминале. После запуска веб-приложения проверьте веб-приложение, перейдя по его URL-адресу `http://<username>.azurewebsites.net`. Убедитесь, что в приложении отображается новое приветственное сообщение.

![Тестирование веб-приложения в Azure](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="change-web-app-and-redeploy"></a>Изменение и повторное развертывание веб-приложения

В каталоге `using-custom-docker-image` находится файл Python с именем `app.py`. Этот файл содержит строку кода, которая отображает сообщение `Hello World!`. Измените строку, чтобы отображалось сообщение `Hello World of Web Apps running in Docker Containers!`.

```python
return "Hello World of Web Apps running in Docker Containers!"
```

После того как вы изменили и сохранили файл Python, необходимо перестроить и передать новый образ Docker. Затем перезапустите веб-приложение, чтобы изменения вступили в силу. Используйте те же команды, которые использовались ранее в этом руководстве. Можно перейти к разделам о [создании образа на основе файла Docker](#build-the-image-from-the-docker-file) и [передачи образа Docker](#push-docker-image). Протестируйте веб-приложение, выполнив инструкции, указанные в разделе [Тестирование приложения в Azure](#tTest-the-application-in-azure).

## <a name="connect-to-web-app-for-containers-using-ssh"></a>Подключение к веб-приложению для контейнеров с помощью SSH

SSH обеспечивает безопасный обмен данными между клиентом и контейнером. Чтобы пользовательский образ Docker поддерживал SSH, встройте его в файл Dockerfile. Включите SSH в самом файле Docker. Инструкции SSH уже добавлены в образец файла dockerfile, поэтому вы можете следовать им, используя собственный пользовательский образ.

* Инструкция [RUN](https://docs.docker.com/engine/reference/builder/#run) вызывает `apt-get`, а затем устанавливает пароль `"Docker!"` для учетной записи root.

    ```docker
    RUN apt-get update \
        && apt-get install -y --no-install-recommends openssh-server \
        && echo "root:Docker!" | chpasswd
    ```

    > [!NOTE]
    > Эта конфигурация не разрешает внешние подключения к контейнеру. SSH доступен только на сайте Kudu и на сайте SCM. Сайты Kudu и SCM проходят проверку подлинности с учетными данными для публикации.

* Инструкция [COPY](https://docs.docker.com/engine/reference/builder/#copy) указывает ядру Docker копировать файл [sshd_config](http://man.openbsd.org/sshd_config) в каталог */etc/ssh/*. Ваш файл конфигурации должен быть создан на основе нашего файла [sshd_config](https://github.com/Azure-App-Service/node/blob/master/6.11/sshd_config) из репозитория GitHub Azure-App-Service.

    > [!NOTE]
    > Файл *sshd_config* должен содержать приведенные ниже элементы. 
    > * `Ciphers` должен содержать по крайней мере один элемент в этом списке: `aes128-cbc,3des-cbc,aes256-cbc`.
    > * `MACs` должен содержать по крайней мере один элемент в этом списке: `hmac-sha1,hmac-sha1-96`.

    ```docker
    #Copy the sshd_config file to its new location
    COPY sshd_config /etc/ssh/
    ```

* Инструкция [EXPOSE](https://docs.docker.com/engine/reference/builder/#expose) предоставляет порт 2222 в контейнере. Даже при указании пароля привилегированного пользователя порт 2222 недоступен из Интернета. Это внутренний порт, который доступен контейнерам внутри мостовой сети виртуальной частной сети. После этого команды скопируют сведения о конфигурации SSH и запустят службу `ssh`.

    ```docker
    # Configure ports
    EXPOSE 2222 80

    #Copy the sshd_config file to its new location
    COPY sshd_config /etc/ssh/

    # Start the SSH service
    RUN service ssh start
    ```

Файл `init_container.sh` в образце кода содержит инструкции по инициализации контейнера при его запуске. Файл Dockerfile использует инструкции [COPY](https://docs.docker.com/engine/reference/builder/#copy), [RUN](https://docs.docker.com/engine/reference/builder/#run) и [CMD](https://docs.docker.com/engine/reference/builder/#cmd) для соответствующего запуска скрипта `init_container.sh`.

```docker
# Copy init_container.sh to the /bin directory
COPY init_container.sh /bin/

# Run the chmod command to change permissions on above file in the /bin directory
RUN chmod 755 /bin/init_container.sh

# run commands in init_container.sh
CMD ["/bin/init_container.sh"]
```

Вы можете проверить выполнение некоторых приложений в контейнере. Чтобы просмотреть контейнер и проверить выполняющиеся процессы, откройте браузер и перейдите по адресу `https://<app name>.scm.azurewebsites.net/webssh/host`. Затем вы будете перенаправлены на страницу с интерактивной консолью. Введите команду `top` в командной строке.

```bash
top
```

Команда `top` выведет все запущенные процессы в контейнере.

```bash
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

Поздравляем! Вы настроили пользовательский образ Docker для веб-приложения Azure для контейнеров.

## <a name="push-a-docker-image-to-private-registry-optional"></a>Передача образа Docker в частный реестр (необязательно)

Реестр контейнеров Azure — это управляемая служба Docker из Azure для размещения частных образов. Развертывания могут быть любого типа, включая [Docker Swarm](https://docs.docker.com/engine/swarm/), [Kubernetes](https://kubernetes.io/) и контейнеры службы приложений Azure. Использование реестра контейнеров Azure аналогично использованию любого частного реестра, поэтому если вам нужно использовать свой собственный реестр, действия для выполнения этой задачи будут схожи.

Создайте реестр контейнеров Azure с помощью команды [az acr create](https://docs.microsoft.com/cli/azure/acr#az_acr_create). Передайте имя, группу ресурсов и `Basic` для SKU. Доступные номера SKU: `Classic`, `Basic`, `Standard` и `Premium`.

```azurecli-interactive
az acr create --name <azure-container-registry-name> --resource-group myResourceGroup --sku Basic --admin-enabled true
```

В результате будут возвращены следующие выходные данные:

```bash
 - Finished ..
Create a new service principal and assign access:
  az ad sp create-for-rbac --scopes /subscriptions/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name> --role Owner --password <password>

Use an existing service principal and assign access:
  az role assignment create --scope /subscriptions/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name> --role Owner --assignee <app-id>
{
  "adminUserEnabled": false,
  "creationDate": "2017-08-09T04:21:09.654153+00:00",
  "id": "/subscriptions/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/{azure-container-registry-name>",
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

Для передачи образа в реестр необходимо предоставить учетные данные, чтобы реестр принял образ. Эти учетные данные можно получить с помощью команды [az acr show](https://docs.microsoft.com/cli/azure/acr/credential#az_acr_credential_show). 

```azurecli-interactive
az acr credential show --name {azure-container-registry-name}
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
  "username": "<azure-container-registry-name>"
}
```

Теперь, когда у вас есть необходимые учетные данные, войдите в реестр контейнеров Azure с помощью команды `docker login`. Для входа укажите имя сервера. Используйте формат `{azure-container-registry-name>.azurecr.io`.

```bash
docker login <azure-container-registry-name>.azurecr.io --username <azure-container-registry-name> --password <password> 
```

Убедитесь, что вход выполнен успешно. Передайте образ с помощью команды `docker push` и обозначьте образ именем реестра, за которым следует имя и тег образа.

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

## <a name="configure-web-app-to-use-docker-container-from-a-private-registry-optional"></a>Настройка веб-приложения для использования контейнера Docker из частного реестра (необязательно)

Можно настроить веб-приложения на платформе Linux для запуска контейнера, хранящегося в реестре контейнеров Azure. Использование реестра контейнеров Azure аналогично использованию любого частного реестра, поэтому если вам нужно использовать свой собственный реестр, действия для выполнения этой задачи будут схожи.

Получите учетные данные для реестра контейнеров с помощью команды [az acr credential show](https://docs.microsoft.com/cli/azure/acr/credential#az_acr_credential_show). Скопируйте имя пользователя и один из паролей, чтобы использовать их для настройки веб-приложения на следующем шаге.

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
  "username": "<azure-container-registry-name>"
}
```

Выполните команду [az webapp config container set](https://docs.microsoft.com/cli/azure/webapp/config/container#az_webapp_config_container_set). Эта команда назначает пользовательский образ Docker для веб-приложения. Обратите внимание, что URL-адрес должен быть в формате `https://{your-registry-username}.azurecr.io`. Кроме того, веб-приложению требуется имя пользователя и пароль, полученные на предыдущем шаге, чтобы получить доступ к реестру контейнеров.

```azurecli-interactive
az webapp config container set --name <web-app-name> --resource-group myResourceGroup --docker-custom-image-name mydockerimage --docker-registry-server-url https://<azure-container-registry-name>.azurecr.io --docker-registry-server-user <docker-id> --docker-registry-server-password <password>
```

> [!NOTE]
> В URL-адресе реестра требуется `https`.
>

Команда показывает вывод, похожий на следующую строку JSON, которая указывает на то, что изменение конфигурации выполнено успешно:

```bash
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "slotSetting": false,
    "value": "mydockerimage"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_URL",
    "slotSetting": false,
    "value": "mycontainerregistry.azurecr.io"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
    "slotSetting": false,
    "value": "{azure-container-registry-name}"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
    "slotSetting": false,
    "value": null
  }
]
```

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Дальнейшие действия

[Вопросы и ответы о платформе "Веб-приложения для контейнеров" в службе приложений Azure](app-service-linux-faq.md)
