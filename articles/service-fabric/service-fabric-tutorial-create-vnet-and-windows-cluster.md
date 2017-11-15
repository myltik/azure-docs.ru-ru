---
title: "Создание кластера Service Fabric на платформе Windows в Azure | Документация Майкрософт"
description: "Узнайте, как развернуть кластер Service Fabric на платформе Windows в существующей виртуальной сети с помощью PowerShell."
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
ms.date: 11/02/2017
ms.author: ryanwi
ms.openlocfilehash: 31e35432ecc10b06c7a6400a1e0904e7bc2cd8c9
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2017
---
# <a name="deploy-a-service-fabric-windows-cluster-into-an-azure-virtual-network"></a>Развертывание кластера Service Fabric на платформе Windows в виртуальной сети Azure
Это руководство представляет первую часть цикла. Вы узнаете, как развернуть кластер Service Fabric на платформе Windows в подсети существующей виртуальной сети с помощью PowerShell. После окончания этого учебника у вас будет кластер в облаке, в который можно разворачивать приложения.  Создание кластера Linux с помощью Azure CLI описывается в разделе [Развертывание безопасного кластера Service Fabric на платформе Linux в виртуальной сети Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md).

Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * создание виртуальной сети в Azure с помощью PowerShell;
> * создание хранилища ключей и передача сертификата;
> * создание защищенного кластера Service Fabric в Azure с помощью PowerShell;
> * Защита кластера с помощью сертификата X.509
> * Подключение к кластеру с помощью PowerShell
> * Удаление кластера

Из этого цикла руководств вы узнаете, как выполнять такие задачи:
> [!div class="checklist"]
> * создание защищенного кластера в Azure;
> * [развертывание службы управления API с помощью Service Fabric](service-fabric-tutorial-deploy-api-management.md).

## <a name="prerequisites"></a>Предварительные требования
Перед началом работы с этим руководством выполните следующие действия:
- Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Установите [пакет SDK для Service Fabric и модуль PowerShell](service-fabric-get-started.md)
- Установите модуль [Azure PowerShell версии 4.1 или более поздней](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)

