---
title: Создание кластера Service Fabric на платформе Linux в Azure | Документация Майкрософт
description: Из этого руководства вы узнаете, как развернуть кластер Service Fabric на платформе Linux в имеющейся виртуальной сети с помощью Azure CLI.
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
ms.openlocfilehash: 63d5ae371b4852be96f764b91ec44f1befbd469b
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2018
---
# <a name="tutorial-deploy-a-service-fabric-linux-cluster-into-an-azure-virtual-network"></a>Руководство. Развертывание кластера Service Fabric на платформе Linux в виртуальной сети Azure
Это руководство представляет первую часть цикла. Вы узнаете, как развернуть кластер Service Fabric Linux в [виртуальной сети Azure](../virtual-network/virtual-networks-overview.md) и [группе безопасности сети (NSG)](../virtual-network/virtual-networks-nsg.md) с помощью шаблона и интерфейса командной строки Azure. После окончания этого учебника у вас будет кластер в облаке, в который можно разворачивать приложения. Создание кластера Windows с помощью PowerShell описывается в разделе [Развертывание безопасного кластера Service Fabric на платформе Windows в виртуальной сети Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md).

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

## <a name="prerequisites"></a>предварительным требованиям
Перед началом работы с этим руководством выполните следующие действия:
- Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Установите [интерфейс командной строки Service Fabric](service-fabric-cli.md).
- Установите [Azure CLI 2.0](/cli/azure/install-azure-cli).

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

С помощью этого руководства мы развернем кластер с пятью узлами одного типа. Однако при развертывании любого рабочего кластера важно правильно [спланировать его загрузку](service-fabric-cluster-capacity.md). Вот несколько моментов, которые необходимо учесть:

- Количество узлов и их типов для создания кластера. 
- Свойства каждого типа узлов (например, размер, основной тип, возможность подключения из Интернета, количество виртуальных машин и др.).
- Надежность и устойчивость характеристик кластера.

## <a name="download-and-explore-the-template"></a>Скачивание и изучение шаблона
Скачайте следующие файлы шаблона Resource Manager:
- [vnet-linuxcluster.json][template];
- [vnet-linuxcluster.parameters.json][parameters].

Файл [vnet-linuxcluster.json][template] развертывает ряд ресурсов, включая следующие.

### <a name="service-fabric-cluster"></a>Кластер Service Fabric
Кластер Linux развернут со следующими характеристиками:
- один тип узла; 
- пять узлов на первичном типе узла (можно настроить в параметрах шаблона);
- ОС: Ubuntu 16.04 LTS (можно настроить в параметрах шаблона);
- защищенный сертификат (можно настроить в параметрах шаблона);
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
- порт приложения: 443.

### <a name="virtual-network-subnet-and-network-security-group"></a>Виртуальная сеть, подсеть и группа безопасности сети
Названия виртуальной сети, подсети и группы безопасности сети указаны в параметрах шаблона.  В параметрах шаблона также объявляются адресные пространства виртуальной сети и подсети:
- адресное пространство виртуальной сети: 10.0.0.0/16;
- адресное пространство подсети Service Fabric: 10.0.2.0/24.

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
|certificateThumbprint|| <p>Если создается самозаверяющий сертификат или указывается файл сертификата, значение должно быть пустым.</p><p>Если необходимо использовать имеющийся сертификат, который вы ранее передали в хранилище ключей, заполните значение отпечатка сертификата. Например, 6190390162C988701DB5676EB81083EA608DCCF3. </p>| 
|certificateUrlValue|| <p>Если создается самозаверяющий сертификат или указывается файл сертификата, значение должно быть пустым.</p><p>Если необходимо использовать имеющийся сертификат, который вы ранее передали в хранилище ключей, укажите URL-адрес сертификата. Например, https://mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346.</p>|
|sourceVaultValue||<p>Если создается самозаверяющий сертификат или указывается файл сертификата, значение должно быть пустым.</p><p>Если необходимо использовать имеющийся сертификат, который вы ранее передали в хранилище ключей, укажите сведения об исходном хранилище. Например, /subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT.</p>|


<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>Развертывание кластера и виртуальной сети
Настройте топологию сети и разверните кластер Service Fabric. Шаблон Resource Manager [vnet-linuxcluster.json][template] создает виртуальную сеть, а также подсеть и группу безопасности сети (NSG) для Service Fabric. Шаблон также развертывает кластер с включенным сертификатом безопасности.  Для рабочих кластеров в качестве сертификата нужно использовать сертификат из центра сертификации (ЦС). Самозаверяющий сертификат можно использовать для защиты тестовых кластеров.

В приведенном ниже сценарии используется команда [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest#az_sf_cluster_create) и шаблон, которые позволяют развернуть новый кластер с помощью имеющегося сертификата. Эта команда также создает хранилище ключей в Azure и отправляет сертификат.

```azurecli
ResourceGroupName="sflinuxclustergroup"
Location="southcentralus"  
Password="q6D7nN%6ck@6"
VaultName="linuxclusterkeyvault"
VaultGroupName="linuxclusterkeyvaultgroup"
CertPath="C:\MyCertificates\MyCertificate.pem"

# sign in to your Azure account and select your subscription
az login
az account set --subscription <guid>

# Create a new resource group for your deployment and give it a name and a location.
az group create --name $ResourceGroupName --location $Location

# Create the Service Fabric cluster.
az sf cluster create --resource-group $ResourceGroupName --location $Location \
   --certificate-password $Password --certificate-file $CertPath \
   --vault-name $VaultName --vault-resource-group $ResourceGroupName  \
   --template-file vnet-linuxcluster.json --parameter-file vnet-linuxcluster.parameters.json
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

## <a name="next-steps"></a>Дополнительная информация
Из этого руководства вы узнали, как выполнить следующие задачи:

> [!div class="checklist"]
> * создание виртуальной сети в Azure с помощью Azure CLI;
> * создание защищенного кластера Service Fabric в Azure с помощью Azure CLI;
> * Защита кластера с помощью сертификата X.509
> * подключение к кластеру с помощью интерфейса командной строки Service Fabric;
> * Удаление кластера

Теперь перейдите к следующему руководству, чтобы узнать, как масштабировать кластер.
> [!div class="nextstepaction"]
> [Scale a Service Fabric cluster](service-fabric-tutorial-scale-cluster.md) (Масштабирование кластера Service Fabric)


[template]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/cluster-tutorial/vnet-linuxcluster.json
[parameters]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/cluster-tutorial/vnet-linuxcluster.parameters.json
