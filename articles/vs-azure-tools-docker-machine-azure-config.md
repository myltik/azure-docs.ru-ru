---
title: Создание узлов Docker в Azure с помощью виртуальной машины Docker | Документация Майкрософт
description: Описывается использование машины Docker для создания узлов Docker в Azure.
services: azure-container-service
documentationcenter: na
author: mlearned
manager: douge
editor: ''
ms.assetid: 7a3ff6e1-fa93-4a62-b524-ab182d2fea08
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/08/2016
ms.author: mlearned
ms.openlocfilehash: 766d327a87ed13e04166d71c3d9ae0a1e7a66d19
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23124502"
---
# <a name="create-docker-hosts-in-azure-with-docker-machine"></a>Создание узлов Docker в Azure с помощью машины Docker
Для запуска контейнеров [Docker](https://www.docker.com/) требуется виртуальная машина узла с запущенной управляющей программой Docker.
В этой статье описывается использование команды [docker-machine](https://docs.docker.com/machine/) , которая используется для создания новых виртуальных машин Linux, настроенных с помощью управляющей программы Docker, запущенной в Azure. 

**Примечание.** 

* *Для выполнения действий, описанных в этой статье, требуется машина Docker версии 0.9.0-rc2 или более поздней версии.*
* *Поддержка контейнеров Windows на машинах Docker будет реализована в ближайшем будущем*

## <a name="create-vms-with-docker-machine"></a>Создание виртуальных машин с помощью машины Docker
Создавайте виртуальные машины узла Docker в Azure с помощью команды `docker-machine create`, используя драйвер `azure`. 

Для драйвера Azure потребуется ваш идентификатор подписки. Для получения идентификатора подписки Azure можно воспользоваться [интерфейсом командной строки Azure](cli-install-nodejs.md) или [порталом Azure](https://portal.azure.com). 

**Использование портала Azure**

* В области навигации слева выберите **Подписки** и скопируйте идентификатор подписки.

**Использование Azure CLI**

* Введите ```azure account list``` и скопируйте идентификатор подписки.

Чтобы просмотреть параметры и их значения по умолчанию, введите `docker-machine create --driver azure` .
Также можно ознакомиться с [документацией по драйверу Docker Azure](https://docs.docker.com/machine/drivers/azure/) . 

В приведенном ниже примере используются [значения по умолчанию](https://github.com/docker/machine/blob/master/drivers/azure/azure.go#L22), но при необходимости можно задать следующие значения. 

* azure-dns в качестве имени, связанного с созданными общедоступным IP-адресом и сертификатами. Это DNS-имя вашей виртуальной машины. В таком случае можно безопасно остановить виртуальную машину, освободить динамический IP-адрес и выполнить повторное подключение, после того как виртуальная машина начнет работу с новым IP-адресом. Префикс имени должен быть уникальным для этого региона: UNIQUE_DNSNAME_PREFIX.westus.cloudapp.azure.com.
* Открытые порта 80 на виртуальной машине для исходящего доступа к Интернету.
* Размер виртуальной машины для использования более быстрого хранилища класса Premium.
* Хранилище класса Premium, используемое для диска виртуальной машины.

```
docker-machine create -d azure --azure-subscription-id <Your AZURE_SUBSCRIPTION_ID> --azure-dns <Your UNIQUE_DNSNAME_PREFIX> --azure-open-port 80 --azure-size Standard_DS1_v2 --azure-storage-type "Premium_LRS" mydockerhost 
```

## <a name="choose-a-docker-host-with-docker-machine"></a>Выбор узла Docker с помощью машины Docker
Когда в машине Docker есть запись для узла, можно установить узел по умолчанию при выполнении команд Docker.

## <a name="using-powershell"></a>с использованием PowerShell.
```powershell
docker-machine env MyDockerHost | Invoke-Expression 
```

## <a name="using-bash"></a>Использование Bash
```bash
eval $(docker-machine env MyDockerHost)
```

Теперь можно выполнять команды Docker в указанном узле.

```
docker ps
docker info
```

## <a name="run-a-container"></a>Запуск контейнера
Теперь, когда узел настроен, можно запустить простой веб-сервер, чтобы проверить правильность настроек узла.
В этом примере мы используем стандартный образ nginx, указываем, что он должен прослушивать порт 80, и что при перезапуске виртуальной машины узла контейнер также должен перезапускаться (`--restart=always`). 

```bash
docker run -d -p 80:80 --restart=always nginx
```

Результаты должны быть примерно следующими:

```
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
83f52fbfa5f8: Pull complete
fa664caa1402: Pull complete
Digest: sha256:12127e07a75bda1022fbd4ea231f5527a1899aad4679e3940482db3b57383b1d
Status: Downloaded newer image for nginx:latest
25942c35d86fe43c688d0c03ad478f14cc9c16913b0e1c2971cb32eb4d0ab721
```

## <a name="test-the-container"></a>Проверка контейнера
Проверьте запущенные контейнеры с помощью `docker ps`:

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                         NAMES
d5b78f27b335        nginx               "nginx -g 'daemon off"   5 minutes ago       Up 5 minutes        0.0.0.0:80->80/tcp, 443/tcp   goofy_mahavira
```

Введите `docker-machine ip <VM name>`, чтобы узнать IP-адрес, который вводится в браузере:

```
PS C:\> docker-machine ip MyDockerHost
191.237.46.90
```

![Запущенный контейнер nginx](./media/vs-azure-tools-docker-machine-azure-config/nginxsuccess.png)

## <a name="summary"></a>Сводка
С помощью машины Docker можно легко подготовить узлы Docker в Azure к выполнению проверок отдельных узлов Docker.
Сведения о рабочем размещении контейнеров см. в разделе [Служба контейнеров Azure](http://aka.ms/AzureContainerService).

Сведения о разработке приложений .NET Core с помощью Visual Studio см. в разделе [Docker Tools for Visual Studio](http://aka.ms/DockerToolsForVS) (Инструменты Docker для Visual Studio).

