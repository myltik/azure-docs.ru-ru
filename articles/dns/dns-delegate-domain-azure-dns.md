---
title: "Делегирование домена в Azure DNS | Документация Майкрософт"
description: "Узнайте, как изменить делегирование домена и использовать серверы имен Azure DNS для размещения домена."
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
ms.assetid: 257da6ec-d6e2-4b6f-ad76-ee2dde4efbcc
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: kumud
ms.openlocfilehash: a13d9b360e2c43aa2a3d4f62215e4570ce42cd5b
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/24/2018
---
# <a name="delegate-a-domain-to-azure-dns"></a>Делегирование домена в Azure DNS

С помощью Azure DNS вы можете разместить зону DNS и управлять записями DNS для домена в Azure. Чтобы запросы DNS для домена достигали Azure DNS, домен должен быть делегирован в Azure DNS из родительского домена. Помните, что Azure DNS — это не регистратор домена. В этой статье описывается делегирование домена в Azure DNS.

Для доменов, приобретенных у регистратора, эти записи может настроить сам регистратор. Для создания зоны DNS с доменным именем в Azure DNS необязательно быть его владельцем. Однако вам необходимо быть владельцем домена, чтобы настроить делегирование в Azure DNS у регистратора доменных имен.

Предположим, например, что вы приобрели домен contoso.net и создали зону с именем contoso.net в Azure DNS. Регистратор предоставляет вам как владельцу домена возможность настройки адресов серверов имен (то есть записи NS) для домена. Регистратор будет хранить эти записи NS в родительском домене .net. Клиенты по всему миру будут направляться в ваш домен в зоне Azure DNS при попытке разрешить записи DNS в contoso.net.

## <a name="create-a-dns-zone"></a>Создание зоны DNS

1. Войдите на портал Azure.
1. В меню **Концентратор** выберите **Создать** > **Сеть** > **Зона DNS**, чтобы открыть страницу **Создание зоны DNS**.

   ![Зона DNS](./media/dns-domain-delegation/dns.png)

1. На странице **Создание зоны DNS** введите следующие значения, а затем выберите **Создать**.

   | **Параметр** | **Значение** | **Дополнительные сведения** |
   |---|---|---|
   |**Имя**|contoso.net|Укажите имя зоны DNS.|
   |**Подписка**|[Ваша подписка]|Выберите подписку для создания шлюза приложений.|
   |**Группа ресурсов**|**Создать:** contosoRG|Создайте группу ресурсов. Имя группы ресурсов должно быть уникальным в пределах выбранной подписки. Дополнительные сведения о группах ресурсов см. в [этом разделе](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups) статьи "Общие сведения о диспетчере ресурсов Azure".|
   |**Местоположение.**|Запад США||

> [!NOTE]
> Расположение группы ресурсов никак не влияет на зону DNS. Расположение зоны DNS всегда является глобальным и не отображается.

## <a name="retrieve-name-servers"></a>Получение серверов имен

Прежде чем делегировать зоны DNS в службу DNS Azure, необходимо знать имена серверов зоны. Когда создается зона, служба DNS Azure выделяет серверы имен из пула.

1. Создав зону DNS, на портале Azure на панели **Избранное** выберите **Все ресурсы**. На странице **Все ресурсы** выберите зону DNS **contoso.net**. Если выбранная подписка имеет несколько ресурсов, введите **contoso.net** в поле **Фильтровать по имени**, чтобы получить доступ к шлюзу приложений. 

1. Получите серверы имен на странице "Зона DNS". В этом примере зоне contoso.net назначены серверы имен ns1-01.azure-dns.com, ns2-01.azure-dns.net, ns3-01.azure-dns.org и ns4-01.azure-dns.info.

   ![Список серверов имен](./media/dns-domain-delegation/viewzonens500.png)

Служба DNS Azure автоматически создает в вашей зоне заслуживающие доверия NS-записи, в которых указаны выделенные серверы имен. Чтобы увидеть серверы имен через Azure PowerShell или интерфейс командной строки Azure, получите эти записи.

Ниже приведены примеры получения серверов имен для зоны в Azure DNS с помощью PowerShell и Azure CLI.

### <a name="powershell"></a>PowerShell

