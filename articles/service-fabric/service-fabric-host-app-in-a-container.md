---
title: "Развертывание приложения .NET в контейнере в Azure Service Fabric | Документация Майкрософт"
description: "В этом руководстве объясняется, как упаковать приложение .NET в контейнер Docker с помощью Visual Studio. Затем это приложение-контейнер развертывается в кластер Service Fabric."
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/19/2017
ms.author: mikhegn
ms.translationtype: Human Translation
ms.sourcegitcommit: d9ae8e8948d82b9695d7d144d458fe8180294084
ms.openlocfilehash: 56788914452c0f3a7072d6b2805866072b9b7fea
ms.contentlocale: ru-ru
ms.lasthandoff: 05/23/2017

---

# <a name="deploy-a-net-app-in-a-container-to-azure-service-fabric"></a>Развертывание приложения-контейнера .NET в Azure Service Fabric

В этом руководстве объясняется, как развернуть имеющееся приложение ASP.NET в контейнер Windows с помощью Visual Studio 2017 с обновлением 3 (предварительная версия). Затем описывается, как развернуть контейнер в Azure с использованием Visual Studio Team Services и разместить его в кластер Service Fabric.

## <a name="prerequisites"></a>Предварительные требования

1. Установите [Docker CE для Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description), чтобы иметь возможность запускать контейнеры в Windows 10.
2. Ознакомьтесь с [кратким руководством по работе с контейнерами в Windows 10][link-container-quickstart].
3. В этом руководстве используется пример приложения **Fabrikam Fiber CallCenter**, который можно скачать [здесь][link-fabrikam-github].
4. [Azure PowerShell][link-azure-powershell-install]
5. [Расширение инструментов непрерывной поставки для Visual Studio 2017][link-visualstudio-cd-extension]
6. [Подписка Azure][link-azure-subscription] и [учетная запись Visual Studio Team Services][link-vsts-account]. Задания в этом учебнике можно выполнить, используя бесплатные уровни всех служб.

>[!NOTE]
>Если вы запускаете образы контейнеров Windows на компьютере впервые, Docker CE потребуется развернуть базовые образы для контейнеров. В этом руководстве используются образы размером 14 ГБ. Выполните следующую команду в PowerShell, чтобы получить базовые образы:
>1. docker pull microsoft/mssql-server-windows-developer
>2. docker pull microsoft/aspnet:4.6.2

## <a name="containerize-the-application"></a>Помещение приложения в контейнер

Для запуска приложения в контейнере необходимо добавить **поддержку Docker** в проекте Visual Studio. При добавлении в приложение **поддержки Docker** происходят две вещи. В проект будет добавлен файл _docker_. Этот новый файл описывает способ создания образа контейнера. Затем в решение будет добавлен новый проект _docker-compose_. Новый проект содержит несколько файлов docker-compose. Файлы docker-compose можно использовать для описания порядка выполнения контейнера.

См. дополнительные сведения о работе с [инструментами для контейнера Visual Studio][link-visualstudio-container-tools].

### <a name="add-docker-support"></a>Добавление поддержки Docker

1. В Visual Studio откройте файл **FabrikamFiber.CallCenter.sln**.

2. Щелкните правой кнопкой мыши проект **FabrikamFiber.Web** > **Добавить** > **Поддержка Docker**.

### <a name="add-support-for-sql"></a>Добавление поддержки SQL

Это приложение использует SQL в качестве поставщика данных, поэтому для запуска приложения требуется SQL Server. В этом руководстве мы используем экземпляр SQL Server, выполняемый в контейнере для локальной отладки.
Чтобы выполнить SQL Server в контейнере во время отладки, мы создадим ссылку на образ контейнера SQL Server в файле docker-compose.override.yml. 

1. Откройте **обозреватель решений**.

2. Откройте **docker-compose** > **docker-compose.yml** > **docker-compose.override.yml**.

3. В узле `services:` добавьте новый узел с именем `db:`. Этот узел объявляет выполнение SQL Server в контейнере.

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
   >Выполнение SQL Server в контейнере не поддерживает сохранение данных после остановки контейнера. Не используйте эту конфигурацию для рабочей среды.

