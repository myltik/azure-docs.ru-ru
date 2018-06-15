---
title: Мониторинг облачной службы Azure | Документация Майкрософт
description: Сведения о задачах и механизмах для мониторинга облачной службы Azure.
services: cloud-services
documentationcenter: ''
author: thraka
manager: timlt
editor: ''
ms.assetid: ''
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: adegeo
ms.openlocfilehash: f3a3a1beb8540ee8ab0502379396c06ea505fb44
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/11/2018
ms.locfileid: "29149912"
---
# <a name="introduction-to-cloud-service-monitoring"></a>Введение в мониторинг облачных служб

Вы можете отслеживать для облачных служб ключевые показатели производительности. Каждая роль облачной службы собирает минимальный набор данных: использование ЦП, сетевых и дисковых ресурсов. Если к роли облачной службы применено расширение `Microsoft.Azure.Diagnostics`, эта роль может собирать дополнительные элементы данных. В этой статье содержатся базовые сведения о системе диагностике Azure для облачных служб.

Базовый уровень мониторинга включает сбор данных от счетчиков производительности для экземпляра роли с интервалом в 3 минуты. Базовые данные мониторинга не сохраняются в учетной записи хранения, то есть не требуют дополнительных затрат.

Расширенный уровень мониторинга включает сбор дополнительных метрик с интервалами в 5 минут, 1 час и 12 часов. Статистические данные хранятся в таблицах в учетной записи хранения и удаляются по прошествии 10 дней. Учетная запись хранения настраивается отдельно для каждой роли. Этот параметр можно указать в строке подключения в файлах [.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) и [.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg).


## <a name="basic-monitoring"></a>Базовый мониторинг

Как мы уже упоминали во введении, облачная служба автоматически собирает основные данные мониторинга из узла виртуальной машины. Сюда входят процент использования ЦП и данные операций сетевого ввода-вывода и дискового чтения/записи. Собранные данные мониторинга автоматически отображаются на портале Azure на странице общих сведений и метрик для облачной службы. 

Для базового мониторинга не требуется учетная запись хранения. 

![Плитки базового мониторинга для облачной службы](media/cloud-services-how-to-monitor/basic-tiles.png)

## <a name="advanced-monitoring"></a>Расширенный мониторинг

Для расширенного мониторинга требуется использовать расширение **системы диагностики Azure** (а также, при необходимости, пакет SDK для Application Insights) для тех ролей, которые вы хотите отслеживать. Расширение диагностики использует файл конфигурации (отдельный для каждой роли) с именем **diagnostics.wadcfgx**, в котором можно настроить собираемые метрики диагностики. Расширение диагностики Azure собирает и хранит данные в учетной записи хранения Azure. Эти параметры настраиваются в **WADCFGX-**, [CSDEF-](cloud-services-model-and-package.md#servicedefinitioncsdef) и [CSCFG-](cloud-services-model-and-package.md#serviceconfigurationcscfg) файлах. Это означает, что расширенный мониторинг потребует дополнительных расходов.

При создании каждой роли Visual Studio добавляет в нее расширение системы диагностики Azure. Расширение диагностики может собирать данные следующих типов:

* настраиваемые счетчики производительности;
* Журналы приложений
* Журналы событий Windows
* источник событий .NET;
* Журналы IIS
* Трассировка событий Windows на основе манифестов
* Аварийные дампы
* пользовательские журналы ошибок.

> [!IMPORTANT]
> Все эти данные собираются в учетной записи хранения, но портал **не** содержит встроенных средств для их визуализации. Настоятельно рекомендуем интегрировать в приложение другую службу, например Application Insights.

## <a name="setup-diagnostics-extension"></a>Настройка расширения системы диагностики

Если у вас еще нет **классической** учетной записи хранения, [создайте ее](../storage/common/storage-create-storage-account.md#create-a-storage-account). Для создания учетной записи хранения обязательно используйте **классическую модель развертывания**.

Затем перейдите к ресурсу **Учетная запись хранения (классика)**. Выберите **Параметры** > **Ключи доступа** и скопируйте значение **Основная строка подключения**. Это значение вы примените для облачной службы. 

Чтобы включить расширенную диагностику, необходимо внести изменения в два файла конфигурации: **ServiceDefinition.csdef** и **ServiceConfiguration.cscfg**.

### <a name="servicedefinitioncsdef"></a>ServiceDefinition.csdef

Добавьте в файл **ServiceDefinition.csdef** новый параметр `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` для каждой роли, которая использует расширенную диагностику. Visual Studio автоматически добавляет это значение в файл при создании нового проекта. Если же оно отсутствует, добавьте его прямо сейчас. 

```xml
<ServiceDefinition name="AnsurCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRoleWithSBQueue1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
```

Значение определяет новый параметр, который должен быть добавлен в каждый файл **ServiceConfiguration.cscfg**. 

Скорее всего, у вас есть два **CSCFG**-файла. Один из них (с именем **ServiceConfiguration.cloud.cscfg**) используется для развертывания в Azure, а другой (с именем **ServiceConfiguration.local.cscfg**) — для локальных развертываний в эмулированной среде. Откройте для редактирования каждый файл **.cscfg**. Добавьте в них параметр с именем `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`. Задайте значение **основной строки подключения** классической учетной записи хранения. Если вы хотите использовать локальное хранилище на компьютере разработки, используйте значение `UseDevelopmentStorage=true`.

```xml
<ServiceConfiguration serviceName="AnsurCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2015-04.2.6">
  <Role name="WorkerRoleWithSBQueue1">
    <Instances count="1" />
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=mystorage;AccountKey=KWwkdfmskOIS240jnBOeeXVGHT9QgKS4kIQ3wWVKzOYkfjdsjfkjdsaf+sddfwwfw+sdffsdafda/w==" />
      
      <!-- or use the local development machine for storage
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
      -->
```

## <a name="use-application-insights"></a>Использование Application Insights

Когда вы публикуете облачную службу из Visual Studio, вы можете настроить для нее отправку данных диагностики в Application Insights. Можно отправить данные в уже существующий ресурс Application Insights или создать новый ресурс Azure в рамках процесса публикации. Application Insights может отслеживать для облачной службы статистику доступности, производительности, сбоев и использования. Также в Application Insights можно добавлять пользовательские диаграммы, чтобы контролировать по ним самые важные данные. Чтобы получить данные об экземпляре роли, добавьте в проект облачной службы пакет SDK для Application Insights. Дополнительные сведения об интеграции с Application Insights см. в статье [Application Insights для облачных служб Azure](../application-insights/app-insights-cloudservices.md).

Обратите внимание, что в Application Insights могут отображаться счетчики производительности (и другие параметры), настроенные в расширении службы диагностики Azure, но по-настоящему широкие возможности вы получите только при интеграции пакета SDK для Application Insights в рабочие роли и веб-роли.


## <a name="next-steps"></a>Дополнительная информация

- [Подробнее об Application Insights для облачных служб Azure](../application-insights/app-insights-cloudservices.md)
- [Настройка счетчиков производительности](diagnostics-performance-counters.md)

