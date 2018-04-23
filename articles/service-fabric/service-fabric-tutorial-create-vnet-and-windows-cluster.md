---
title: Создание кластера Service Fabric на платформе Windows в Azure | Документация Майкрософт
description: Из этого руководства вы узнаете, как развернуть кластер Service Fabric на платформе Windows в виртуальной сети и группе безопасности сети Azure с помощью PowerShell.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/22/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: b85c3030984758a0c425fb526a32fcd8eff974c9
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
---
# <a name="tutorial-deploy-a-service-fabric-windows-cluster-into-an-azure-virtual-network"></a>Руководство. Развертывание кластера Service Fabric на платформе Windows в виртуальной сети Azure
Это руководство представляет первую часть цикла. Вы узнаете, как развернуть кластер Service Fabric на платформе Windows в [виртуальной сети Azure](../virtual-network/virtual-networks-overview.md) и [группе безопасности сети](../virtual-network/virtual-networks-nsg.md) с помощью шаблона и PowerShell. После окончания этого учебника у вас будет кластер в облаке, в который можно разворачивать приложения.  Создание кластера Linux с помощью Azure CLI описывается в разделе [Развертывание безопасного кластера Service Fabric на платформе Linux в виртуальной сети Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md).

В этом руководстве описывается рабочий сценарий.  Если вы хотите быстро создать небольшой кластер для тестирования, см. статью [Create a three-node test Service Fabric cluster](./scripts/service-fabric-powershell-create-test-cluster.md) (Создание тестового кластера Service Fabric с тремя узлами).

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
> * [свертывание и развертывание кластера](service-fabric-tutorial-scale-cluster.md);
> * [обновление среды выполнения кластера;](service-fabric-tutorial-upgrade-cluster.md)
> * [развертывание службы управления API с помощью Service Fabric](service-fabric-tutorial-deploy-api-management.md).

## <a name="prerequisites"></a>предварительным требованиям
Перед началом работы с этим руководством выполните следующие действия:
- Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Установите [пакет SDK для Service Fabric и модуль PowerShell](service-fabric-get-started.md)
- Установите модуль [Azure PowerShell версии 4.1 или более поздней](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)