```powershell
# The record name "@" is used to refer to records at the top of the zone.
$zone = Get-AzureRmDnsZone -Name contoso.net -ResourceGroupName contosoRG
Get-AzureRmDnsRecordSet -Name "@" -RecordType NS -Zone $zone
```

Ниже приведен пример ответа.

```
Name              : @
ZoneName          : contoso.net
ResourceGroupName : contosorg
Ttl               : 172800
Etag              : 03bff8f1-9c60-4a9b-ad9d-ac97366ee4d5
RecordType        : NS
Records           : {ns1-07.azure-dns.com., ns2-07.azure-dns.net., ns3-07.azure-dns.org.,
                    ns4-07.azure-dns.info.}
Metadata          :
```

### <a name="azure-cli"></a>Инфраструктура CLI Azure

```azurecli
az network dns record-set list --resource-group contosoRG --zone-name contoso.net --type NS --name @
```

Ниже приведен пример ответа.

```json
{
  "etag": "03bff8f1-9c60-4a9b-ad9d-ac97366ee4d5",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contosoRG/providers/Microsoft.Network/dnszones/contoso.net/NS/@",
  "metadata": null,
  "name": "@",
  "nsRecords": [
    {
      "nsdname": "ns1-07.azure-dns.com."
    },
    {
      "nsdname": "ns2-07.azure-dns.net."
    },
    {
      "nsdname": "ns3-07.azure-dns.org."
    },
    {
      "nsdname": "ns4-07.azure-dns.info."
    }
  ],
  "resourceGroup": "contosoRG",
  "ttl": 172800,
  "type": "Microsoft.Network/dnszones/NS"
}
```

## <a name="delegate-the-domain"></a>Делегирование домена

Теперь, когда зона DNS создана и у вас есть серверы имен, необходимо обновить родительский домен с серверами имен Azure DNS. У каждого регистратора есть собственные средства управления DNS для изменения записей серверов имен домена. На странице управления DNS регистратора замените записи NS на созданные службой Azure DNS.

При делегировании домена в Azure DNS вам необходимо использовать имена серверов доменных имен, предоставленные службой Azure DNS. Рекомендуем всегда использовать все четыре сервера имен независимо от имени домена. Для делегирования домена не требуется, чтобы в сервере имен и вашем домене содержался один и тот же домен верхнего уровня.

Не используйте *связующие записи* для указания IP-адресов сервера доменных имен Azure DNS, так как эти IP-адреса в будущем могут измениться. Делегирование с использованием серверов имен в собственной зоне (также известны как *серверы личных имен*) в настоящее время не поддерживается в Azure DNS.

## <a name="verify-that-name-resolution-is-working"></a>Проверка работы разрешения имен

По завершении делегирования можно проверить, работает ли разрешение имен. Это можно сделать с помощью такого средства, как nslookup, запросив запись типа SOA для своей зоны. (Запись типа SOA автоматически создается при создании зоны.)

Нет необходимости указывать серверы имен Azure DNS. Если делегирование настроено правильно, в ходе обычного процесса разрешения DNS серверы имен находятся автоматически.

```
nslookup -type=SOA contoso.com
```

Ниже приведен пример ответа из предыдущей команды.

```
Server: ns1-04.azure-dns.com
Address: 208.76.47.4

contoso.com
primary name server = ns1-04.azure-dns.com
responsible mail addr = msnhst.microsoft.com
serial = 1
refresh = 900 (15 mins)
retry = 300 (5 mins)
expire = 604800 (7 days)
default TTL = 300 (5 mins)
```

## <a name="delegate-subdomains-in-azure-dns"></a>Делегирование дочерних доменов в Azure DNS

Если нужно настроить отдельную дочернюю зону, можно делегировать дочерний домен в Azure DNS. Предположим, например, что вы настроили и делегировали contoso.net в Azure DNS. Теперь необходимо настроить отдельную дочернюю зону partners.contoso.net.

1. Создайте в Azure DNS дочернюю зону partners.contoso.net.
2. Поищите заслуживающие доверия записи сервера имен в дочерней зоне, чтобы получить список серверов имен, в которых в Azure DNS размещена дочерняя зона.
3. Выполните делегирование дочерней зоны. Для этого настройте записи сервера имен в родительской зоне, указывающей на дочернюю.

