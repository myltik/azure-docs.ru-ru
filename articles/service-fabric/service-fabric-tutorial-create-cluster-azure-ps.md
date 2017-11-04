---
title: "Создание кластера Service Fabric в Azure | Документы Майкрософт"
description: "Узнайте, как создать кластер Service Fabric c Windows или Linux в Azure с помощью PowerShell."
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
ms.openlocfilehash: 29a8d5cf9a59a08dc63df1dbd601aa1f509cad95
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2017
---
# <a name="create-a-secure-cluster-on-azure-using-powershell"></a>Создание безопасного кластера в Azure с помощью PowerShell
Это первое руководство в серии, в котором показано, как переместить приложение .NET в облако с помощью кластеров и контейнеров Service Fabric. В следующих разделах вы узнаете, как создать кластер Service Fabric (с Windows или Linux) в Azure. После выполнения всех указаний из этого руководства у вас будет безопасный кластер, запущенный в облаке, в котором можно развертывать приложения.

## <a name="prerequisites"></a>Предварительные требования
Перед началом работы с этим руководством выполните следующие действия:
- Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Установите [пакет SDK для Service Fabric](service-fabric-get-started.md).
- Установите [модуль Azure Powershell 4.1 или более поздней версии](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) (при необходимости [установите службу Azure PowerShell](/powershell/azure/overview) или [обновите ее до новой версии](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0#update-azps)).


# <a name="create-a-service-fabric-cluster"></a>Создание кластера Service Fabric

Этот сценарий создает предварительную версию кластера с одним узлом в Service Fabric. Кластер защищен с помощью самозаверяющего сертификата, который создается вместе с кластером и помещается в хранилище ключей. Кластеры из одного узла не поддерживают масштабирование за пределы одной виртуальной машины, кроме того, кластеры предварительной версии нельзя обновить до последующих версий.

Чтобы рассчитать затраты, связанные с запуском кластера Service Fabric в Azure, используйте [калькулятор цен Azure](https://azure.microsoft.com/pricing/calculator/).
Дополнительные сведения о создании кластеров Service Fabric см. в статье [Создание кластера Service Fabric в Azure с помощью Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

## <a name="log-in-to-azure"></a>Вход в Azure
Откройте консоль PowerShell, войдите в Azure и выберите подписку, в которую нужно развернуть кластер:

   ```powershell
   Login-AzureRmAccount
   Select-AzureRmSubscription -SubscriptionId <subscription-id>
   ```

## <a name="cluster-parameters"></a>Параметры кластера

   Этот сценарий использует следующие параметры и основные понятия. Настройте параметры в соответствии с требованиями.

   | Параметр       | Описание | Рекомендуемое значение |
   | --------------- | ----------- | --------------- |
   | Расположение | Регион Azure, в котором развертывается кластер. | *Например, westeurope, eastasia, eastus* |
   | Имя     | Имя создаваемого кластера. Имя должно содержать от 4 до 23 знаков и может иметь только строчные буквы, цифры и дефисы. | *Например, bobs-sfpreviewcluster* |
   | ResourceGroupName   | Имя группы ресурсов, в которой будет создан кластер. | *Например, myresourcegroup* |
   | VmSku  | Номер SKU виртуальной машины, используемый для узлов. | *Любой допустимый номер SKU виртуальной машины* |
   | ОС  | ОС виртуальной машины, используемая для узлов. | *Любая допустимая ОС виртуальной машины* |
   | keyVaultName | Имя нового хранилища ключей KeyVault для связывания с кластером. | *Например, mykeyvault* |
   | ClusterSize (размер кластера) | Число виртуальных машин в кластере (может быть 1 или 3–99).| *Укажите только одну виртуальную машину для предварительной версии кластера* |
   | CertificateSubjectName | Имя субъекта создаваемого сертификата. | *Указывается в формате <name>.<location>.cloudapp.azure.com* |

## <a name="default-parameter-values"></a>Значения параметров по умолчанию
Параметры **виртуальной машины** являются необязательными. Если вы не укажете их, будет задано имя администратора по умолчанию (vmadmin) и PowerShell предложит вам ввести пароль виртуальной машины до создания кластера.

**Порты.** По умолчанию будут использоваться порты 80 и 8081. Вы можете указать дополнительные порты, следуя указаниям для [портов в кластерах Service Fabric](https://docs.microsoft.com/en-us/azure/service-fabric/create-load-balancer-rule).

**Диагностика** включена по умолчанию.

**Служба DNS** по умолчанию не включена.

**Обратный прокси-сервер** также не включен по умолчанию.

## <a name="create-the-cluster-with-your-parameters"></a>Создание кластера с использованием параметров

Определившись с параметрами, соответствующими требованиям, выполните следующую команду для создания безопасного кластера Service Fabric и его сертификата.

Этот сценарий можно изменить, чтобы включить дополнительные параметры. Дополнительные сведения о параметрах для создания кластера см. в статье о командлете [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/new-azurermservicefabriccluster.md).

>[!NOTE]
>Перед выполнением этой команды необходимо создать папку для вывода сертификата кластера.

```powershell

    # Certificate variables. This will create and encrypt a password to be used by Service Fabric.
    $certpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force

    # You must create the folder where you want to output the certificate on your machine before executing this step.
    $certfolder="c:\mycertificates\"

    # Variables for common values. Change the values to fit your needs.
    $clusterloc="WestUS"
    $clustername = "mysfcluster"
    $groupname="mysfclustergroup"       
    $vmsku = "Standard_D2_v2"
    $vaultname = "mykeyvault"
    $subname="$clustername.$clusterloc.cloudapp.azure.com"

    # Set the number of cluster nodes. Possible values: 1, 3-99
    $clustersize=1

    # Create the Service Fabric cluster and its self-signed certificate. The OS specified here allows you use this cluster with any applications that are also leveraging containers.
    New-AzureRmServiceFabricCluster -Name $clustername -ResourceGroupName $groupname -Location $clusterloc `
    -ClusterSize $clustersize -CertificateSubjectName $subname `
    -CertificatePassword $certpwd -CertificateOutputFolder $certfolder `
    -OS WindowsServer2016DatacenterwithContainers -VmSku $vmsku -KeyVaultName $vaultname
```

Этот процесс может занять несколько минут. По завершении настройки отобразятся сведения о кластере, созданном в Azure. А сертификат кластера будет скопирован в каталог -CertificateOutputFolder в пути, указанном для этого параметра.

Запишите URL-адрес кластера **ManagementEndpoint**, который выглядит примерно так: *https://mycluster.westeurope.cloudapp.azure.com:19080*.

## <a name="import-the-certificate"></a>Импорт сертификата

Если создание кластера завершилось успешно, выполните следующую команду, чтобы убедиться, что можно использовать самозаверяющий сертификат.

```powershell

    # Connect to the cluster using by installing the certificate into the Personal (My) store of the current user on your computer.
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
            -FilePath C:\mycertificates\mysfclustergroup20170531104310.pfx `
            -Password $certpwd
```

Эта команда устанавливает сертификат для текущего пользователя компьютера.  Этот сертификат понадобится вам для доступа к Service Fabric Explorer и просмотра сведений о работоспособности кластера.


## <a name="view-your-cluster-optional"></a>Просмотр кластера (необязательно)

После получения кластера и импортированного сертификата можно подключиться к кластеру и просмотреть его работоспособность. Есть множество способов подключения через Service Fabric Explorer или PowerShell.

### <a name="service-fabric-explorer"></a>Service Fabric Explorer
Сведения о работоспособности кластера можно просмотреть, открыв Service Fabric Explorer. Для этого в браузере перейдите по URL-адресу **ManagementEndpoint** для кластера, а затем выберите сертификат, сохраненный на компьютере.

>[!NOTE]
>При открытии обозревателя Service Fabric появится сообщение об ошибке сертификата, так как используется самозаверяющий сертификат. В браузере Edge необходимо щелкнуть *Сведения*, а затем ссылку *Перейти на веб-страницу*. В браузере Chrome нужно щелкнуть *Дополнительно*, а затем ссылку *Продолжить*.

### <a name="powershell"></a>PowerShell

Модуль PowerShell **Service Fabric** предоставляет многие командлеты для управления кластерами, приложениями и службами Service Fabric.  Для подключения к безопасному кластеру используйте командлет [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster). Сведения об отпечатке сертификата и конечной точке подключения можно найти в выходных данных из предыдущего шага.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

Вы также можете убедиться, что подключение установлено и кластер находится в работоспособном состоянии, выполнив командлет [Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth).

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="next-steps"></a>Дальнейшие действия
В этом руководстве вы узнали, как создать защищенный кластер Service Fabric в Azure с помощью PowerShell.

Теперь перейдите к следующему руководству, из которого вы узнаете, как развернуть существующее приложение.
> [!div class="nextstepaction"]
> [Развертывание существующего приложения .NET с помощью Docker Compose](service-fabric-host-app-in-a-container.md)

---
