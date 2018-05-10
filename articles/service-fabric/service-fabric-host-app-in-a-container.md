---
title: Развертывание приложения .NET в контейнере в Azure Service Fabric | Документация Майкрософт
description: В этом руководстве объясняется, как упаковать приложение .NET в контейнер Docker с помощью Visual Studio. Затем это приложение-контейнер развертывается в кластер Service Fabric.
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: 04a6fbc56d3c65cfb53339c4178dfa36e2aeb4ea
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="deploy-a-net-application-in-a-windows-container-to-azure-service-fabric"></a>Развертывание приложения .NET в контейнере Windows в Azure Service Fabric

В этом руководстве объясняется, как развернуть имеющееся приложение ASP.NET в контейнере Windows в Azure.

Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * Создание проекта Docker в Visual Studio
> * Помещение имеющегося приложения в контейнер
> * Настройка непрерывной интеграции с Visual Studio и VSTS

## <a name="prerequisites"></a>предварительным требованиям

1. Установите [Docker CE для Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description), чтобы иметь возможность запускать контейнеры в Windows 10.
2. Ознакомьтесь с [кратким руководством по работе с контейнерами в Windows 10][link-container-quickstart].
3. Скачайте пример приложения [Fabrikam Fiber CallCenter][link-fabrikam-github].
4. Установите [Azure PowerShell][link-azure-powershell-install].
5. Установите [расширение инструментов непрерывной поставки для Visual Studio 2017][link-visualstudio-cd-extension].
6. Создайте [подписку Azure][link-azure-subscription] и [учетную запись Visual Studio Team Services][link-vsts-account]. 
7. [Создайте кластер в Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md).

## <a name="create-a-cluster-on-azure"></a>Создание кластера в Azure
Приложения Service Fabric работают в кластере, наборе виртуальных или физических машин, подключенных к сети. Перед созданием и развертыванием приложения [создайте в облаке Azure кластер Service Fabric](service-fabric-tutorial-create-vnet-and-windows-cluster.md). Создавая кластер, выбирайте SKU с поддержкой контейнеров (например, Windows Server Datacenter 2016 с контейнерами).

## <a name="containerize-the-application"></a>Помещение приложения в контейнер

Теперь, когда у вас есть работающий в Azure кластер Service Fabric, создайте и разверните контейнерное приложение. Для запуска приложения в контейнере необходимо добавить **поддержку Docker** в проекте Visual Studio. При добавлении в приложение **поддержки Docker** происходят две вещи. Сначала в проект добавляется _Dockerfile_. Этот новый файл описывает способ создания образа контейнера. Затем в решение будет добавлен новый проект _docker-compose_. Новый проект содержит несколько файлов docker-compose. Файлы docker-compose можно использовать для описания порядка выполнения контейнера.

См. дополнительные сведения о работе с [инструментами для контейнера Visual Studio][link-visualstudio-container-tools].

### <a name="add-docker-support"></a>Добавление поддержки Docker

В Visual Studio откройте файл [FabrikamFiber.CallCenter.sln][link-fabrikam-github].

Щелкните правой кнопкой мыши проект **FabrikamFiber.Web** > **Добавить** > **Поддержка Docker**.

### <a name="add-support-for-sql"></a>Добавление поддержки SQL

Это приложение использует SQL в качестве поставщика данных, поэтому для запуска приложения требуется SQL Server. Сошлитесь на образ контейнера SQL Server в файле docker-compose.override.ym.

В Visual Studio откройте **обозреватель решений**, найдите **docker-compose** и откройте файл **docker-compose.override.yml**.

Перейдите к узлу `services:`, добавьте узел с именем `db:`, определяющий запись SQL Server для контейнера.

```yml
  db:
    image: microsoft/mssql-server-windows-developer
    environment:
      sa_password: "Password1"
      ACCEPT_EULA: "Y"
    ports:
      - "1433"
    healthcheck:
      test: [ "CMD", "sqlcmd", "-U", "sa", "-P", "Password1", "-Q", "select 1" ]
      interval: 1s
      retries: 20
```

>[!NOTE]
>Вы можете использовать любой SQL Server для локальной отладки, который доступен с вашего узла. Тем не менее **localdb** не поддерживает взаимодействие типа `container -> host`.

>[!WARNING]
>Выполнение SQL Server в контейнере не поддерживает сохранение данных. После остановки контейнера данные будут удалены. Не используйте эту конфигурацию для рабочей среды.

