---
title: Развертывание приложения .NET в контейнере в Azure Service Fabric | Документация Майкрософт
description: Узнайте, как поместить имеющееся приложение .NET в контейнер с помощью Visual Studio и выполнять отладку контейнеров в Service Fabric локально. Помещенное в контейнер приложение отправляется в реестр контейнеров Azure и развертывается в кластере Service Fabric. При развертывании в Azure приложение использует базу данных SQL Azure для хранения данных.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/07/2018
ms.author: ryanwi
ms.openlocfilehash: d0b3ce1fcabbc69c30e316a69e492da7c75d23ef
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34207491"
---
# <a name="tutorial-deploy-a-net-application-in-a-windows-container-to-azure-service-fabric"></a>Руководство по развертыванию приложения .NET в контейнере Windows в Azure Service Fabric

В этом руководстве рассматривается, как поместить имеющееся приложение ASP.NET в контейнер и упаковать его в качестве приложения Service Fabric.  Запустите контейнеры локально в кластере разработки Service Fabric, а затем разверните приложение в Azure.  Приложение сохраняет данные в [базе данных SQL Azure](/azure/sql-database/sql-database-technical-overview). 

Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * Помещение имеющегося приложения в контейнер с использованием Visual Studio.
> * Создание базы данных SQL Azure
> * Создание реестра контейнеров Azure.
> * Развертывание приложения Service Fabric в Azure.

## <a name="prerequisites"></a>предварительным требованиям

1. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
2. Установите [Docker CE для Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description), чтобы иметь возможность запускать контейнеры в Windows 10.
3. Установите [среду выполнения Service Fabric версии 6.2 или более поздней](service-fabric-get-started.md) и [пакет SDK Service Fabric версии 3.1](service-fabric-get-started.md) или более поздней.
4. [Установите Visual Studio 2017](https://www.visualstudio.com/) версии 15.7 или более поздней, а также рабочие нагрузки **разработка Azure** и **ASP.NET и веб-разработка**.
5. Установите [Azure PowerShell][link-azure-powershell-install].
 

## <a name="download-and-run-fabrikam-fiber-callcenter"></a>Загрузка и запуск Fabrikam Fiber CallCenter
Скачайте пример приложения [Fabrikam Fiber CallCenter][link-fabrikam-github].  Щелкните ссылку **скачивания архива**.  Из каталога *sourceCode* в файле *fabrikam.zip* извлеките файл *sourceCode.zip*, а затем извлеките каталог *VS2015* на компьютер.

Убедитесь, что приложение Fabrikam Fiber CallCenter создается и выполняется без ошибок.  Запустите Visual Studio с правами **администратора** и откройте файл [FabrikamFiber.CallCenter.sln][link-fabrikam-github].  Нажмите клавишу F5, чтобы отладить и запустить приложение.

![Веб-пример Fabrikam][fabrikam-web-page]

## <a name="containerize-the-application"></a>Помещение приложения в контейнер
Щелкните правой кнопкой мыши проект **FabrikamFiber.Web** > **Добавить** > **Container Orchestrator Support** (Поддержка оркестратора контейнеров).  Выберите **Service Fabric** в качестве оркестратора контейнеров и нажмите кнопку **ОК**.

В решении создается новый проект приложения Service Fabric **FabrikamFiber.CallCenterApplication**.  Dockerfile добавляется в имеющийся проект **FabrikamFiber.Web**.  Каталог **PackageRoot** также добавляется в проект **FabrikamFiber.Web**, который содержит манифест служб и параметры для новой службы FabrikamFiber.Web. 

Контейнер готов к сборке и упаковке в приложение Service Fabric. После создания образа контейнера на компьютере можно отправить его в любой реестр контейнеров и развернуть на любом узле, чтобы запустить.

## <a name="create-an-azure-sql-db"></a>Создание базы данных SQL Azure
При выполнении приложения Fabrikam Fiber CallCenter в рабочей среде данные должны сохраняться в базе данных. Пока нет способа гарантировать сохранение данных в контейнере, поэтому хранить рабочие данные в SQL Server в контейнере нельзя.

Мы рекомендуем [базу данных SQL Azure](/azure/sql-database/sql-database-get-started-powershell). Для установки и запуска управляемой базы данных SQL Server в Azure выполните следующий скрипт.  При необходимости измените переменные скрипта. *clientIP* — это IP-адрес компьютера разработчика.  При наличии корпоративного брандмауэра IP-адрес компьютера разработчика не может быть IP-адресом, которому отказано в доступе к Интернету.  Вы также можете задать правило брандмауэра сервера для базы данных SQL через [портал Azure](https://portal.azure.com), где указан IP-адрес компьютера.

```powershell
$subscriptionID="<subscription ID>"

# Sign in to your Azure account and select your subscription.
Login-AzureRmAccount -SubscriptionId $subscriptionID 

# The data center and resource name for your resources.
$dbresourcegroupname = "fabrikam-fiber-db-group"
$location = "southcentralus"

# The logical server name: Use a random value or replace with your own value (do not capitalize).
$servername = "fab-fiber-$(Get-Random)"

# Set an admin login and password for your database.
# The login information for the server.
$adminlogin = "ServerAdmin"
$password = "Password@123"

# The IP address of your development computer that accesses the SQL DB.
$clientIP = "24.18.117.76"

# The database name.
$databasename = "call-center-db"

# Create a new resource group for your deployment and give it a name and a location.
New-AzureRmResourceGroup -Name $dbresourcegroupname -Location $location

# Create the SQL server.
New-AzureRmSqlServer -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

# Create the firewall rule to allow your development computer to access the server.
New-AzureRmSqlServerFirewallRule -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowClient" -StartIpAddress $clientIP -EndIpAddress $clientIP

# Creeate the database in the server.
New-AzureRmSqlDatabase  -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -RequestedServiceObjectiveName "S0"

Write-Host "Server name is $servername"
```

## <a name="update-the-web-config"></a>Обновление веб-конфигурации
Вернитесь в проект **FabrikamFiber.Web**, обновите строку подключения в файле **web.config**, чтобы она указывала на SQL Server в контейнере.  Обновите часть *сервера* строки подключения сервером, созданным предыдущим скриптом. 

```xml
<add name="FabrikamFiber-Express" connectionString="Server=tcp:fab-fiber-1300282665.database.windows.net,1433;Initial Catalog=call-center-db;Persist Security Info=False;User ID=ServerAdmin;Password=Password@123;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;" providerName="System.Data.SqlClient" />
<add name="FabrikamFiber-DataWarehouse" connectionString="Server=tcp:fab-fiber-1300282665.database.windows.net,1433;Initial Catalog=call-center-db;Persist Security Info=False;User ID=ServerAdmin;Password=Password@123;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;" providerName="System.Data.SqlClient" />
  
```
>[!NOTE]
>Вы можете использовать любой SQL Server для локальной отладки, который доступен с вашего узла. Тем не менее **localdb** не поддерживает взаимодействие типа `container -> host`. Если для создания конечной сборки веб-приложения вы хотите использовать другую базу данных SQL, добавьте еще одну строку подключения в файле *web.release.config*.

## <a name="run-the-containerized-application-locally"></a>Выполнение контейнеризованного приложения локально
Нажмите клавишу **F5**, чтобы запустить и отладить приложение в контейнере в локальном кластере разработки Service Fabric.

## <a name="create-a-container-registry"></a>Создание реестра контейнеров
Теперь, когда приложение выполняется локально, запустите подготовку к развертыванию в Azure.  Образы контейнеров должны храниться в реестре контейнеров.  Создайте [реестр контейнеров Azure](/azure/container-registry/container-registry-intro) с помощью следующего скрипта.  Перед развертыванием приложения в Azure отправьте образ контейнера в этот реестр.  Если приложение развертывается в кластере в Azure, образ контейнера извлекается из этого реестра.

```powershell
# Variables
$acrresourcegroupname = "fabrikam-acr-group"
$location = "southcentralus"
$registryname="fabrikamregistry"

New-AzureRmResourceGroup -Name $acrresourcegroupname -Location $location

$registry = New-AzureRMContainerRegistry -ResourceGroupName $acrresourcegroupname -Name $registryname -EnableAdminUser -Sku Basic
```

## <a name="create-a-service-fabric-cluster-on-azure"></a>Создание кластера Service Fabric в Azure
Приложения Service Fabric работают в кластере, наборе виртуальных или физических машин, подключенных к сети.  Перед развертыванием приложения в Azure сначала создается кластер Service Fabric в Azure.

Вы можете:
- Создать тестовый кластер с помощью Visual Studio. Этот вариант позволяет создать безопасный кластер непосредственно из Visual Studio с нужными конфигурациями. 
- [Создать безопасный кластер из шаблона](service-fabric-tutorial-create-vnet-and-windows-cluster.md).

Создавая кластер, выбирайте SKU с поддержкой контейнеров (например, Windows Server Datacenter 2016 с контейнерами). В этом руководстве создается кластер из Visual Studio, который идеально подходит для сценариев тестирования. При создании кластера другим способом или использовании имеющегося кластера можно скопировать и вставить конечную точку подключения или выбрать ее из подписки. 

1. Щелкните правой кнопкой мыши проект приложения **FabrikamFiber.CallCenterApplication** в обозревателе решений, а затем выберите **Опубликовать**.

2. Войдите в систему, используя свою учетную запись Azure, чтобы получить доступ к своим подпискам. 

3. Щелкните раскрывающийся список для **конечной точки подключения** и выберите вариант **Создать новый кластер...**    
        
4. В диалоговом окне **Создание кластера** измените следующие параметры:

    1. Укажите имя кластера в поле **Имя кластера**, а также подписку и расположение, которые нужно использовать.
    2. Необязательно. Можно изменить количество узлов. По умолчанию у вас есть три узла — минимальное количество, необходимое для тестирования сценариев Service Fabric.
    3. Выберите вкладку **Сертификат**. На этой вкладке введите пароль, используемый для защиты сертификата кластера. С помощью этого сертификата вы защитите кластер. Также можно изменить путь, используемый для сохранения сертификата. Visual Studio также может импортировать сертификат автоматически, так как это необходимый шаг для публикации приложения в кластер.
    4. Перейдите на вкладку **Сведения о виртуальной машине**. Укажите пароль, который вы хотите использовать для виртуальных машин, входящих в состав кластера. Имя пользователя и пароль можно использовать для удаленного подключения к виртуальным машинам. Также необходимо выбрать размер виртуальной машины и при необходимости изменить ее образ.
    5. На вкладке **Дополнительно** укажите порты приложения, которые нужно открыть в подсистеме балансировки нагрузки при развертывании кластера. В обозревателе решений откройте файл FabrikamFiber.Web->PackageRoot->ServiceManifest.xml.  Порт для веб-интерфейса указан в разделе **Конечная точка**.  Вы также можете добавить имеющийся ключ Application Insights, который будет использоваться для направления файлов журнала приложений.
    6. После внесения всех необходимых изменений в параметры нажмите кнопку **Создать**. 
5. Процесс создания займет несколько минут. Когда кластер будет полностью создан, в окне вывода отобразится соответствующее уведомление.
    

## <a name="allow-your-application-running-in-azure-to-access-the-sql-db"></a>Настройка доступа приложения, выполняющегося в Azure, к базе данных SQL
Ранее было создано правило брандмауэра SQL для предоставления доступа к выполняющемуся локально приложению.  Теперь необходимо разрешить приложению, выполняющемуся в Azure, получать доступ к базе данных SQL.  Создайте [конечную точку службы виртуальной сети](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview) для кластера Service Fabric, а затем создайте правило, чтобы разрешить этой конечной точке получать доступ к базе данных SQL.

```powershell
# Create a virtual network service endpoint
$clusterresourcegroup = "fabrikamfiber.callcenterapplication_RG"
$resource = Get-AzureRmResource -ResourceGroupName $clusterresourcegroup -ResourceType Microsoft.Network/virtualNetworks | Select-Object -first 1
$vnetName = $resource.Name

Write-Host 'Virtual network name: ' $vnetName 

# Get the virtual network by name.
$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName $clusterresourcegroup `
  -Name              $vnetName

Write-Host "Get the subnet in the virtual network:"

# Get the subnet, assume the first subnet contains the Service Fabric cluster.
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet | Select-Object -first 1

$subnetName = $subnet.Name
$subnetID = $subnet.Id
$addressPrefix = $subnet.AddressPrefix

Write-Host "Subnet name: " $subnetName " Address prefix: " $addressPrefix " ID: " $subnetID

# Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet.
$vnet = Set-AzureRmVirtualNetworkSubnetConfig `
  -Name            $subnetName `
  -AddressPrefix   $addressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint Microsoft.Sql | Set-AzureRmVirtualNetwork

$vnet.Subnets[0].ServiceEndpoints;  # Display the first endpoint.

# Add a SQL DB firewall rule for the virtual network service endpoint
$subnet = Get-AzureRmVirtualNetworkSubnetConfig `
  -Name           $subnetName `
  -VirtualNetwork $vnet;

$VNetRuleName="ServiceFabricClusterVNetRule"
$vnetRuleObject1 = New-AzureRmSqlServerVirtualNetworkRule `
  -ResourceGroupName      $dbresourcegroupname `
  -ServerName             $servername `
  -VirtualNetworkRuleName $VNetRuleName `
  -VirtualNetworkSubnetId $subnetID;
```
## <a name="deploy-the-application-to-azure"></a>Развертывание приложения в Azure
Теперь, когда приложение готово, можно развернуть его в кластер в Azure напрямую из Visual Studio.  В обозревателе решений щелкните правой кнопкой мыши проект приложения **FabrikamFiber.CallCenterApplication**, а затем выберите **Опубликовать**.  В разделе **конечной точки подключения** выберите конечную точку кластера, которая была создана ранее.  В **реестре контейнеров Azure** выберите реестр контейнеров, который был создан ранее.  Щелкните **Опубликовать**, чтобы развернуть приложение в кластере Azure.

![публикации приложения][publish-app]

Отслеживайте ход развертывания в окне вывода.  После развертывания приложения откройте браузер и введите адрес кластера и порт приложения. Например, http://http://fabrikamfibercallcenter.southcentralus.cloudapp.azure.com:8659/.

![Веб-пример Fabrikam][fabrikam-web-page-deployed]

## <a name="clean-up-resources"></a>Очистка ресурсов
После завершения работы не забудьте удалить все созданные ресурсы.  Простейший способ — это удалить группы ресурсов, которые содержат кластер Service Fabric, базу данных SQL Azure и службу "Реестр контейнеров Azure".

```powershell
$dbresourcegroupname = "fabrikam-fiber-db-group"
$acrresourcegroupname = "fabrikam-acr-group"
$clusterresourcegroupname="fabrikamcallcentergroup"

# Remove the Azure SQL DB
Remove-AzureRmResourceGroup -Name $dbresourcegroupname

# Remove the container registry
Remove-AzureRmResourceGroup -Name $acrresourcegroupname

# Remove the Service Fabric cluster
Remove-AzureRmResourceGroup -Name $clusterresourcegroupname
```

## <a name="next-steps"></a>Дополнительная информация
Из этого руководства вы узнали, как выполнить следующие задачи:

> [!div class="checklist"]
> * Помещение имеющегося приложения в контейнер с использованием Visual Studio.
> * Создание базы данных SQL Azure
> * Создание реестра контейнеров Azure.
> * Развертывание приложения Service Fabric в Azure.

В следующей части руководства описывается настройка [мониторинга контейнера](service-fabric-tutorial-monitoring-wincontainers.md).


[link-fabrikam-github]: https://aka.ms/fabrikamcontainer
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

[fabrikam-web-page]: media/service-fabric-host-app-in-a-container/fabrikam-web-page.png
[fabrikam-web-page-deployed]: media/service-fabric-host-app-in-a-container/fabrikam-web-page-deployed.png
[publish-app]: media/service-fabric-host-app-in-a-container/publish-app.png