### <a name="create-a-dns-zone"></a>Создание зоны DNS

1. Войдите на портал Azure.
1. В меню **Концентратор** выберите **Создать** > **Сеть** > **Зона DNS**, чтобы открыть страницу **Создание зоны DNS**.

   ![Зона DNS](./media/dns-domain-delegation/dns.png)

1. На странице **Создание зоны DNS** введите следующие значения, а затем выберите **Создать**.

   | **Параметр** | **Значение** | **Дополнительные сведения** |
   |---|---|---|
   |**Имя**|partners.contoso.net|Укажите имя зоны DNS.|
   |**Подписка**|[Ваша подписка]|Выберите подписку для создания шлюза приложений.|
   |**Группа ресурсов**|**Использовать существующий:** contosoRG|Создайте группу ресурсов. Имя группы ресурсов должно быть уникальным в пределах выбранной подписки. Дополнительные сведения о группах ресурсов см. в разделе [Группы ресурсов](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups) статьи "Общие сведения об Azure Resource Manager".|
   |**Местоположение.**|Запад США||

> [!NOTE]
> Расположение группы ресурсов никак не влияет на зону DNS. Расположение зоны DNS всегда является глобальным и не отображается.

### <a name="retrieve-name-servers"></a>Получение серверов имен

1. Создав зону DNS, на портале Azure на панели **Избранное** выберите **Все ресурсы**. Выберите зону DNS **partners.contoso.net** на странице **Все ресурсы**. Если выбранная подписка имеет несколько ресурсов, введите **partners.contoso.net** в поле **Фильтровать по имени**, чтобы получить доступ к зоне DNS.

1. Получите серверы имен на странице "Зона DNS". В этом примере зоне contoso.net назначены серверы имен ns1-01.azure-dns.com, ns2-01.azure-dns.net, ns3-01.azure-dns.org и ns4-01.azure-dns.info.

   ![Список серверов имен](./media/dns-domain-delegation/viewzonens500.png)

Служба DNS Azure автоматически создает в вашей зоне заслуживающие доверия NS-записи, в которых указаны выделенные серверы имен.  Чтобы увидеть серверы имен через Azure PowerShell или интерфейс командной строки Azure, получите эти записи.

### <a name="create-a-name-server-record-in-the-parent-zone"></a>Создание записи сервера имен в родительской зоне

1. На портале Azure перейдите к зоне DNS **contoso.net**.
1. Выберите **+ Набор записей**.
1. На странице **Добавление набора записей** введите следующие значения и нажмите кнопку **ОК**.

   | **Параметр** | **Значение** | **Дополнительные сведения** |
   |---|---|---|
   |**Имя**|partners|Введите имя дочерней зоны DNS.|
   |**Тип**|NS|Используйте NS для записей серверов имен.|
   |**Срок жизни**|1|Введите срок жизни.|
   |**Единица срока жизни**|Часы|Укажите срок жизни в часах.|
   |**Сервер доменных имен**|{серверы доменных имен из зоны partners.contoso.net}|Введите четыре сервера имен из зоны partners.contoso.net. |

   ![Значения для записи сервера имен](./media/dns-domain-delegation/partnerzone.png)


### <a name="delegate-subdomains-in-azure-dns-by-using-other-tools"></a>Делегирование дочерних доменов в Azure с помощью других средств

Ниже приведены примеры делегирования дочерних доменов в Azure DNS с помощью PowerShell и Azure CLI.

#### <a name="powershell"></a>PowerShell

Этот процесс продемонстрирован в приведенном ниже примере PowerShell. Эти же действия можно выполнить с помощью портала Azure или кроссплатформенного интерфейса командной строки Azure.