Ниже приведены процедуры для создания кластера Service Fabric с пятью узлами. Чтобы рассчитать затраты, связанные с запуском кластера Service Fabric в Azure, используйте [калькулятор цен Azure](https://azure.microsoft.com/pricing/calculator/).

## <a name="sign-in-to-azure-and-select-your-subscription"></a>Вход в Azure и выбор подписки
В этом руководстве используется Azure PowerShell. При запуске нового сеанса PowerShell войдите в свою учетную запись Azure и выберите подписку перед выполнением команд Azure.
 
Выполните приведенный ниже сценарий, чтобы войти в учетную запись Azure и выбрать подписку.

```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов
Создайте группу ресурсов для развертывания и задайте для нее имя и расположение.

```powershell
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc
```

## <a name="deploy-the-network-topology"></a>Развертывание топологии сети
Настройте топологию сети, в которой будут развернуты служба управления API и кластер Service Fabric. Шаблон Resource Manager [Network.json][network-arm] настроен для создания виртуальной сети, а также подсети и группы безопасности сети (NSG) для Service Fabric и подсети и группы безопасности сети для службы управления API. Дополнительные сведения о виртуальных сетях, подсетях и NSG можно получить [здесь](../virtual-network/virtual-networks-overview.md).

Файл параметров [network.parameters.json][network-parameters-arm] содержит имена подсетей и групп безопасности сети, в которых будут развернуты Service Fabric и служба управления API.  Развертывание службы управления API описывается в [этом руководстве](service-fabric-tutorial-deploy-api-management.md). В рамках этого руководства изменять значения параметров не нужно. Эти значения используются в шаблонах Resource Manager для Service Fabric.  Если изменить эти значения здесь, потребуется изменить их и в других шаблонах Resource Manager, используемых в этом руководстве и [руководстве по развертыванию службы управления API](service-fabric-tutorial-deploy-api-management.md). 

Скачайте шаблон Resource Manager и файл параметров:
- [network.json][network-arm]
- [network.parameters.json][network-parameters-arm]

Используйте следующую команду PowerShell для развертывания шаблона Resource Manager и файла параметров для настройки сети:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateFile C:\winclustertutorial\network.json -TemplateParameterFile C:\winclustertutorial\network.parameters.json -Verbose
```

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>
## <a name="deploy-the-service-fabric-cluster"></a>Развертывание кластера Service Fabric
Когда сетевые ресурсы развернуты, необходимо развернуть кластер Service Fabric в виртуальной сети в подсети и группе безопасности сети, используемых для кластера Service Fabric. Для развертывания кластера в существующих виртуальной сети и подсети (развернутых ранее в этой статье) требуется шаблон Resource Manager.  В рамках этого цикла руководств шаблон предварительно настроен для использования имен виртуальной сети, подсети и группы безопасности сети, настроенных на предыдущем шаге.  

Скачайте шаблон Resource Manager и файл параметров:
- [cluster.json][cluster-arm]
- [cluster.parameters.json][cluster-parameters-arm]

Этот шаблон используется для создания безопасного кластера.  Сертификат кластера — это сертификат X.509, используемый для защиты обмена данными между узлами и для аутентификации конечных точек управления кластера в клиенте управления.  Этот сертификат кластера также предоставляет SSL для API управления HTTPS и Service Fabric Explorer по протоколу HTTPS. Хранилище ключей Azure используется для управления сертификатами кластеров Service Fabric в Azure.  При развертывании кластера в Azure поставщик ресурсов Azure, ответственный за создание кластеров Service Fabric, извлекает сертификаты из хранилища ключей и устанавливает их на виртуальные машины кластера. 

Вы можете использовать сертификат из центра сертификации в качестве сертификата кластера или создать самозаверяющий сертификат для тестирования. Сертификат кластера должен:

- Содержать закрытый ключ.
- Быть создан для обмена ключами, которые можно экспортировать в файл обмена личной информацией (PFX-файл).
- Иметь имя субъекта, совпадающее с доменным именем, которое используется для обращения к кластеру Service Fabric. Это необходимо, чтобы предоставить SSL-протокол конечным точкам управления HTTPS в кластере и Service Fabric Explorer. Не удается получить SSL-сертификат от центра сертификации (ЦС) в домене .cloudapp.azure.com. Необходимо получить имя личного домена для кластера. При запросе сертификата из ЦС имя субъекта сертификата должно совпадать с именем личного домена, используемого для кластера.

Заполните пустые параметры в файле *cluster.parameters.json* для развертывания.

|Параметр|Значение|
|---|---|
|adminPassword|Password#1234|
|adminUserName|vmadmin|
|clusterName|mysfcluster|
|location|southcentralus|

Оставьте значения параметров *certificateThumbprint*, *certificateUrlValue* и *sourceVaultValue* пустыми, чтобы создать самозаверяющий сертификат.  Если необходимо использовать имеющийся сертификат, который вы ранее передали в хранилище ключей, заполните эти значения параметров.

В приведенном ниже сценарии используется командлет [New AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) и шаблон, которые позволяют развернуть в Azure новый кластер. Кроме того, при помощи командлета создается хранилище ключей в Azure, добавляется самозаверяющий сертификат в хранилище ключей и локально скачивается файл сертификата. Вы можете указать имеющийся сертификат или хранилище ключей, воспользовавшись другими параметрами командлета [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster).

```powershell
# Variables.
$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$certfolder="c:\mycertificates\"
$clustername = "mysfcluster"
$vaultname = "clusterkeyvault111"
$vaultgroupname="clusterkeyvaultgroup111"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# Create the Service Fabric cluster.
New-AzureRmServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile 'C:\winclustertutorial\cluster.json' `
-ParameterFile 'C:\winclustertutorial\cluster.parameters.json' -CertificatePassword $certpwd `
-CertificateOutputFolder $certfolder -KeyVaultName $vaultname -KeyVaultResouceGroupName $vaultgroupname -CertificateSubjectName $subname
```

## <a name="connect-to-the-secure-cluster"></a>Подключение к безопасному кластеру
Подключитесь к кластеру с помощью модуля Service Fabric PowerShell, который установлен вместе с пакетом SDK для Service Fabric.  Сначала установите сертификат в личное хранилище (Мое хранилище) текущего пользователя компьютера.  Выполните следующую команду PowerShell:

```powershell
$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\mysfcluster20170531104310.pfx `
        -Password $certpwd
```

Теперь все готово для подключения к защищенному кластеру.

Модуль PowerShell **Service Fabric** предоставляет многие командлеты для управления кластерами, приложениями и службами Service Fabric.  Для подключения к безопасному кластеру используйте командлет [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster). Сведения об отпечатке сертификата и конечной точке подключения можно найти в выходных данных из предыдущего шага.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

Убедитесь, что подключение установлено и кластер находится в работоспособном состоянии, выполнив командлет [Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth).

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="clean-up-resources"></a>Очистка ресурсов
Кластер, который вы только что создали, используется в других статьях этого цикла руководств. Если вы не планируете сразу же переходить к следующей статье, можете удалить кластер и хранилище ключей, чтобы не платить за их использование. Чтобы удалить кластер и все ресурсы, который он использует, проще всего удалить группу ресурсов.

Удалите группу ресурсов и все ресурсы кластера с помощью командлета [Remove-AzureRMResourceGroup](/en-us/powershell/module/azurerm.resources/remove-azurermresourcegroup).  Также удалите группу ресурсов, которая содержит хранилище ключей.

```powershell
Remove-AzureRmResourceGroup -Name $groupname -Force
Remove-AzureRmResourceGroup -Name $vaultgroupname -Force
```

## <a name="next-steps"></a>Дальнейшие действия
Из этого руководства вы узнали, как выполнять такие задачи:

> [!div class="checklist"]
> * создание виртуальной сети в Azure с помощью PowerShell;
> * создание хранилища ключей и передача сертификата;
> * Создание защищенного кластера Service Fabric в Azure с помощью PowerShell
> * Защита кластера с помощью сертификата X.509
> * Подключение к кластеру с помощью PowerShell
> * Удаление кластера

Теперь перейдите к следующему руководству, чтобы узнать, как масштабировать кластер.
> [!div class="nextstepaction"]
> [Scale a Service Fabric cluster](service-fabric-tutorial-scale-cluster.md) (Масштабирование кластера Service Fabric)


[network-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.json
[network-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.parameters.json

[cluster-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.json
[cluster-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.parameters.json
