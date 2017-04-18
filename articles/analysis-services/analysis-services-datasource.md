---
title: "Подключения к источникам данных | Документация Майкрософт"
description: "Описание подключений к источникам данных для моделей данных в службах Azure Analysis Services."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 6ec63319-ff9b-4b01-a1cd-274481dc8995
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 04/14/2017
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: a287ebd634a9305229424d0efea266146f88a952
ms.openlocfilehash: 234032630cb3911deb7c7d32cfc4963ad6aee43f
ms.lasthandoff: 01/18/2017


---
# <a name="datasource-connections"></a>Подключения к источникам данных
Моделям данных в службах Azure Analysis Services могут требоваться разные поставщики данных при подключении к определенным источникам данных. В некоторых случаях табличные модели, которые подключаются к источникам данных с помощью собственных поставщиков, таких как собственный клиент SQL Server (SQLNCLI11), могут возвращать ошибку.

Например, при наличии модели данных в памяти или модели данных DirectQuery, которая подключается к облачному источнику данных, такому как база данных SQL Azure, если используются собственные поставщики, кроме SQLOLEDB, может отображаться сообщение об ошибке: **"The provider 'SQLNCLI11.1' is not registered"** (Поставщик "SQLNCLI11.1" не зарегистрирован).

Если же при наличии модели DirectQuery, подключающейся к локальным источникам данных, используются собственные поставщики, может появиться сообщение об ошибке: **"Error creating OLE DB row set. Incorrect syntax near 'LIMIT'"** (Ошибка при создании набора строк OLE DB. Неверный синтаксис рядом с "LIMIT").

## <a name="data-source-providers"></a>Поставщики источников данных
При подключении к локальным или облачным источникам данных поддерживаются следующие поставщики источников данных для моделей данных в памяти или моделей данных DirectQuery:

### <a name="cloud"></a>Облако
| **Источник данных** | **В памяти** | **DirectQuery** |
|  --- | --- | --- |
| Хранилище данных SQL Azure |Поставщик данных .NET Framework для SQL Server |Поставщик данных .NET Framework для SQL Server |
| База данных SQL Azure |Поставщик данных .NET Framework для SQL Server |Поставщик данных .NET Framework для SQL Server | |

### <a name="on-premises-via-gateway"></a>Локально (через шлюз)
|**Источник данных** | **В памяти** | **DirectQuery** |
|  --- | --- | --- |
| SQL Server |SQL Server Native Client 11.0 |Поставщик данных .NET Framework для SQL Server |
| SQL Server |Поставщик Microsoft OLE DB для SQL Server |Поставщик данных .NET Framework для SQL Server | |
| SQL Server |Поставщик данных .NET Framework для SQL Server |Поставщик данных .NET Framework для SQL Server | |
| Oracle |Поставщик Microsoft OLE DB для Oracle |Поставщик данных Oracle для .NET | |
| Oracle |Поставщик данных Oracle для .NET |Поставщик данных Oracle для .NET | |
| Teradata |Поставщик OLE DB для Teradata |Поставщик данных Teradata для .NET | |
| Teradata |Поставщик данных Teradata для .NET |Поставщик данных Teradata для .NET | |
| Система платформы аналитики |Поставщик данных .NET Framework для SQL Server |Поставщик данных .NET Framework для SQL Server | |

> [!NOTE]
> При использовании локального шлюза убедитесь, что установлены 64-разрядные версии поставщиков.
> 
> 

При переносе локальной табличной модели служб SQL Server Analysis Services в службы Azure Analysis Services может потребоваться изменить поставщика.

**Указание поставщика источников данных**

1. В разделе SSDT > **Tabular Model Explorer (Обозреватель табличных моделей)** > **Data Sources (Источники данных)** щелкните правой кнопкой подключение к источнику данных и выберите **Edit Data Source (Изменить источник данных)**.
2. В окне **Edit Connection** (Изменение подключения) щелкните **Advanced** (Дополнительно), чтобы открыть окно дополнительных свойств.
3. В разделе **Set Advanced Properties (Настройка дополнительных свойств)** > **Providers (Поставщики)** выберите соответствующего поставщика.

## <a name="impersonation"></a>Олицетворение
В некоторых случаях может быть необходимо указать другую учетную запись олицетворения. Учетную запись олицетворения можно указать в SSDT или SSMS.

Для локальных источников данных:

* При использовании проверки подлинности SQL олицетворением должна быть учетная запись службы.
* При использовании проверки подлинности Windows задайте пользователя и пароль Windows. Для SQL Server проверка подлинности Windows с использованием конкретной учетной записи олицетворения поддерживается только для моделей данных в памяти.

Для облачных источников данных

* При использовании проверки подлинности SQL олицетворением должна быть учетная запись службы.

## <a name="next-steps"></a>Дальнейшие действия
При наличии локальных источников данных не забудьте установить [локальный шлюз](analysis-services-gateway.md). Дополнительные сведения об управлении сервером в SSDT или SSMS см. в разделе об [управлении сервером](analysis-services-manage.md).


