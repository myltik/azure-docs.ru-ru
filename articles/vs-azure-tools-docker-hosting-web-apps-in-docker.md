---
title: "Развертывание контейнера Docker ASP.NET Core в Linux на удаленном узле Docker | Документация Майкрософт"
description: "Узнайте, как использовать средства Visual Studio для Docker для развертывания веб-приложения ASP.NET Core в контейнере Docker, работающем на виртуальной машине узла Docker под управлением Linux в Azure"
services: azure-container-service
documentationcenter: .net
author: mlearned
manager: douge
editor: 
ms.assetid: e5e81c5e-dd18-4d5a-a24d-a932036e78b9
ms.service: azure-container-service
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/08/2016
ms.author: mlearned
ms.translationtype: Human Translation
ms.sourcegitcommit: be747170a0d8a7a6defd790a3f8a122c4d397671
ms.openlocfilehash: 4a87ee69f23779bf4f6f5db40bc05edbcfc7668d
ms.contentlocale: ru-ru
ms.lasthandoff: 05/23/2017


---
# <a name="deploy-an-aspnet-container-to-a-remote-docker-host"></a>Развертывание контейнера ASP.NET на удаленном узле Docker
## <a name="overview"></a>Обзор
Docker — это облегченная платформа контейнеров, чем-то похожая на виртуальную машину, которую можно использовать для размещения приложений и служб.
В этом руководстве пошагово описывается использование расширения [Средства Visual Studio для Docker](https://docs.microsoft.com/en-us/dotnet/articles/core/docker/visual-studio-tools-for-docker) для развертывания приложения ASP.NET Core на узле Docker в Azure с помощью PowerShell.

## <a name="prerequisites"></a>Предварительные требования
Для работы с этим руководством предварительно необходимо сделать следующее:

* Создать виртуальную машину для узла Docker в Azure, как описано в статье [Использование машины Docker с драйвером Azure](virtual-machines/linux/docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Установить последнюю версию [Visual Studio](https://www.visualstudio.com/downloads/).
* Скачать [пакет SDK для Microsoft ASP.NET Core 1.0](https://go.microsoft.com/fwlink/?LinkID=809122).
* Установить [Docker для Windows](https://docs.docker.com/docker-for-windows/install/).

## <a name="1-create-an-aspnet-core-web-app"></a>1. Создание веб-приложения ASP.NET Core
Давайте создадим простое приложение ASP.NET Core, которое мы будем использоваться в этом руководстве.

[!INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## <a name="2-add-docker-support"></a>2) Добавление поддержки Docker
[!INCLUDE [create-aspnet5-app](../includes/vs-azure-tools-docker-add-docker-support.md)]

## <a name="3-use-the-dockertaskps1-powershell-script"></a>3. Использование сценария PowerShell DockerTask.ps1
1. В командной строке PowerShell перейдите в корневой каталог проекта. 
   
   ```
   PS C:\Src\WebApplication1>
   ```
2. Убедитесь, что удаленный узел работает. Должно отображаться состояние Running. 
   
   ```
   docker-machine ls
   NAME         ACTIVE   DRIVER   STATE     URL                        SWARM   DOCKER    ERRORS
   MyDockerHost -        azure    Running   tcp://xxx.xxx.xxx.xxx:2376         v1.10.3
   ```
   
3. Создайте приложение с использованием параметра -Build.
   
   ```
   PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Build -Environment Release -Machine mydockerhost
   ```  

   > ```
   > PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Build -Environment Release 
   > ```  
   > 
   > 
4. Запустите приложение с помощью параметра -Run.
   
   ```
   PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Run -Environment Release -Machine mydockerhost
   ```
   
   > ```
   > PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Run -Environment Release 
   > ```
   > 
   > 
   
   По завершении работы Docker должны отобразиться результаты, аналогичные приведенным ниже.
   
   ![Просмотр приложения][3]

[0]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/docker-props-in-solution-explorer.png
[1]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/change-docker-machine-name.png
[2]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/launch-application.png
[3]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/view-application.png

