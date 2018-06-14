---
title: Схема определения облачных служб Azure (CSFEF-файл) | Документация Майкрософт
ms.custom: ''
ms.date: 04/14/2015
services: cloud-services
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: b7735dbf-8e91-4d1b-89f7-2f17e9302469
caps.latest.revision: 42
author: thraka
ms.author: adegeo
manager: timlt
ms.openlocfilehash: d5e086a952e18e477177634e5c197c27d4a5cc5f
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34359022"
---
# <a name="azure-cloud-services-definition-schema-csdef-file"></a>Схема определения облачных служб Azure (CSFEF-файл)
Файл определения службы определяет модель службы для приложения. В этом файле содержатся определения ролей, доступных в облачной службе, указываются конечные точки службы и устанавливаются параметры конфигурации для службы. Значения параметров конфигурации задаются в файле конфигурации службы, который описан в [схеме конфигурации облачной службы (классических)](http://msdn.microsoft.com/library/b1ae68cd-cc95-48cb-a4a4-da91dc708a35).

По умолчанию файл схемы конфигурации системы диагностики Azure устанавливается в каталог `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas`. Замените `<version>` установленной версией [пакета SDK для Azure](http://www.windowsazure.com/develop/downloads/).

По умолчанию определения службы хранятся в файле с расширением .csdef.

## <a name="basic-service-definition-schema"></a>Базовая схема определения службы
Файл определения службы должен содержать один элемент `ServiceDefinition`. Определение службы должно содержать как минимум один элемент роли (`WebRole` или `WorkerRole`). В одном определении могут содержаться до 25 ролей. Также вы можете смешивать типы ролей. Определение службы также содержит необязательный элемент `NetworkTrafficRules`, ограничивающий роли, которые могут взаимодействовать с указанными внутренними конечными точками. Определение службы также содержит необязательный элемент `LoadBalancerProbes`, содержащий определенные пользователем зонды работоспособности конечных точек.

Базовый формат файла определения службы приведен ниже.

```xml
<ServiceDefinition name="<service-name>" topologyChangeDiscovery="<change-type>" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" upgradeDomainCount="<number-of-upgrade-domains>" schemaVersion="<version>">
  
  <LoadBalancerProbes>
         …
  </LoadBalancerProbes>
  
  <WebRole …>
         …
  </WebRole>
  
  <WorkerRole …>
         …
  </WorkerRole>
  
  <NetworkTrafficRules>
         …
  </NetworkTrafficRules>

</ServiceDefinition>
```

## <a name="schema-definitions"></a>Определения схем
В следующих разделах описаны схемы:

- [Схема LoadBalancerProbe](schema-csdef-loadbalancerprobe.md)
- [Схема WebRole](schema-csdef-webrole.md)
- [Схема WorkerRole](schema-csdef-workerrole.md)
- [Схема NetworkTrafficRules](schema-csdef-networktrafficrules.md)

##  <a name="ServiceDefinition"></a> Элемент ServiceDefinition
Элемент `ServiceDefinition` — это элемент верхнего уровня файла определения службы.

В таблице ниже описаны атрибуты элемента `ServiceDefinition`.

| Атрибут               | ОПИСАНИЕ |
| ----------------------- | ----------- |
| name                    |Обязательный элемент. Имя службы. Имя должно быть уникальным в пределах учетной записи службы.|
| topologyChangeDiscovery | Необязательный элемент. Указывает тип уведомления об изменении топологии. Возможные значения:<br /><br /> -   `Blast` — как можно быстрее отправляет обновление всем экземплярам роли. Чтобы вы могли использовать этот параметр, роль должна иметь возможность обработать обновление топологии без перезапуска.<br />-   `UpgradeDomainWalk` — отправляет обновление каждому экземпляру роли в последовательном режиме после того, как предыдущий экземпляр успешно принял обновление.|
| schemaVersion           | Необязательный элемент. Указывает версию схемы определения службы. Версия схемы позволяет Visual Studio выбрать правильные средства пакета SDK для использования при проверке схемы, если установлено одновременно несколько версий пакета SDK.|
| upgradeDomainCount      | Необязательный элемент. Указывает число доменов обновления, между которыми распределены роли в этой службе. Экземпляры ролей назначаются домену обновления при развертывании службы. Дополнительные сведения см. в разделе [Обновление роли или развертывания облачной службы](cloud-services-how-to-manage-portal.md#update-a-cloud-service-role-or-deployment).<br /><br /> Можно указать до 20 доменов обновления. Если число доменов обновления не указано, по умолчанию оно равно 5.|