Перейдите к узлу `fabrikamfiber.web:` и добавьте дочерний узел с именем `depends_on:`. Это гарантирует, что служба `db` (контейнер SQL Server) будет запущена перед веб-приложением (fabrikamfiber.web).

```yml
  fabrikamfiber.web:
    depends_on:
      - db
```

### <a name="update-the-web-config"></a>Обновление веб-конфигурации

Вернитесь в проект **FabrikamFiber.Web**, обновите строку подключения в файле **web.config**, чтобы она указывала на SQL Server в контейнере.

```xml
<add name="FabrikamFiber-Express" connectionString="Data Source=db,1433;Database=FabrikamFiber;User Id=sa;Password=Password1;MultipleActiveResultSets=True" providerName="System.Data.SqlClient" />

<add name="FabrikamFiber-DataWarehouse" connectionString="Data Source=db,1433;Database=FabrikamFiber;User Id=sa;Password=Password1;MultipleActiveResultSets=True" providerName="System.Data.SqlClient" />
```

>[!NOTE]
>Если для создания конечной сборки веб-приложения вы хотите использовать другой SQL Server, добавьте еще одну строку подключения в файл web.release.config.

### <a name="test-your-container"></a>Тестирование контейнера

Нажмите клавишу **F5** для запуска и отладки приложения в контейнере.

Edge откроет страницу запуска определенного приложения с помощью IP-адреса контейнера во внутренней сети NAT (обычно 172.x.x.x). Дополнительные сведения об отладке приложений в контейнерах с помощью Visual Studio 2017 см. в [этой статье][link-debug-container].

![пример fabrikam в контейнере][image-web-preview]

Контейнер готов к сборке и упаковке в приложение Service Fabric. После создания образа контейнера на компьютере можно отправить его в любой реестр контейнеров и развернуть на любом узле, чтобы запустить.

## <a name="get-the-application-ready-for-the-cloud"></a>Подготовка приложения для облака

Чтобы подготовить приложение для запуска в службе Service Fabric в Azure, необходимо выполнить два действия.

1. Предоставить порт, по которому мы будем связываться с веб-приложением в кластере Service Fabric.
2. Указать готовую к работе базу данных SQL для приложения.

### <a name="expose-the-port-for-the-app"></a>Предоставление порта для приложения
В кластере Service Fabric, который мы настроили, порт *80* открыт по умолчанию в балансировщике нагрузки Azure, который распределяет входящий трафик кластера. Мы можем предоставить наш контейнер по этому порту с помощью файла docker-compose.yml.

В Visual Studio откройте **обозреватель решений**, найдите **docker-compose** и откройте файл **docker-compose.yml**.

Измените узел `fabrikamfiber.web:`, добавив дочерний узел с именем `ports:`.

Добавьте запись строки `- "80:80"`. Файл docker-compose.yml должен выглядеть следующим образом.

```yml
  version: '3'

  services:
    fabrikamfiber.web:
      image: fabrikamfiber.web
      build:
        context: .\FabrikamFiber.Web
        dockerfile: Dockerfile
      ports:
        - "80:80"
```

### <a name="use-a-production-sql-database"></a>Использование рабочей базы данных SQL
При выполнении в рабочей среде данные требуется сохранять в базе данных. Пока нет способа гарантировать сохранение данных в контейнере, поэтому хранить рабочие данные в SQL Server в контейнере нельзя.

Рекомендуется использовать базу данных SQL Azure. Сведения о настройке и запуске управляемого экземпляра SQL Server в Azure см. в статье [о быстрых запусках базы данных SQL Azure][link-azure-sql].

>[!NOTE]
>Не забудьте изменить строку подключения так, чтобы она указывала на SQL Server, в файле **web.release.config** в проекте **FabrikamFiber.Web**.
>
>Это приложение корректно завершает работу, если база данных SQL недоступна. Можно продолжить работу и развернуть приложение без SQL Server.

## <a name="deploy-with-visual-studio-team-services"></a>Развертывание с помощью Visual Studio Team Services

Чтобы настроить развертывание с помощью Visual Studio Team Services, необходимо установить [расширение инструментов непрерывной поставки для Visual Studio 2017][link-visualstudio-cd-extension]. Это расширение упрощает развертывание в Azure за счет настройки Visual Studio Team Services и позволяет развернуть приложение в кластер Service Fabric.

Чтобы начать работу, необходимо разместить код в системе управления версиями. В остальной части этого раздела предполагается использование **git**.

### <a name="set-up-a-vsts-repo"></a>Настройка репозитория VSTS
В нижнем правом углу Visual Studio щелкните **Добавить в систему управления версиями** > **Git** (или вариант, который вы предпочитаете).

