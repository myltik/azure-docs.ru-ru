---
title: "Создание кластера Service Fabric в Azure | Документы Майкрософт"
description: "Узнайте, как создать кластер Service Fabric c Windows или Linux в Azure при помощи PowerShell."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/03/2017
ms.author: cristyg
ms.custom: mvc
ms.openlocfilehash: 8b744f6b33a04a707c56e6fc8a4638118170b668
ms.sourcegitcommit: c50171c9f28881ed3ac33100c2ea82a17bfedbff
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/26/2017
---
# <a name="create-a-secure-cluster-in-azure-by-using-powershell"></a>Создание безопасного кластера в Azure с помощью PowerShell
Это первое руководство в серии, в котором показано, как переместить приложение .NET в облако с помощью кластеров и контейнеров Azure Service Fabric. Из следующих разделов вы узнаете, как создать кластер Service Fabric (с Windows или Linux) в Azure. Выполнив инструкции из этого руководства, вы создадите безопасный кластер, запущенный в облаке, в котором можно развертывать приложения.

## <a name="prerequisites"></a>Предварительные требования
Перед началом работы с этим руководством выполните следующие действия:
- Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Установите [пакет SDK для Service Fabric](service-fabric-get-started.md).
- Установите модуль [Azure PowerShell 4.1 или более поздней версии](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). (При необходимости [установите Azure PowerShell](/powershell/azure/overview) или [обновите этот модуль](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0#update-azps)).


## <a name="create-a-service-fabric-cluster"></a>Создание кластера Service Fabric

При помощи этого сценария создается предварительная версия кластера с одним узлом в Service Fabric. Самозаверяющий сертификат защищает кластер. В сценарии создается сертификат вместе с кластером и сертификат помещается в хранилище ключей. Кластеры из одного узла нельзя масштабировать за пределы одной виртуальной машины. Кроме того, кластеры предварительной версии нельзя обновить до новых версий.

Чтобы рассчитать затраты, связанные с запуском кластера Service Fabric в Azure, используйте [калькулятор цен Azure](https://azure.microsoft.com/pricing/calculator/).
Дополнительные сведения о создании кластеров Service Fabric см. в статье [Создание кластера Service Fabric в Azure с помощью Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

## <a name="log-in-to-azure"></a>Вход в Azure
Откройте консоль PowerShell, войдите в Azure и выберите подписку, в которую нужно развернуть кластер:

   ```PowerShell
   Login-AzureRmAccount
   Select-AzureRmSubscription -SubscriptionId <subscription-id>
   ```

## <a name="cluster-parameters"></a>Параметры кластера

   Этот сценарий использует следующие параметры и основные понятия. Настройте параметры в соответствии с требованиями.

   | Параметр       | Описание | Рекомендуемое значение |
   | --------------- | ----------- | --------------- |
   | Расположение | Регион Azure, в котором развертывается кластер. | Например, *westeurope*, *eastasia* или *eastus* |
   | Имя     | Имя создаваемого кластера. Имя должно содержать от 4 до 23 символов. Использовать можно только строчные буквы, цифры и дефисы. | Например, *bobs-sfpreviewcluster* |
   | ResourceGroupName   | Имя группы ресурсов, в которой будет создан кластер. | Например, *myresourcegroup* |
   | VmSku  | Номер SKU виртуальной машины, выбранный для узлов. | Любой допустимый номер SKU виртуальной машины |
   | ОС  | ОС виртуальной машины, выбранная для узлов. | Любая допустимая ОС виртуальной машины |
   | keyVaultName | Имя нового хранилища ключей для связывания с кластером. | Например, *mykeyvault* |
   | ClusterSize (размер кластера) | Количество виртуальных машин в кластере (может быть *1* или *3–99*).| Для предварительной версии кластера укажите только одну виртуальную машину |
   | CertificateSubjectName | Имя субъекта создаваемого сертификата. | Указывается в формате *<name>*.*<location>*.cloudapp.azure.com |

### <a name="default-parameter-values"></a>Значения параметров по умолчанию
**Виртуальная машина**: необязательные параметры. Если вы не укажете их, по умолчанию задается имя администратора *vmadmin* и перед созданием кластера в PowerShell отображается запрос на пароль.

**Порты**: по умолчанию используются порты 80 и 8081. Вы можете указать дополнительные порты, следуя указаниям для [портов в кластерах Service Fabric](https://docs.microsoft.com/en-us/azure/service-fabric/create-load-balancer-rule).

**Диагностика** включена по умолчанию.

**Служба DNS** отключена по умолчанию.

**Обратный прокси-сервер** отключен по умолчанию.

## <a name="create-the-cluster-with-your-parameters"></a>Создание кластера с использованием параметров

Определившись с параметрами, соответствующими требованиям, выполните приведенную ниже команду для создания безопасного кластера Service Fabric и его сертификата.

Этот сценарий можно изменить, чтобы включить дополнительные параметры. Дополнительные сведения о параметрах для создания кластера см. в статье о командлете [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/new-azurermservicefabriccluster.md).

>[!NOTE]
>Перед выполнением этой команды создайте папку, в которой будет храниться сертификат кластера.

```PowerShell

# Set the certificate variables. This creates and encrypts a password that Service Fabric will use.
$certpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force

# You must create the folder where you want to store the certificate on your machine before you start this step.
$certfolder="c:\mycertificates\"

# Set the variables for common values. Change the values to fit your needs.
$clusterloc="WestUS"
$clustername = "mysfcluster"
$groupname="mysfclustergroup"       
$vmsku = "Standard_D2_v2"
$vaultname = "mykeyvault"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# Set the number of cluster nodes. The possible values are 1 and 3-99.
$clustersize=1

# Create the Service Fabric cluster and its self-signed certificate. The OS you specify here lets you use this cluster with any applications that are also using containers.
New-AzureRmServiceFabricCluster -Name $clustername -ResourceGroupName $groupname -Location $clusterloc `
-ClusterSize $clustersize -CertificateSubjectName $subname `
-CertificatePassword $certpwd -CertificateOutputFolder $certfolder `
-OS WindowsServer2016DatacenterwithContainers -VmSku $vmsku -KeyVaultName $vaultname
```

Этот процесс может занять несколько минут. После настройки отобразятся сведения о кластере, созданном в Azure. А сертификат кластера будет скопирован в каталог -CertificateOutputFolder в пути, указанном для этого параметра.

Запишите URL-адрес кластера **ManagementEndpoint**, который выглядит примерно так: https://mycluster.westeurope.cloudapp.azure.com:19080.

## <a name="import-the-certificate"></a>Импорт сертификата

После создания кластера выполните следующую команду, чтобы убедиться, что можно использовать самозаверяющий сертификат:

```PowerShell

# To connect to the cluster, install the certificate into the Personal (My) store of the current user on your computer.
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
-FilePath C:\mycertificates\mysfclustergroup20170531104310.pfx `
-Password $certpwd
```

Эта команда устанавливает сертификат для текущего пользователя компьютера. Этот сертификат понадобится вам для доступа к Service Fabric Explorer и просмотра сведений о работоспособности кластера.


## <a name="view-your-cluster-optional"></a>Просмотр кластера (необязательно)

После получения кластера и импортированного сертификата можно подключиться к кластеру и просмотреть сведения о его работоспособности. Есть несколько способов подключения через Service Fabric Explorer или PowerShell.

### <a name="service-fabric-explorer"></a>Service Fabric Explorer
Чтобы просмотреть сведения о работоспособности кластера, откройте Service Fabric Explorer. Для этого в браузере перейдите по URL-адресу **ManagementEndpoint** кластера и выберите сертификат, сохраненный на компьютере.

>[!NOTE]
>При открытии Service Fabric Explorer появится сообщение об ошибке сертификата, так как используется самозаверяющий сертификат. В браузере Edge выберите **Сведения** и щелкните ссылку **Перейти на веб-страницу**. В браузере Chrome выберите **Дополнительно** и щелкните ссылку **Продолжить**.

### <a name="powershell"></a>PowerShell

Модуль PowerShell Service Fabric предоставляет командлеты для управления кластерами, приложениями и службами Service Fabric. Для подключения к безопасному кластеру используйте командлет [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster). Сведения об отпечатке сертификата и конечной точке подключения можно найти в выходных данных из предыдущего шага.

```PowerShell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster.southcentralus.cloudapp.azure.com:19000 `
-KeepAliveIntervalInSec 10 `
-X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
-FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
-StoreLocation CurrentUser -StoreName My
```

Кроме того, вы можете убедиться, что подключение установлено и кластер находится в работоспособном состоянии, выполнив командлет [Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth).

```PowerShell
Get-ServiceFabricClusterHealth
```

## <a name="next-steps"></a>Дальнейшие действия
Из этого руководства вы узнали, как создать защищенный кластер Service Fabric в Azure с помощью PowerShell.

Теперь перейдите к следующему руководству, чтобы узнать, как развернуть существующее приложение.
> [!div class="nextstepaction"]
> [Развертывание существующего приложения .NET с помощью Docker Compose](service-fabric-host-app-in-a-container.md)

 