```powershell
# Create the parent and child zones. These can be in the same resource group or different resource groups, because Azure DNS is a global service.
$parent = New-AzureRmDnsZone -Name contoso.net -ResourceGroupName contosoRG
$child = New-AzureRmDnsZone -Name partners.contoso.net -ResourceGroupName contosoRG

# Retrieve the authoritative NS records from the child zone as shown in the next example. This information contains the name servers assigned to the child zone.
$child_ns_recordset = Get-AzureRmDnsRecordSet -Zone $child -Name "@" -RecordType NS

# Create the corresponding NS record set in the parent zone to complete the delegation. The record set name in the parent zone matches the child zone name (in this case, "partners").
$parent_ns_recordset = New-AzureRmDnsRecordSet -Zone $parent -Name "partners" -RecordType NS -Ttl 3600
$parent_ns_recordset.Records = $child_ns_recordset.Records
Set-AzureRmDnsRecordSet -RecordSet $parent_ns_recordset
```

Используйте `nslookup`, чтобы убедиться в правильности настройки зоны, просмотрев запись типа SOA дочерней зоны.

```
nslookup -type=SOA partners.contoso.com
```

```
Server: ns1-08.azure-dns.com
Address: 208.76.47.8

partners.contoso.com
    primary name server = ns1-08.azure-dns.com
    responsible mail addr = msnhst.microsoft.com
    serial = 1
    refresh = 900 (15 mins)
    retry = 300 (5 mins)
    expire = 604800 (7 days)
    default TTL = 300 (5 mins)
```

#### <a name="azure-cli"></a>Инфраструктура CLI Azure

```azurecli
#!/bin/bash

# Create the parent and child zones. These can be in the same resource group or different resource groups, because Azure DNS is a global service.
az network dns zone create -g contosoRG -n contoso.net
az network dns zone create -g contosoRG -n partners.contoso.net
```

Получите серверы доменных имен для зоны `partners.contoso.net` из выходных данных.

```
{
  "etag": "00000003-0000-0000-418f-250de2b2d201",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contosorg/providers/Microsoft.Network/dnszones/partners.contoso.net",
  "location": "global",
  "maxNumberOfRecordSets": 5000,
  "name": "partners.contoso.net",
  "nameServers": [
    "ns1-09.azure-dns.com.",
    "ns2-09.azure-dns.net.",
    "ns3-09.azure-dns.org.",
    "ns4-09.azure-dns.info."
  ],
  "numberOfRecordSets": 2,
  "resourceGroup": "contosorg",
  "tags": {},
  "type": "Microsoft.Network/dnszones"
}
```

Создайте набор записей и записи NS для каждого сервера доменных имен.

```azurecli
#!/bin/bash

# Create the record set
az network dns record-set ns create --resource-group contosorg --zone-name contoso.net --name partners

# Create an NS record for each name server.
az network dns record-set ns add-record --resource-group contosorg --zone-name contoso.net --record-set-name partners --nsdname ns1-09.azure-dns.com.
az network dns record-set ns add-record --resource-group contosorg --zone-name contoso.net --record-set-name partners --nsdname ns2-09.azure-dns.net.
az network dns record-set ns add-record --resource-group contosorg --zone-name contoso.net --record-set-name partners --nsdname ns3-09.azure-dns.org.
az network dns record-set ns add-record --resource-group contosorg --zone-name contoso.net --record-set-name partners --nsdname ns4-09.azure-dns.info.
```

## <a name="delete-all-resources"></a>Удаление всех ресурсов

Чтобы удалить все ресурсы, созданные в этой статье, выполните следующие действия:

1. На портале Azure в области **Избранное** выберите **Все ресурсы**. На странице **Все ресурсы** выберите группу ресурсов **contosorg**. Если выбранная подписка имеет несколько ресурсов, введите **contosorg** в поле **Фильтровать по имени**, чтобы получить доступ к группе ресурсов.
1. На странице **contosorg** нажмите кнопку **Удалить**.
1. На портале появится запрос на ввод имени группы ресурсов для подтверждения удаления. Введите **contosorg** для имени группы ресурсов, а затем нажмите кнопку **Удалить**. 

При удалении группы ресурсов будут удалены все ресурсы, содержащиеся в ней. Всегда проверяйте содержимое группы ресурсов, прежде чем удалять ее. Сначала портал удаляет все ресурсы в группе ресурсов, а затем удаляет и саму группу. Этот процесс занимает несколько минут.

## <a name="next-steps"></a>Дополнительная информация

[Управление зонами DNS](dns-operations-dnszones.md)

[Управление зонами DNS](dns-operations-recordsets.md)