Ниже приведены процедуры для создания кластера Service Fabric с пятью узлами. Чтобы рассчитать затраты, связанные с запуском кластера Service Fabric в Azure, используйте [калькулятор цен Azure](https://azure.microsoft.com/pricing/calculator/).

## <a name="key-concepts"></a>Основные понятия
[Кластер Service Fabric](service-fabric-deploy-anywhere.md) — это подключенный к сети набор виртуальных машин или физических компьютеров, в котором вы развертываете микрослужбы и управляете ими. Кластеры можно масштабировать до нескольких тысяч машин. Узлом называется компьютер или виртуальная машина, которая входит в состав кластера. Каждому узлу назначается имя (строка). Узлы имеют свои характеристики, в частности свойства размещения.

Тип узла определяет размер, количество и свойства набора виртуальных машин в кластере. Каждый определенный тип узлов настраивается как [масштабируемый набор виртуальных машин](/azure/virtual-machine-scale-sets/), т. е. как вычислительный ресурс Azure, который можно использовать для развертывания коллекции виртуальных машин и управления ею как набором. Каждый тип узла поддерживает возможность независимого масштабирования, имеет разные наборы открытых портов и собственные метрики емкости. Типы узлов используются для определения ролей набора узлов кластера, например "интерфейсная часть" или "серверная часть".  В кластере могут быть узлы нескольких типов. Тип основных узлов должен включать не менее пяти виртуальных машин для производственных кластеров и не менее трех виртуальных машин для тестовых кластеров.  [Системные службы Service Fabric](service-fabric-technical-overview.md#system-services) размещаются на узлах основного типа.

Кластер защищен сертификатом кластера. Сертификат кластера — это сертификат X.509, используемый для защиты обмена данными между узлами и для аутентификации конечных точек управления кластера в клиенте управления.  Этот сертификат кластера также предоставляет SSL для API управления HTTPS и Service Fabric Explorer по протоколу HTTPS. Самозаверяющие сертификаты используются для тестовых кластеров.  Для рабочих кластеров в качестве сертификата нужно использовать сертификат из центра сертификации (ЦС).

Сертификат кластера должен:

- Содержать закрытый ключ.
- Быть создан для обмена ключами, которые можно экспортировать в файл обмена личной информацией (PFX-файл).
- Иметь имя субъекта, совпадающее с доменным именем, которое используется для обращения к кластеру Service Fabric. Это необходимо, чтобы предоставить SSL-протокол конечным точкам управления HTTPS в кластере и Service Fabric Explorer. Не удается получить SSL-сертификат от центра сертификации (ЦС) в домене .cloudapp.azure.com. Необходимо получить имя личного домена для кластера. При запросе сертификата из ЦС имя субъекта сертификата должно совпадать с именем личного домена, используемого для кластера.

Хранилище ключей Azure используется для управления сертификатами кластеров Service Fabric в Azure.  При развертывании кластера в Azure поставщик ресурсов Azure, ответственный за создание кластеров Service Fabric, извлекает сертификаты из хранилища ключей и устанавливает их на виртуальные машины кластера.

В этом руководстве рассматривается кластер с пятью узлами одного типа. Однако при развертывании любого рабочего кластера важно правильно [спланировать его загрузку](service-fabric-cluster-capacity.md). Вот несколько моментов, которые необходимо учесть:

- Количество узлов и их типов для создания кластера. 
- Свойства каждого типа узлов (например, размер, основной тип, возможность подключения из Интернета, количество виртуальных машин и др.).
- Надежность и устойчивость характеристик кластера.

## <a name="download-and-explore-the-template"></a>Скачивание и изучение шаблона
Скачайте следующие файлы шаблона Resource Manager:
- [vnet-cluster.json][template];
- [vnet-cluster.parameters.json][parameters].

Файл [vnet-cluster.json][template] развертывает ряд ресурсов, включая следующие. 

### <a name="service-fabric-cluster"></a>Кластер Service Fabric
Кластер Windows развертывается со следующими характеристиками:
- один тип узла; 
- пять узлов на первичном типе узла (можно настроить в параметрах шаблона);
- ОС: Windows Server 2016 Datacenter с контейнерами (можно настроить в параметрах шаблона);
- защищенный сертификат (можно настроить в параметрах шаблона);
- включенный [обратный прокси-сервер](service-fabric-reverseproxy.md);
- [служба DNS](service-fabric-dnsservice.md) включена;
- [уровень устойчивости](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) Bronze (можно настроить в параметрах шаблона);
- [уровень надежности](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) Silver (можно настроить в параметрах шаблона);
- конечная точка подключения клиента: 19000 (можно настроить в параметрах шаблона);
- конечная точка HTTP-шлюза: 19080 (можно настроить в параметрах шаблона).

### <a name="azure-load-balancer"></a>Azure Load Balancer
Подсистема балансировки нагрузки развернута, а проверки и правила настроены для следующих портов:
- конечная точка подключения клиента: 19000;
- конечная точка HTTP шлюза: 19080; 
- порт приложения: 80;
- порт приложения: 443;
- обратный прокси-сервер Service Fabric: 19081.

Если нужны другие порты приложений, нужно настроить ресурсы Microsoft.Network/loadBalancers и Microsoft.Network/networkSecurityGroups, чтобы разрешить входящий трафик.

### <a name="virtual-network-subnet-and-network-security-group"></a>Виртуальная сеть, подсеть и группа безопасности сети
Названия виртуальной сети, подсети и группы безопасности сети указаны в параметрах шаблона.  В параметрах шаблона также объявляются адресные пространства виртуальной сети и подсети:
- Адресное пространство виртуальной сети: 172.16.0.0/20.
- Адресное пространство подсети Service Fabric: 172.16.2.0/23.

Следующие правила входящего трафика включены в группе безопасности сети. Значения порта можно изменить, изменив переменные шаблона.
- ClientConnectionEndpoint (протокол TCP): 19000.
- HttpGatewayEndpoint (HTTP или TCP): 19080.
- SMB : 445.
- Межузловая передача сообщений — 1025, 1026, 1027.
- Диапазон временных портов — от 49152 до 65 534 (требуется минимум 256 портов).
- Порты для использования в приложениях: 80 и 443.
- Диапазон портов приложений — от 49152 до 65 534 (используются для взаимодействия служб и не открыты в подсистеме балансировки нагрузки).
- Блокировка остальных портов.

Если нужны другие порты приложений, нужно настроить ресурсы Microsoft.Network/loadBalancers и Microsoft.Network/networkSecurityGroups, чтобы разрешить входящий трафик.

## <a name="set-template-parameters"></a>Установка параметров шаблона
В файле параметров [vnet-cluster.parameters.json][parameters] содержатся многие значения, используемые для развертывания кластера и связанных ресурсов. Далее представлены некоторые параметры, которые может понадобиться изменить для развертывания:

|Параметр|Пример значения|Заметки|
|---|---||
|adminUserName|vmadmin| Имя администратора кластера виртуальных машин. |
|adminPassword|Password#1234| Пароль администратора для кластера виртуальных машин.|
|clusterName|mysfcluster123| Имя кластера. |
|location|southcentralus| Расположение кластера. |
|certificateThumbprint|| <p>Если создается самозаверяющий сертификат или указывается файл сертификата, значение должно быть пустым.</p><p>Если необходимо использовать имеющийся сертификат, который вы ранее передали в хранилище ключей, заполните значение отпечатка сертификата. Например, 6190390162C988701DB5676EB81083EA608DCCF3.</p>. | 
|certificateUrlValue|| <p>Если создается самозаверяющий сертификат или указывается файл сертификата, значение должно быть пустым. </p><p>Если необходимо использовать имеющийся сертификат, который вы ранее передали в хранилище ключей, укажите URL-адрес сертификата. Например, https://mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346.</p>|
|sourceVaultValue||<p>Если создается самозаверяющий сертификат или указывается файл сертификата, значение должно быть пустым.</p><p>Если необходимо использовать имеющийся сертификат, который вы ранее передали в хранилище ключей, укажите сведения об исходном хранилище. Например, /subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT.</p>|


<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>Развертывание кластера и виртуальной сети
Настройте топологию сети и разверните кластер Service Fabric. Шаблон Resource Manager [vnet-cluster.json][template] создает виртуальную сеть, а также подсеть и группу безопасности сети (NSG) для Service Fabric. Шаблон также развертывает кластер с включенным сертификатом безопасности.  Для рабочих кластеров в качестве сертификата нужно использовать сертификат из центра сертификации (ЦС). Самозаверяющий сертификат можно использовать для защиты тестовых кластеров.

### <a name="create-a-cluster-using-an-existing-certificate"></a>Создание кластера с помощью имеющегося сертификата
В приведенном ниже сценарии используется командлет [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) и шаблон, которые позволяют развернуть в Azure новый кластер. Этот командлет также создает хранилище ключей в Azure и отправляет сертификат. 

```powershell
# Variables.
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"  # must match the location parameter in the template
$templatepath="C:\temp\cluster"

$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$clustername = "mysfcluster123"  # must match the clusterName parameter in the template
$vaultname = "clusterkeyvault123"
$vaultgroupname="clusterkeyvaultgroup123"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# sign in to your Azure account and select your subscription
Connect-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>

# Create a new resource group for your deployment and give it a name and a location.
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzureRmServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\vnet-cluster.json" `
-ParameterFile "$templatepath\vnet-cluster.parameters.json" -CertificatePassword $certpwd `
-KeyVaultName $vaultname -KeyVaultResouceGroupName $vaultgroupname -CertificateFile $certpath
```

### <a name="create-a-cluster-using-a-new-self-signed-certificate"></a>Создание кластера с помощью нового самозаверяющего сертификата
В приведенном ниже сценарии используется командлет [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) и шаблон, которые позволяют развернуть в Azure новый кластер. Кроме того, при помощи командлета создается хранилище ключей в Azure, добавляется самозаверяющий сертификат в хранилище ключей и локально скачивается файл сертификата. 

```powershell
# Variables.
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"  # must match the location parameter in the template
$templatepath="C:\temp\cluster"

$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$certfolder="c:\mycertificates\"
$clustername = "mysfcluster123"
$vaultname = "clusterkeyvault123"
$vaultgroupname="clusterkeyvaultgroup123"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# sign in to your Azure account and select your subscription
Connect-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>

# Create a new resource group for your deployment and give it a name and a location.
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzureRmServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\vnet-cluster.json" `
-ParameterFile "$templatepath\vnet-cluster.parameters.json" -CertificatePassword $certpwd `
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
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
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

## <a name="next-steps"></a>Дополнительная информация
Из этого руководства вы узнали, как выполнить следующие задачи:

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


[template]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/cluster-tutorial/vnet-cluster.json
[parameters]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/cluster-tutorial/vnet-cluster.parameters.json
