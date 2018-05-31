---
title: Служба метаданных экземпляров Azure | Документация Майкрософт
description: Использование интерфейса RESTful для получения сведений о вычислительных ресурсах виртуальной машины Windows, сети и ближайших мероприятиях обслуживания.
services: virtual-machines-windows
documentationcenter: ''
author: harijayms
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/10/2017
ms.author: harijayms
ms.openlocfilehash: a40971e373a6e2fe6324e06816112e2025506ba8
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/11/2018
ms.locfileid: "34057984"
---
# <a name="azure-instance-metadata-service"></a>Служба метаданных экземпляров Azure


Служба метаданных экземпляров Azure предоставляет сведения о выполнении экземпляров виртуальных машин, которые можно использовать для настройки виртуальных машин и управления ими.
Сюда входят сведения о номере SKU, конфигурации сети и ближайших мероприятиях обслуживания. Дополнительные сведения о том, какие сведения доступны, см. в разделе [Категории данных метаданных экземпляров](#instance-metadata-data-categories).

Служба метаданных экземпляров Azure — это конечная точка REST, доступная для всех виртуальных машин IaaS, созданных с помощью нового [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/). Конечная точка доступна по известному IP-адресу без поддержки маршрутизации (`169.254.169.254`), к которому можно получить доступ только в пределах виртуальной машины.

> [!IMPORTANT]
> Эта служба является **общедоступной** во всех регионах Azure.  Она регулярно получает обновления, чтобы предоставлять новые сведения об экземплярах виртуальной машины. На этой странице представлены актуальные сведения о доступных [категориях данных](#instance-metadata-data-categories).

## <a name="service-availability"></a>Доступность службы
Служба доступна в общедоступных регионах Azure. Не все версии API-интерфейса могут быть доступны во всех регионах Azure.

регионы                                        | Доступность?                                 | Поддерживаемые версии
-----------------------------------------------|-----------------------------------------------|-----------------
[Все общедоступные глобальные регионы Azure](https://azure.microsoft.com/regions/)     | Общедоступная версия   | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01
[Azure для государственных организаций](https://azure.microsoft.com/overview/clouds/government/)              | Общедоступная версия | 2017-04-02, 2017-08-01
[Azure для Китая](https://www.azure.cn/)                                                           | Общедоступная версия | 2017-04-02, 2017-08-01
[Azure для Германии](https://azure.microsoft.com/overview/clouds/germany/)                    | Общедоступная версия | 2017-04-02, 2017-08-01

Эта таблица обновляется при наличии обновлений службы, или когда доступны новые поддерживаемые версии.

Чтобы проверить службу метаданных экземпляров, создайте виртуальную машину в [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) или на [портале Azure](http://portal.azure.com) в приведенных выше регионах согласно примерам ниже.

## <a name="usage"></a>Использование

### <a name="versioning"></a>Управление версиями
Для службы метаданных экземпляров включено управление версиями. Версии являются обязательными. На данный момент используется версия Global Azure `2017-12-01`. Сейчас поддерживаются версии 2017-04-02, 2017-08-01 и 2017-12-01.

> [!NOTE] 
> В предыдущих выпусках предварительной версии в качестве api-version поддерживалось значение {latest}. Этот формат больше не поддерживается и в дальнейшем будет считаться устаревшим.

Так как мы добавляем новые версии, вы все еще можете получить доступ к предыдущим версиям для обеспечения совместимости, если используемый скрипт зависит от конкретных форматов данных. Тем не менее предыдущая предварительная версия (2017-03-01) может быть недоступна после выпуска общедоступной службы.

### <a name="using-headers"></a>Использование заголовков
При запросе службы метаданных экземпляров необходимо указать заголовок `Metadata: true`, чтобы избежать случайного перенаправления запроса.

### <a name="retrieving-metadata"></a>Получение метаданных

Используйте метаданные экземпляров для запуска виртуальных машин, созданных или управляемых с помощью [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/). Вы можете получить доступ ко всем категориям данных экземпляров виртуальной машины с помощью следующего запроса:

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01"
```

> [!NOTE] 
> Во всех запросах метаданных экземпляров учитывается регистр.

### <a name="data-output"></a>Выходные данные
По умолчанию служба метаданных экземпляров возвращает данные в формате JSON (`Content-Type: application/json`). Однако при необходимости другие API-интерфейсы могут возвращать данные в различных форматах.
В следующей таблице приведены сведения о других форматах данных, поддерживаемых API-интерфейсами.

API | Формат данных по умолчанию | Другие форматы
--------|---------------------|--------------
/instance | json | text
/scheduledevents | json | Нет

Для доступа к нестандартному формату ответа укажите в запросе запрошенный формат в качестве параметра строки запроса. Например:

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

### <a name="security"></a>Безопасность
Конечная точка службы метаданных экземпляров доступна только в пределах запущенного экземпляра виртуальной машины на IP-адресе, не поддерживающем маршрутизацию. Кроме того, любой запрос с заголовком `X-Forwarded-For` отклоняется службой.
Чтобы гарантировать, что фактический запрос отправлен напрямую и не был получен в ходе непреднамеренного перенаправления, он должен содержать отправляемый заголовок `Metadata: true`. 

### <a name="error"></a>Ошибка
Если элемент данных не найден или запрос неправильно сформирован, службы метаданных экземпляров возвращают стандартные ошибки HTTP. Например:

Код состояния HTTP | Причина
----------------|-------
200 ОК |
400 — недопустимый запрос | Отсутствует заголовок `Metadata: true`
404 — не найдено | Запрашиваемый элемент не существует 
405 — недопустимый метод | Поддерживаются только запросы `GET` и `POST`
429 — слишком много запросов | Сейчас API-интерфейс поддерживает максимум 5 запросов в секунду
500 — ошибка службы     | Повторите попытку через некоторое время

### <a name="examples"></a>Примеры

> [!NOTE] 
> Все ответы API — это строки в формате JSON. Все следующие примеры ответов представлены в удобном для чтения формате.

#### <a name="retrieving-network-information"></a>Получение сведений о сети

**Запрос**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

**Ответ**

> [!NOTE] 
> Ответ представляет собой строку JSON. Следующие примеры ответов представлены в удобном для чтения формате.

```json
{
  "interface": [
    {
      "ipv4": {
        "ipAddress": [
          {
            "privateIpAddress": "10.1.0.4",
            "publicIpAddress": "X.X.X.X"
          }
        ],
        "subnet": [
          {
            "address": "10.1.0.0",
            "prefix": "24"
          }
        ]
      },
      "ipv6": {
        "ipAddress": []
      },
      "macAddress": "000D3AF806EC"
    }
  ]
}

```

#### <a name="retrieving-public-ip-address"></a>Получение общедоступного IP-адреса

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

#### <a name="retrieving-all-metadata-for-an-instance"></a>Получение всех метаданных для экземпляра

**Запрос**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-12-01"
```

**Ответ**

> [!NOTE] 
> Ответ представляет собой строку JSON. Следующие примеры ответов представлены в удобном для чтения формате.

```json
{
  "compute": {
    "location": "westus",
    "name": "avset2",
    "offer": "UbuntuServer",
    "osType": "Linux",
    "placementGroupId": "",
    "platformFaultDomain": "1",
    "platformUpdateDomain": "1",
    "publisher": "Canonical",
    "resourceGroupName": "myrg",
    "sku": "16.04-LTS",
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "",
    "version": "16.04.201708030",
    "vmId": "13f56399-bd52-4150-9748-7190aae1ff21",
    "vmScaleSetName": "",
    "vmSize": "Standard_D1",
    "zone": "1"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipAddress": [
            {
              "privateIpAddress": "10.1.2.5",
              "publicIpAddress": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.1.2.0",
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipAddress": []
        },
        "macAddress": "000D3A36DDED"
      }
    ]
  }
}
```

#### <a name="retrieving-metadata-in-windows-virtual-machine"></a>Получение метаданных в виртуальной машине Windows

**Запрос**

Метаданные экземпляра в Windows можно получить с помощью программы `curl`. 

```bash
curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2017-08-01 | select -ExpandProperty Content
```

Их также можно получить с помощью командлета PowerShell `Invoke-RestMethod`.
    
```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -URI http://169.254.169.254/metadata/instance?api-version=2017-08-01 -Method get 
```

**Ответ**

> [!NOTE] 
> Ответ представляет собой строку JSON. Следующие примеры ответов представлены в удобном для чтения формате.

```json
{
  "compute": {
    "location": "westus",
    "name": "SQLTest",
    "offer": "SQL2016SP1-WS2016",
    "osType": "Windows",
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "publisher": "MicrosoftSQLServer",
    "sku": "Enterprise",
    "version": "13.0.400110",
    "vmId": "453945c8-3923-4366-b2d3-ea4c80e9b70e",
    "vmSize": "Standard_DS2"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipAddress": [
            {
              "privateIpAddress": "10.0.1.4",
              "publicIpAddress": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.0.1.0",
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipAddress": [
            
          ]
        },
        "macAddress": "002248020E1E"
      }
    ]
  }
}
```

## <a name="instance-metadata-data-categories"></a>Категории данных метаданных экземпляров
Следующие категории данных доступны через службу метаданных экземпляров.

Данные | ОПИСАНИЕ | Представленные версии 
-----|-------------|-----------------------
location | Регион Azure, в котором запущена виртуальная машина | 2017-04-02 
name | Имя виртуальной машины. | 2017-04-02
offer | Предоставляют сведения об образе виртуальной машины. Это значение доступно только для образов, развернутых из коллекции образов Azure. | 2017-04-02
publisher | Издатель образа виртуальной машины | 2017-04-02
sku | Определенный номер SKU для образа виртуальной машины | 2017-04-02
версия | Версия образа виртуальной машины | 2017-04-02
osType | Windows или Linux | 2017-04-02
platformUpdateDomain |  [Домен обновления](manage-availability.md), на котором запущена виртуальная машина | 2017-04-02
platformFaultDomain | [Домен сбоя](manage-availability.md), на котором запущена виртуальная машина | 2017-04-02
vmId | [Уникальный идентификатор](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) для виртуальной машины | 2017-04-02
vmSize | [Размер виртуальной машины](sizes.md) | 2017-04-02
subscriptionId | Подписка Azure для виртуальной машины | 2017-08-01
tags | [Теги](../../azure-resource-manager/resource-group-using-tags.md) для виртуальной машины  | 2017-08-01
имя_группы_ресурсов | [Группа ресурсов](../../azure-resource-manager/resource-group-overview.md) для виртуальной машины | 2017-08-01
placementGroupId | [Группа размещения](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) масштабируемого набора виртуальных машин | 2017-08-01
vmScaleSetName | [Имя масштабируемого набора виртуальных машин] (../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) | 2017-12-01
зона | [Зона доступности](../../availability-zones/az-overview.md) виртуальной машины | 2017-12-01 
ipv4/privateIpAddress | Локальный IPv4-адрес виртуальной машины | 2017-04-02
ipv4/publicIpAddress | Общедоступный IPv4-адрес виртуальной машины | 2017-04-02
subnet/address | Адрес подсети виртуальной машины | 2017-04-02 
subnet/prefix | Префикс подсети, пример 24 | 2017-04-02 
ipv6/ipAddress | Локальный IPv6-адрес виртуальной машины | 2017-04-02 
macAddress | Mac-адрес виртуальной машины | 2017-04-02 
scheduledevents | Ознакомьтесь со статьей [Служба метаданных Azure. Запланированные события (предварительная версия) для виртуальных машин Windows](scheduled-events.md) | 2017-08-01
identity | Управляемое удостоверение службы (предварительная версия). Ознакомьтесь с [получением маркера доступа](../../active-directory/managed-service-identity/how-to-use-vm-token.md). | 2018-02-01

## <a name="example-scenarios-for-usage"></a>Примеры сценариев использования  

### <a name="tracking-vm-running-on-azure"></a>Отслеживание виртуальной машины в Azure

Поставщику услуг может потребоваться отслеживать количество виртуальных машин, использующих ваше программное обеспечение, или установить агенты, отслеживающие уникальность виртуальной машины. Чтобы получить уникальный идентификатор для виртуальной машины, используйте поле `vmId` службы метаданных экземпляров.

**Запрос**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**Ответ**

```
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="placement-of-containers-data-partitions-based-faultupdate-domain"></a>Размещение контейнеров и секций данных на основе домена сбоя или обновления 

В некоторых сценариях размещение разных реплик имеет первостепенное значение. Например, для [размещения реплики HDFS](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) или размещения контейнера с помощью [оркестратора](https://kubernetes.io/docs/user-guide/node-selection/) необходимо знать домен сбоя `platformFaultDomain` и домен обновления `platformUpdateDomain`, на которых запущена виртуальная машина.
Кроме того, это можно сделать с помощью [зоны доступности](../../availability-zones/az-overview.md) для экземпляров.
Вы можете запросить данные непосредственно через службу метаданных экземпляров.

**Запрос**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text" 
```

**Ответ**

```
0
```

### <a name="getting-more-information-about-the-vm-during-support-case"></a>Получение дополнительных сведений о виртуальной машине при обращении в службу поддержки 

Чтобы получить дополнительные сведения о виртуальной машине, поставщик услуг может позвонить в службу поддержки. Если клиент предоставит сведения о метаданных вычислений, специалист службы поддержки получит основные сведения о виртуальной машине в Azure. 

**Запрос**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute?api-version=2017-08-01"
```

**Ответ**

> [!NOTE] 
> Ответ представляет собой строку JSON. Следующие примеры ответов представлены в удобном для чтения формате.

```json
{
  "compute": {
    "location": "CentralUS",
    "name": "IMDSCanary",
    "offer": "RHEL",
    "osType": "Linux",
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "publisher": "RedHat",
    "sku": "7.2",
    "version": "7.2.20161026",
    "vmId": "5c08b38e-4d57-4c23-ac45-aca61037f084",
    "vmSize": "Standard_DS2"
  }
}
```

### <a name="examples-of-calling-metadata-service-using-different-languages-inside-the-vm"></a>Примеры вызова службы метаданных с использованием различных языков в виртуальной машине 

Язык | Пример 
---------|----------------
Ruby     | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb
Go  | https://github.com/Microsoft/azureimds/blob/master/imdssample.go            
Python   | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
C++      | https://github.com/Microsoft/azureimds/blob/master/IMDSSample-windows.cpp
C#       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
JavaScript | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
PowerShell | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
Bash       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
Perl;       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
Java       | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
Visual Basic | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.vb
Puppet | https://github.com/keirans/azuremetadata
    

## <a name="faq"></a>Часто задаваемые вопросы
1. Я получаю ошибку `400 Bad Request, Required metadata header not specified`. Что это означает?
   * Для работы службы метаданных экземпляров заголовок `Metadata: true` необходимо передать в запрос. Передача заголовка в вызов REST позволяет получить доступ к службе метаданных экземпляров. 
2. Почему я не получаю сведения о вычислениях для виртуальной машины?
   * Сейчас служба метаданных экземпляров поддерживает только экземпляры, созданные с помощью Azure Resource Manager. В будущем мы можем добавить поддержку виртуальных машин облачной службы.
3. Виртуальная машина была недавно создана с помощью Azure Resource Manager. Почему не отображаются сведения о метаданных вычислений?
   * Чтобы отобразить метаданные вычислений для любой виртуальной машины, созданной после сентября 2016 года, необходимо добавить [тег](../../azure-resource-manager/resource-group-using-tags.md). Для обновления метаданных более старых виртуальных машин (созданных до сентября 2016 г.) удалите расширения или диски данных или добавьте их к виртуальной машине.
4. Отображаются не все данные новой версии 2017-08-01
   * Чтобы отобразить метаданные вычислений для любой виртуальной машины, созданной после сентября 2016 года, необходимо добавить [тег](../../azure-resource-manager/resource-group-using-tags.md). Для обновления метаданных более старых виртуальных машин (созданных до сентября 2016 г.) удалите расширения или диски данных или добавьте их к виртуальной машине.
5. Почему я получаю ошибку `500 Internal Server Error`?
   * Повторите запрос в зависимости от экспоненциальной системы. Если проблема не исчезла, обратитесь в службу поддержки Azure.
6. Где можно задать дополнительные вопросы или оставить комментарии?
   * Добавьте здесь свои комментарии здесь: http://feedback.azure.com.
7. Будет ли это работать для экземпляра масштабируемого набора виртуальных машин?
   * Да, служба метаданных доступна для экземпляров масштабируемого набора. 
8. Как можно получить поддержку для службы?
   * Чтобы получить поддержку для службы, зарегистрируйте проблему на портале Azure для виртуальной машины, в которой вы не можете получить ответ метаданных после нескольких долгих попыток 

   ![Поддержка метаданных экземпляров](./media/instance-metadata-service/InstanceMetadata-support.png)
    
## <a name="next-steps"></a>Дополнительная информация

- См. дополнительные сведения о [запланированных событиях](scheduled-events.md)