![кнопка добавления в систему управления версиями][image-source-control]

На панели _Team Explorer_ нажмите клавишу **Опубликовать репозиторий Git**.

Выберите имя репозитория VSTS и нажмите кнопку **Опубликовать репозиторий**.

![публикация репозитория в VSTS][image-publish-repo]

Теперь, когда ваш код синхронизируется с исходным репозиторием VSTS, можно настроить непрерывную интеграцию и непрерывную поставку.

### <a name="setup-continuous-delivery"></a>Настройка непрерывной доставки

В _обозревателе решений_ щелкните правой кнопкой мыши **Решение** > **Настроить непрерывную поставку**.

Выберите подписку Azure.

Задайте для **типа узла** **кластер Service Fabric**.

Задайте в качестве **целевого узла** кластер Service Fabric, созданный при работе с предыдущим разделом.

Выберите **реестр контейнеров** для публикации контейнера.

>[!TIP]
>Чтобы создать реестр контейнеров, нажмите кнопку **Изменить**.

Нажмите кнопку **ОК**.

![настройка непрерывной интеграции Service Fabric][image-setup-ci]
   
   После завершения настройки контейнер развертывается в Service Fabric. Каждый раз, когда вы отправляете обновления в репозиторий, выполняется новая сборка и выпуск.
   
   >[!NOTE]
   >Сборка образов контейнеров занимает примерно 15 минут.
   >Первое развертывание в кластере Service Fabric вызывает загрузку базовых образов контейнеров Windows Server Core. Загрузка занимает дополнительно 5–10 минут.

Перейдите к приложению Fabrikam Call Center, используя URL-адрес кластера, например *http://mycluster.westeurope.cloudapp.azure.com*

Теперь, когда решение центра обработки вызовов Fabrikam помещено в контейнер и развернуто, можно открыть [портал Azure][link-azure-portal], чтобы увидеть выполнение приложения в Service Fabric. Чтобы протестировать приложение, откройте браузер и перейдите по URL-адресу кластера Service Fabric.

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как выполнить следующие задачи:

> [!div class="checklist"]
> * Создание проекта Docker в Visual Studio
> * Помещение имеющегося приложения в контейнер
> * Настройка непрерывной интеграции с Visual Studio и VSTS

В следующей части руководства описывается настройка [мониторинга контейнера](service-fabric-tutorial-monitoring-wincontainers.md).

<!--   NOTE SURE WHAT WE SHOULD DO YET HERE

Advance to the next tutorial to learn how to bind a custom SSL certificate to it.

> [!div class="nextstepaction"]
> [Bind an existing custom SSL certificate to Azure Web Apps](app-service-web-tutorial-custom-ssl.md)

## Next steps

- [Container Tooling in Visual Studio][link-visualstudio-container-tools]
- [Get started with containers in Service Fabric][link-servicefabric-containers]
- [Creating Service Fabric applications][link-servicefabric-createapp]
-->

[link-debug-container]: /dotnet/articles/core/docker/visual-studio-tools-for-docker
[link-fabrikam-github]: https://aka.ms/fabrikamcontainer
[link-container-quickstart]: /virtualization/windowscontainers/quick-start/quick-start-windows-10
[link-visualstudio-container-tools]: /dotnet/articles/core/docker/visual-studio-tools-for-docker
[link-azure-powershell-install]: /powershell/azure/install-azurerm-ps
[link-servicefabric-create-secure-clusters]: service-fabric-cluster-creation-via-arm.md
[link-visualstudio-cd-extension]: https://aka.ms/cd4vs
[link-servicefabric-containers]: service-fabric-get-started-containers.md
[link-servicefabric-createapp]: service-fabric-create-your-first-application-in-visual-studio.md
[link-azure-portal]: https://portal.azure.com
[link-sf-clustertemplate]: https://aka.ms/securepreviewonelineclustertemplate
[link-azure-pricing-calculator]: https://azure.microsoft.com/pricing/calculator/
[link-azure-subscription]: https://azure.microsoft.com/free/
[link-vsts-account]: https://www.visualstudio.com/team-services/pricing/
[link-azure-sql]: /azure/sql-database/

[image-web-preview]: media/service-fabric-host-app-in-a-container/fabrikam-web-sample.png
[image-source-control]: media/service-fabric-host-app-in-a-container/add-to-source-control.png
[image-publish-repo]: media/service-fabric-host-app-in-a-container/publish-repo.png
[image-setup-ci]: media/service-fabric-host-app-in-a-container/configure-continuous-integration.png
