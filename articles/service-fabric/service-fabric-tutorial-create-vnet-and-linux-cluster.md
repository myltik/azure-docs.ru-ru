---
title: "Создание кластера Service Fabric на платформе Linux в Azure | Документация Майкрософт"
description: "Узнайте, как развернуть кластер Service Fabric на платформе Linux в существующей виртуальной сети с помощью Azure CLI."
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
ms.date: 09/26/2017
ms.author: ryanwi
ms.openlocfilehash: bf4b0f67a4c3667fb0c0cb826a822d6090c36375
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2017
---
# <a name="deploy-a-service-fabric-linux-cluster-into-an-azure-virtual-network"></a>Развертывание кластера Service Fabric на платформе Linux в виртуальной сети Azure
Это руководство представляет первую часть цикла. Вы узнаете, как развернуть кластер Service Fabric на платформе Linux в подсети существующей виртуальной сети с помощью Azure CLI. После окончания этого учебника у вас будет кластер в облаке, в который можно разворачивать приложения. Создание кластера Windows с помощью PowerShell описывается в разделе [Развертывание безопасного кластера Service Fabric на платформе Windows в виртуальной сети Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md).

Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * создание виртуальной сети в Azure с помощью Azure CLI;
> * создание защищенного кластера Service Fabric в Azure с помощью Azure CLI;
> * Защита кластера с помощью сертификата X.509
> * подключение к кластеру с помощью интерфейса командной строки Service Fabric;
> * Удаление кластера

Из этого цикла руководств вы узнаете, как выполнять такие задачи:
> [!div class="checklist"]
> * создание защищенного кластера в Azure;
> * [свертывание и развертывание кластера](service-fabric-tutorial-scale-cluster.md);
> * [обновление среды выполнения кластера;](service-fabric-tutorial-upgrade-cluster.md)
> * [развертывание службы управления API с помощью Service Fabric](service-fabric-tutorial-deploy-api-management.md).

## <a name="prerequisites"></a>Предварительные требования
Перед началом работы с этим руководством выполните следующие действия:
- Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Установите [интерфейс командной строки Service Fabric](service-fabric-cli.md).
- Установите [Azure CLI 2.0](/cli/azure/install-azure-cli).

