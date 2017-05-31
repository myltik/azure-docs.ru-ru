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
ms.date: 05/10/2017
ms.author: mikhegn
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: a965fcb9dd5caf9656af5ae381b25baaaa01ec6d
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017

---

# <a name="deploy-a-net-app-in-a-container-to-azure-service-fabric"></a>Развертывание приложения-контейнера .NET в Azure Service Fabric

В этом руководстве объясняется, как развернуть имеющееся приложение ASP.NET в контейнер Windows с помощью Visual Studio 2017 с обновлением 3 (предварительная версия). Затем описывается, как развернуть контейнер в Azure с использованием Visual Studio Team Services и разместить его в кластер Service Fabric.

## <a name="prerequisites"></a>Предварительные требования

1. Установите [Docker CE для Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description), чтобы иметь возможность запускать контейнеры в Windows 10.
2. Ознакомьтесь с [кратким руководством по работе с контейнерами в Windows 10][link-container-quickstart].
3. В этой статье используется пример приложения **Fabrikam Fiber**, который можно скачать [здесь][link-fabrikam-github].
4. [Azure PowerShell][link-azure-powershell-install]
5. [Расширение инструментов непрерывной поставки для Visual Studio 2017][link-visualstudio-cd-extension]

>[!NOTE]
>Если вы запускаете образы контейнеров Windows на компьютере впервые, Docker CE должен развернуть базовые образы для контейнеров. В этом руководстве используются образы размером 14 ГБ. Выполните следующую команду в PowerShell, чтобы получить базовые образы:
>
>```cmd
>docker pull microsoft/mssql-server-windows-developer
>docker pull microsoft/aspnet:4.6.2
>```

## <a name="containerize-the-application"></a>Помещение приложения в контейнер

Для запуска приложения в контейнере необходимо добавить **поддержку Docker** в проекте Visual Studio. При добавлении в приложение **поддержки Docker** происходят две вещи. В проект будет добавлен файл _docker_. Этот новый файл описывает способ создания образа контейнера. Затем в решение будет добавлен новый проект _docker-compose_. Этот проект содержит несколько файлов docker-compose, которые можно использовать, чтобы описать, как должен выполняться контейнер.

См. дополнительные сведения о работе с [инструментами для контейнера Visual Studio][link-visualstudio-container-tools].

### <a name="add-docker-support"></a>Добавление поддержки Docker

1. В Visual Studio откройте файл **FabrikamFiber.CallCenter.sln**.

2. Щелкните правой кнопкой мыши проект **FabrikamFiber.Web** > **Добавить** > **Поддержка Docker**.

### <a name="add-support-for-sql"></a>Добавление поддержки SQL

Это приложение использует SQL в качестве поставщика данных, поэтому для запуска приложения требуется SQL Server. В этом руководстве SQL Server будет выполняться в контейнере.
Чтобы сообщить Docker, что мы хотим запустить SQL Server в контейнере, создадим ссылку на образ контейнера SQL Server в файле docker-compose.override.yml проекта docker-compose. В этом случае при отладке приложения в Visual Studio будет использоваться SQL Server, запущенный в контейнере.

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

   >[!NOTE]
   >Если вы хотите, чтобы SQL Server всегда запускался в контейнере, можно добавить приведенный выше код в файл docker-compose.yml вместо файла docker-compose.override.yml.


4. Измените узел `fabrikamfiber.web`, добавив новый дочерний узел с именем `depends_on:`. Это гарантирует, что служба `db` (контейнер SQL Server) будет запущена перед веб-приложением (fabrikamfiber.web).

   ```yml
     fabrikamfiber.web:
       ports:
         - "80"
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

Далее в этом руководстве мы будет использовать Visual Studio Team Services для создания и развертывания контейнера, отправки его в реестр контейнеров Azure и последующего развертывания в Service Fabric, запущенной в Azure.

## <a name="create-a-service-fabric-cluster"></a>Создание кластера Service Fabric

Если кластер Service Fabric для развертывания приложения уже имеется, этот шаг можно пропустить. В противном случае создадим кластер Service Fabric.

>[!NOTE]
>В следующей процедуре создается кластер Service Fabric, защищенный с помощью самозаверяющего сертификата, который помещается в хранилище Key Vault, созданное в процессе развертывания. Дополнительные сведения об использовании проверки подлинности Azure Active Directory см. в статье [Создание кластера Service Fabric в Azure с помощью Azure Resource Manager][link-servicefabric-create-secure-clusters].

1. Скачайте локальную копию файлов шаблона и параметров Azure, указанных ниже.
    * [Шаблон Azure Resource Manager для Service Fabric][link-sf-clustertemplate] — это шаблон Resource Manager, который определяет кластер Service Fabric.
    * [Файл параметров шаблона][link-sf-clustertemplate-parameters] — это файл параметров для настройки развертывания кластера.
2. Настройте следующие параметры в файле параметров.
  
   | Параметр       | Описание |
   | --------------- | ----------- |
   | clusterName     | Имя кластера. |
   | adminUserName   | Учетная запись локального администратора виртуальных машин кластера. |
   | adminPassword   | Пароль локального администратора виртуальных машин кластера. |
   | clusterLocation | Регион Azure для развертывания кластера. |
   | vmInstanceCount | Количество узлов в кластере (может быть 1). |

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
       -TemplateFile C:\users\me\downloads\PreviewSecureClusters.json `
       -ParameterFile C:\users\me\downloads\myCluster.parameters.json `
       -CertificateOutputFolder C:\users\me\desktop\ `
       -CertificatePassword $pwd `
       -CertificateSubjectName "mycluster.westeurope.cloudapp.azure.com" `
       -ResourceGroupName myclusterRG
   ```

   >[!NOTE]
   >Параметр `-CertificateSubjectName` должен соответствовать параметру clusterName, указанному в файле параметров, а домен необходимо привязать к выбранному региону Azure, например `clustername.eastus.cloudapp.azure.com`.
   
    После завершения настройки будут выведены сведения о кластере, созданном в Azure, а также скопирован сертификат в каталог -CertificateOutputFolder.

8. **Дважды щелкните** сертификат, чтобы установить его на локальном компьютере.

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
   
   После завершения настройки контейнер будет развертываться в Service Fabric при каждой отправке обновлений в репозиторий.

7. **Начните сборку** с помощью **Team Explorer**, чтобы увидеть работу приложения-контейнера в Service Fabric.

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
[link-sf-clustertemplate-parameters]: https://aka.ms/securepreviewonelineclusterparameters

[image-web-preview]: media/service-fabric-host-app-in-a-container/fabrikam-web-sample.png
[image-source-control]: media/service-fabric-host-app-in-a-container/add-to-source-control.png
[image-publish-repo]: media/service-fabric-host-app-in-a-container/publish-repo.png
[image-setup-ci]: media/service-fabric-host-app-in-a-container/configure-continuous-integration.png

