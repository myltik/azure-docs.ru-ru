---
title: Схема определения облачных служб Azure (файл cscfg) | Документация Майкрософт
services: cloud-services
ms.custom: ''
ms.date: 12/07/2016
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: 3ddc7fea-3339-4fc0-bdf9-853c32b25f69
caps.latest.revision: 35
author: thraka
ms.author: adegeo
manager: timlt
ms.openlocfilehash: 6347314e7f279356f4f3944f3238deda84f10fc0
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34358223"
---
# <a name="azure-cloud-services-config-schema-cscfg-file"></a>Схема конфигурации облачных служб Azure (файл cscfg)
Файл конфигурации службы определяет число экземпляров роли, которые развертываются для каждой роли в службе, значения всех параметров конфигурации и отпечатки всех сертификатов, связанных с ролью. Если служба является частью виртуальной сети, нужно указать сведения о конфигурации сети в файле конфигурации службы и в файле конфигурации виртуальной сети. По умолчанию для файла конфигурации службы используется расширение .cscfg.

Модель службы описывается в [схеме определения облачной службы (классический вариант)](schema-csdef-file.md).

По умолчанию файл схемы конфигурации системы диагностики Azure устанавливается в каталог `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas`. Замените `<version>` установленной версией [пакета SDK для Azure](https://azure.microsoft.com/downloads/).

Дополнительные сведения о настройке ролей для службы см. в статье [Что такое модель облачных служб и как создать ее пакет?](cloud-services-model-and-package.md)

## <a name="basic-service-configuration-schema"></a>Базовая схема конфигурации службы
Ниже приводится базовый формат файла конфигурации службы.

```xml
<ServiceConfiguration serviceName="<service-name>" osFamily="<osfamily-number>" osVersion="<os-version>" schemaVersion="<schema-version>">

  <Role …>
    …
  </Role>

  <NetworkConfiguration>
    …
  </NetworkConfiguration>

</ServiceConfiguration>
```

## <a name="schema-definitions"></a>Определения схем
В следующих статьях описаны схемы для элемента `ServiceConfiguration`:

- [Схема Role](schema-cscfg-role.md)
- [Схема NetworkConfiguration](schema-cscfg-networkconfiguration.md)

## <a name="service-configuration-namespace"></a>Пространство имен для конфигурации службы
Для файла конфигурации диагностики используется пространство имен XML `http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration`.

##  <a name="ServiceConfiguration"></a> Элемент ServiceConfiguration
Элемент `ServiceConfiguration` занимает верхний уровень в файле конфигурации службы.

В следующей таблице описаны атрибуты элемента `ServiceConfiguration`. Значения всех атрибутов имеют строковый тип.

| Атрибут | ОПИСАНИЕ |
| --------- | ----------- |
|serviceName|Обязательный элемент. Имя облачной службы. Указанное здесь имя должно соответствовать имени, указанному в файле определения службы.|
|osFamily|Необязательный элемент. Определяет гостевую ОС, которая будет запускаться на экземплярах ролей в облачной службе. Информацию о поддерживаемых выпусках гостевой ОС вы найдете в [таблице совместимости выпусков гостевых ОС Azure и пакетов SDK](cloud-services-guestos-update-matrix.md).<br /><br /> Если вы не включите значение `osFamily` и не укажете конкретную версию гостевой ОС через атрибут `osVersion`, по умолчанию используется значение 1.|
|osVersion|Необязательный элемент. Определяет версию гостевой ОС, которая будет запускаться на экземплярах ролей в облачной службе. Дополнительную информацию о версиях гостевой ОС вы найдете в [таблице совместимости выпусков гостевых ОС Azure и пакетов SDK](cloud-services-guestos-update-matrix.md).<br /><br /> Вы можете настроить автоматическое обновление гостевой ОС до последней версии. Для этого присвойте атрибуту `osVersion` значение `*`. Если задано значение `*`, экземпляры роли развертываются с последней версией гостевой ОС из указанного семейства ОС, и эти версии автоматически обновляются при выходе новых версий гостевой ОС.<br /><br /> Чтобы вручную указать конкретную версию, используйте `Configuration String` (строку конфигурации) из раздела **о будущих, текущих и переходных версиях гостевой ОС** в [таблице совместимости выпусков гостевых ОС Azure и пакетов SDK](cloud-services-guestos-update-matrix.md).<br /><br /> Для атрибута `osVersion` по умолчанию используется значение `*`.|
|schemaVersion|Необязательный элемент. Указывает версию схемы конфигурации службы. Версия схемы позволяет Visual Studio выбрать правильные средства пакета SDK для использования при проверке схемы, если установлено одновременно несколько версий пакета SDK. Дополнительную информацию о схеме и совместимости версий вы найдете в [таблице совместимости выпусков гостевых ОС Azure и пакетов SDK](cloud-services-guestos-update-matrix.md).|

Файл конфигурации службы должен содержать один элемент `ServiceConfiguration`. Элемент `ServiceConfiguration` может содержать любое количество элементов `Role` и не более одного элемента `NetworkConfiguration`.