Ниже приведены процедуры для создания кластера Service Fabric с пятью узлами. Чтобы рассчитать затраты, связанные с запуском кластера Service Fabric в Azure, используйте [калькулятор цен Azure](https://azure.microsoft.com/pricing/calculator/).

## <a name="introduction"></a>Введение
В этом руководстве мы развернем кластер из пяти узлов одного типа в виртуальную сеть в Azure.

[Кластер Service Fabric](service-fabric-deploy-anywhere.md) — это подключенный к сети набор виртуальных машин или физических компьютеров, в котором вы развертываете микрослужбы и управляете ими. Кластеры можно масштабировать до нескольких тысяч машин. Узлом называется компьютер или виртуальная машина, которая входит в состав кластера. Каждому узлу назначается имя (строка). Узлы имеют свои характеристики, в частности свойства размещения.

Тип узла определяет размер, количество и свойства набора виртуальных машин в кластере. Каждый определенный тип узлов настраивается как [масштабируемый набор виртуальных машин](/azure/virtual-machine-scale-sets/), т. е. как вычислительный ресурс Azure, который можно использовать для развертывания коллекции виртуальных машин и управления ею как набором. Каждый тип узла поддерживает возможность независимого масштабирования, имеет разные наборы открытых портов и собственные метрики емкости. Типы узлов используются для определения ролей набора узлов кластера, например "интерфейсная часть" или "серверная часть".  В кластере могут быть узлы нескольких типов. Тип основных узлов должен включать не менее пяти виртуальных машин для производственных кластеров и не менее трех виртуальных машин для тестовых кластеров.  [Системные службы Service Fabric](service-fabric-technical-overview.md#system-services) размещаются на узлах основного типа.

## <a name="cluster-capacity-planning"></a>Планирование загрузки кластера
В этом руководстве мы развернем кластер из пяти узлов одного типа.  При развертывании любого производственного кластера важно правильно спланировать его загрузку. Вот несколько моментов, которые необходимо учесть:

- Количество типов узлов для создания кластера. 
- Свойства каждого типа узлов (например, размер, основной тип, возможность подключения из Интернета, количество виртуальных машин и др.).
- Надежность и устойчивость характеристик кластера.

Дополнительные сведения см. в статье [Рекомендации по планированию загрузки кластера Service Fabric](service-fabric-cluster-capacity.md).

## <a name="sign-in-to-azure-and-select-your-subscription"></a>Вход в Azure и выбор подписки
В этом руководстве используется Azure CLI. При запуске нового сеанса войдите в свою учетную запись Azure и выберите подписку перед выполнением команд Azure.
 
Выполните приведенный ниже сценарий, чтобы войти в учетную запись Azure и выбрать подписку.

```azurecli
az login
az account set --subscription <guid>
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов
Создайте группу ресурсов для развертывания и задайте для нее имя и расположение.

```azurecli
ResourceGroupName="sflinuxclustergroup"
Location="southcentralus"
az group create --name $ResourceGroupName --location $Location
```

## <a name="deploy-the-network-topology"></a>Развертывание топологии сети
Настройте топологию сети, в которой будут развернуты служба управления API и кластер Service Fabric. Шаблон Resource Manager [Network.json][network-arm] настроен для создания виртуальной сети, а также подсети и группы безопасности сети (NSG) для Service Fabric и подсети и группы безопасности сети для службы управления API. Дополнительные сведения о виртуальных сетях, подсетях и NSG можно получить [здесь](../virtual-network/virtual-networks-overview.md).

Файл параметров [network.parameters.json][network-parameters-arm] содержит имена подсетей и групп безопасности сети, в которых будут развернуты Service Fabric и служба управления API.  Развертывание службы управления API описывается в [этом руководстве](service-fabric-tutorial-deploy-api-management.md). В рамках этого руководства изменять значения параметров не нужно. Эти значения используются в шаблонах Resource Manager для Service Fabric.  Если изменить эти значения здесь, потребуется изменить их и в других шаблонах Resource Manager, используемых в этом руководстве и [руководстве по развертыванию службы управления API](service-fabric-tutorial-deploy-api-management.md). 

Скачайте шаблон Resource Manager и файл параметров:
- [network.json][network-arm]
- [network.parameters.json][network-parameters-arm]

Используйте приведенный ниже сценарий, чтобы развернуть шаблон Resource Manager и файл параметров для настройки сети.

```azurecli
az group deployment create \
    --name VnetDeployment \
    --resource-group $ResourceGroupName \
    --template-file network.json \
    --parameters @network.parameters.json
```
<a id="createvaultandcert" name="createvaultandcert_anchor"></a>
## <a name="deploy-the-service-fabric-cluster"></a>Развертывание кластера Service Fabric
Когда сетевые ресурсы развернуты, необходимо развернуть кластер Service Fabric в виртуальной сети в подсети и группе безопасности сети, используемых для кластера Service Fabric. Для развертывания кластера в существующих виртуальной сети и подсети (развернутых ранее в этой статье) требуется шаблон Resource Manager.  Дополнительные сведения см. в разделе [Создание кластера Service Fabric в Azure с помощью Azure Resource Manager](service-fabric-cluster-creation-via-arm.md). В рамках этого цикла руководств шаблон предварительно настроен для использования имен виртуальной сети, подсети и группы безопасности сети, настроенных на предыдущем шаге.  

Скачайте шаблон Resource Manager и файл параметров:
- [linuxcluster.json][cluster-arm]
- [linuxcluster.parameters.json][cluster-parameters-arm]

Этот шаблон используется для создания безопасного кластера.  Сертификат кластера — это сертификат X.509, используемый для защиты обмена данными между узлами и для аутентификации конечных точек управления кластера в клиенте управления.  Этот сертификат кластера также предоставляет SSL для API управления HTTPS и Service Fabric Explorer по протоколу HTTPS. Хранилище ключей Azure используется для управления сертификатами кластеров Service Fabric в Azure.  При развертывании кластера в Azure поставщик ресурсов Azure, ответственный за создание кластеров Service Fabric, извлекает сертификаты из хранилища ключей и устанавливает их на виртуальные машины кластера. 

Вы можете использовать сертификат из центра сертификации в качестве сертификата кластера или создать самозаверяющий сертификат для тестирования. Сертификат кластера должен:

- Содержать закрытый ключ.
- Быть создан для обмена ключами, которые можно экспортировать в файл обмена личной информацией (PFX-файл).
- Иметь имя субъекта, совпадающее с доменным именем, которое используется для обращения к кластеру Service Fabric. Это необходимо, чтобы предоставить SSL-протокол конечным точкам управления HTTPS в кластере и Service Fabric Explorer. Не удается получить SSL-сертификат от центра сертификации (ЦС) в домене .cloudapp.azure.com. Необходимо получить имя личного домена для кластера. При запросе сертификата из ЦС имя субъекта сертификата должно совпадать с именем личного домена, используемого для кластера.

Заполните эти пустые параметры в файле *linuxcluster.parameters.json* для развертывания:

|Параметр|Значение|
|---|---|
|adminPassword|Password#1234|
|adminUserName|vmadmin|
|clusterName|mysfcluster|

Оставьте значения параметров **certificateThumbprint**, **certificateUrlValue** и **sourceVaultValue** пустыми, чтобы создать самозаверяющий сертификат.  Если необходимо использовать имеющийся сертификат, который вы ранее передали в хранилище ключей, заполните эти значения параметров.

В приведенном ниже сценарии используется команда [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest#az_sf_cluster_create) и шаблон, которые позволяют развернуть в Azure новый кластер. Кроме того, при помощи командлета создается хранилище ключей в Azure, добавляется самозаверяющий сертификат в хранилище ключей и локально скачивается файл сертификата. Вы можете указать имеющийся сертификат или хранилище ключей, воспользовавшись другими параметрами команды [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest#az_sf_cluster_create).

```azurecli
Password="q6D7nN%6ck@6"
Subject="mysfcluster.southcentralus.cloudapp.azure.com"
VaultName="linuxclusterkeyvault"
az group create --name $ResourceGroupName --location $Location

az sf cluster create --resource-group $ResourceGroupName --location $Location \
   --certificate-output-folder . --certificate-password $Password --certificate-subject-name $Subject \
   --vault-name $VaultName --vault-resource-group $ResourceGroupName  \
   --template-file linuxcluster.json --parameter-file linuxcluster.parameters.json

```

## <a name="connect-to-the-secure-cluster"></a>Подключение к безопасному кластеру
Подключитесь к кластеру, выполнив команду `sfctl cluster select` в интерфейсе командной строки Service Fabric и указав свой ключ.  Примечание. Используйте параметр **--no-verify** только для самозаверяющего сертификата.

```azurecli
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

Выполните команду `sfctl cluster health`, чтобы проверить подключение к кластеру и убедиться, что кластер работает.

```azurecli
sfctl cluster health
```

## <a name="clean-up-resources"></a>Очистка ресурсов
Кластер, который вы только что создали, используется в других статьях этого цикла руководств. Если вы не собираетесь немедленно приступить к следующей статье, то можете удалить кластер, чтобы за него не взималась плата. Чтобы удалить кластер и все ресурсы, который он использует, проще всего удалить группу ресурсов.

Войдите в Azure и выберите идентификатор подписки, в которой вы хотите удалить кластер.  Идентификатор подписки можно узнать, войдя на [портал Azure](http://portal.azure.com). Удалите группу ресурсов и все ресурсы кластера с помощью команды [az group delete](/cli/azure/group?view=azure-cli-latest#az_group_delete).

```azurecli
az group delete --name $ResourceGroupName
```

## <a name="next-steps"></a>Дальнейшие действия
Из этого руководства вы узнали, как выполнять такие задачи:

> [!div class="checklist"]
> * создание виртуальной сети в Azure с помощью Azure CLI;
> * создание защищенного кластера Service Fabric в Azure с помощью Azure CLI;
> * Защита кластера с помощью сертификата X.509
> * подключение к кластеру с помощью интерфейса командной строки Service Fabric;
> * Удаление кластера

Теперь перейдите к следующему руководству, чтобы узнать, как масштабировать кластер.
> [!div class="nextstepaction"]
> [Scale a Service Fabric cluster](service-fabric-tutorial-scale-cluster.md) (Масштабирование кластера Service Fabric)


[network-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.json
[network-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.parameters.json

[cluster-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/linuxcluster.json
[cluster-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/linuxcluster.parameters.json