4. Измените узел `fabrikamfiber.web`, добавив новый дочерний узел с именем `depends_on:`. Это гарантирует, что служба `db` (контейнер SQL Server) будет запущена перед веб-приложением (fabrikamfiber.web).

   ```yml
     fabrikamfiber.web:
       depends_on:
         - db
   ```

5. В проекте **FabrikamFiber.Web** обновите строку подключения в файле **web.config**, чтобы она указывала на SQL Server в контейнере.

   ```xml
   <add name="FabrikamFiber-Express" connectionString="Data Source=db,1433;Database=FabrikamFiber;User Id=sa;Password=Password1;MultipleActiveResultSets=True" providerName="System.Data.SqlClient" />
   <add name="FabrikamFiber-DataWarehouse" connectionString="Data Source=db,1433;Database=FabrikamFiber;User Id=sa;Password=Password1;MultipleActiveResultSets=True" providerName="System.Data.SqlClient" />
   ```

   >[!NOTE]
   >Если для создания конечной сборки веб-приложения вы хотите использовать другой SQL Server, добавьте еще одну строку подключения в файл web.release.config.

6. Нажмите клавишу **F5** для запуска и отладки приложения в контейнере.

   Edge откроет страницу запуска определенного приложения с помощью IP-адреса контейнера во внутренней сети NAT (обычно 172.x.x.x). Дополнительные сведения об отладке приложений в контейнерах с помощью Visual Studio 2017 см. в [этой статье][link-debug-container].

   ![пример fabrikam в контейнере][image-web-preview]

Теперь можно создать приложение и упаковать его в контейнер. После создания образа контейнера на компьютере можно отправить его в любой реестр контейнеров и развернуть на любом узле, чтобы запустить.

Далее в этом руководстве мы будем использовать Visual Studio Team Services для развертывания контейнера в службе Service Fabric, запущенной в Azure.

## <a name="create-a-service-fabric-cluster"></a>Создание кластера Service Fabric

Если кластер Service Fabric для развертывания приложения уже имеется, этот шаг можно пропустить. В противном случае создадим кластер Service Fabric.

>[!NOTE]
>В следующей процедуре создается кластер Service Fabric из одного узла (одной виртуальной машины) предварительной версии, защищенный с помощью самозаверяющего сертификата, который помещается в хранилище KeyVault.
>Кластеры из одного узла не поддерживают масштабирование за пределы одной виртуальной машины, кроме того, кластеры предварительной версии нельзя обновить до последующих версий.
>Чтобы рассчитать затраты, связанные с выполнением кластера Service Fabric в Azure, используйте [калькулятор цен Azure][link-azure-pricing-calculator].
>Дополнительные сведения о создании кластеров Service Fabric см. в статье [Создание кластера Service Fabric в Azure с помощью Azure Resource Manager][link-servicefabric-create-secure-clusters].

1. Скачайте локальную копию шаблона Azure Resource Manager и файл параметров из этого репозитория GitHub:
    * [Шаблон Azure Resource Manager для Service Fabric][link-sf-clustertemplate]
       - **azuredeploy.json** — шаблон Azure Resource Manager, который определяет кластер Service Fabric.
       - **azuredeploy.parameters.json** — файл параметров для настройки развертывания кластера.
2. Настройте следующие параметры в файле параметров.
  
   | Параметр       | Описание | Рекомендуемое значение |
   | --------------- | ----------- | --------------- |
   | clusterLocation | Регион Azure для развертывания кластера. | *Например, westeurope, eastasia, eastus* |
   | clusterName     | Имя кластера. | *Например, bobs-sfpreviewcluster* |
   | adminUserName   | Учетная запись локального администратора виртуальных машин кластера. | *Любое допустимое имя пользователя Windows Server* |
   | adminPassword   | Пароль локального администратора виртуальных машин кластера. | *Любой допустимый пароль Windows Server* |
   | clusterCodeVersion | Выполняемая версия Service Fabric. (255.255.X.255 — предварительные версии). | **255.255.5713.255** |
   | vmInstanceCount | Число виртуальных машин в кластере (может быть 1 или 3–99). | **1** |

3. Откройте **PowerShell**.
4. **Выполните вход** в Azure.

   ```powershell
   Login-AzureRmAccount
   ```

5. Выберите **подписку**, в которой необходимо развернуть кластер.

   ```powershell
   Select-AzureRmSubscription -SubscriptionId <subscription-id>
   ```

6. Создайте и **зашифруйте пароль** для сертификата, используемого в Service Fabric.

   ```powershell
   $pwd = "<your password>" | ConvertTo-SecureString -AsPlainText -Force
   ```

7. **Создайте кластер**, выполнив следующую команду:

   ```powershell
      New-AzureRmServiceFabricCluster
          -TemplateFile C:\Users\me\Desktop\azuredeploy.json `
          -ParameterFile C:\Users\me\Desktop\azuredeploy.parameters.json `
          -CertificateOutputFolder C:\Users\me\Desktop\ `
          -CertificatePassword $pwd `
          -CertificateSubjectName "mycluster.westeurope.cloudapp.azure.com" `
          -ResourceGroupName myclusterRG
   ```

   >[!NOTE]
   >Параметр `-CertificateSubjectName` должен соответствовать параметру clusterName, указанному в файле параметров, а домен необходимо привязать к выбранному региону Azure, например `clustername.eastus.cloudapp.azure.com`.
   
    После завершения настройки будут выведены сведения о кластере, созданном в Azure, а также скопирован сертификат в каталог -CertificateOutputFolder.

8. **Дважды щелкните** сертификат, чтобы запустить мастер импорта сертификатов.

9. Используйте параметры по умолчанию, но не забудьте установить флажок **Пометить этот ключ как экспортируемый** в шаге **Защита закрытого ключа**. Visual Studio необходимо экспортировать сертификат при настройке реестра контейнеров Azure, чтобы выполнить проверку подлинности кластера Service Fabric далее в этом руководстве.

10. Теперь можно открыть обозреватель Service Fabric в браузере. URL-адрес соответствует значению параметра **ManagementEndpoint** в выходных данных командлета PowerShell, например *https://mycluster.westeurope.cloudapp.azure.com:19080* 

>[!NOTE]
>При открытии обозревателя Service Fabric появится сообщение об ошибке сертификата, так как используется самозаверяющий сертификат. В браузере Edge необходимо щелкнуть *Сведения*, а затем ссылку *Перейти на веб-страницу*. В браузере Chrome нужно щелкнуть *Дополнительно*, а затем ссылку *Продолжить*.

>[!NOTE]
>В случае сбоя создания кластера можно повторно запустить команду, которая обновляет уже развернутые ресурсы. Если сертификат создан в ходе неудачного развертывания, создается новый сертификат. Сведения об устранении неполадок при создании кластера см. в статье [Создание кластера Service Fabric в Azure с помощью Azure Resource Manager][link-servicefabric-create-secure-clusters].

## <a name="getting-the-application-ready-for-the-cloud"></a>Подготовка приложения для облака

Чтобы подготовить приложение для запуска в службе Service Fabric в Azure, необходимо выполнить два действия.

1. Предоставить порт, по которому мы будем связываться с веб-приложением в кластере Service Fabric.
2. Указать готовую к работе базу данных SQL для нашего приложения.

### <a name="1-exposing-the-web-application-in-service-fabric"></a>1. Предоставление приложения в Service Fabric
В кластере Service Fabric, который мы настроили, порт 80 открыт по умолчанию в службе Azure Load Balancer, которая распределяет входящий трафик кластера. Мы можем предоставить наш контейнер по этому порту с помощью файла docker-compose.yml.

1. Откройте **обозреватель решений**.

2. Откройте **docker-compose** > **docker-compose.yml**.

3. Измените узел `fabrikamfiber.web`, добавив новый дочерний узел с именем `ports:` и строку `- "80:80"`. Полный файл docker-compose.yml должен выглядеть следующим образом:

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

### <a name="2-provide-a-production-ready-sql-database-for-our-application"></a>2) Указание готовой к работе базы данных SQL для приложения
Когда приложение будет помещено в контейнер и включена локальная отладка, мы настроим выполнение SQL Server в контейнере. Этот подход является хорошим решением при локальной отладке приложения, так как не требуется сохранение данных в базе данных. Однако при выполнении в рабочей среде данные требуется сохранять в базе данных. Пока нет способа гарантировать сохранение данных в контейнере, поэтому хранить рабочие данные в SQL Server в контейнере нельзя.

Соответственно, если для работы службы требуется сохраняемая база данных SQL, мы рекомендуем использовать базу данных SQL Azure. Сведения о настройке и запуске управляемого экземпляра SQL Server в Azure см. в статье [Azure SQL Database Quickstarts][[link-azure-sql]].

>[!NOTE]
>Не забудьте изменить строку подключения так, чтобы она указывала на SQL Server, в файлеweb.release.config в проекте FabrikamFiber.Web.
>Это приложение корректно завершает работу, если база данных SQL недоступна. Можно продолжить работу и развернуть приложение без SQL Server.

## <a name="deploy-with-visual-studio"></a>Развертывание с помощью Visual Studio

Чтобы настроить развертывание с помощью Visual Studio Team Services, необходимо установить [расширение инструментов непрерывной поставки для Visual Studio 2017][link-visualstudio-cd-extension]. Это расширение упрощает развертывание в Azure за счет настройки Visual Studio Team Services и позволяет развернуть приложение в кластер Service Fabric.

Чтобы начать работу, необходимо разместить код в системе управления версиями. В остальной части этого раздела предполагается использование **git**.

1. В нижнем правом углу Visual Studio щелкните **Добавить в систему управления версиями** > **Git** (или вариант, который вы предпочитаете).

   ![кнопка добавления в систему управления версиями][image-source-control]

2. На панели _Team Explorer_ нажмите клавишу **Опубликовать репозиторий Git**.

3. Выберите имя репозитория VSTS и нажмите кнопку **Опубликовать репозиторий**.

   ![публикация репозитория в VSTS][image-publish-repo]

Теперь, когда ваш код синхронизируется с исходным репозиторием VSTS, можно настроить непрерывную интеграцию и непрерывную поставку.

1. В _обозревателе решений_ щелкните правой кнопкой мыши **Решение** > **Настроить непрерывную поставку**.

2. Выберите подписку Azure.

3. Задайте для **типа узла** **кластер Service Fabric**.

   >[!NOTE]
   >В зависимости от типов создаваемых контейнеров существуют дополнительные варианты размещения приложений в контейнерах в Azure. 

4. Задайте в качестве **целевого узла** кластер Service Fabric, созданный при работе с предыдущим разделом.

5. Выберите **реестр контейнеров** для публикации контейнера.

   >[!TIP]
   >Чтобы создать реестр контейнеров, нажмите кнопку **Изменить**.
    
6. Нажмите кнопку **ОК**.

   ![настройка непрерывной интеграции Service Fabric][image-setup-ci]
   
   После завершения настройки контейнер развертывается в Service Fabric. Каждый раз, когда вы отправляете обновления в репозиторий, выполняется новая сборка и выпуск.
   
   >[!NOTE]
   >Сборка образов контейнеров занимает примерно 15 минут.
   >Первое развертывание в кластере Service Fabric вызывает загрузку базовых образов контейнеров Windows Server Core. Загрузка занимает дополнительно 5–10 минут.

7. Перейдите к приложению Fabrikam Call Center, используя URL-адрес кластера, например *http://mycluster.westeurope.cloudapp.azure.com*

Теперь, когда решение центра обработки вызовов Fabrikam помещено в контейнер и развернуто, можно открыть [портал Azure][link-azure-portal], чтобы увидеть выполнение приложения в Service Fabric. Чтобы протестировать приложение, откройте браузер и перейдите по URL-адресу кластера Service Fabric.

## <a name="next-steps"></a>Дальнейшие действия

- [Инструменты Visual Studio для Docker][link-visualstudio-container-tools]
- [Создание первого приложения-контейнера Service Fabric][link-servicefabric-containers]
- [Создание первого приложения Azure Service Fabric][link-servicefabric-createapp]

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
[link-azure-pricing-calculator]: https://azure.microsoft.com/en-us/pricing/calculator/
[link-azure-subscription]: https://azure.microsoft.com/en-us/free/
[link-vsts-account]: https://www.visualstudio.com/team-services/pricing/
[link-azure-sql]: /sql-database

[image-web-preview]: media/service-fabric-host-app-in-a-container/fabrikam-web-sample.png
[image-source-control]: media/service-fabric-host-app-in-a-container/add-to-source-control.png
[image-publish-repo]: media/service-fabric-host-app-in-a-container/publish-repo.png
[image-setup-ci]: media/service-fabric-host-app-in-a-container/configure-continuous-integration.png
