---
title: "Руководство для разработчиков Azure Government"
description: "Данное руководство включает сравнительный анализ характеристик и рекомендации по разработке приложений для разработчиков Azure."
services: 
cloud: gov
documentationcenter: 
author: Joharve2
manager: Chrisnie
editor: 
ms.assetid: 6e04e9aa-1a73-442c-a46c-2e4ff87e58d5
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 10/29/2015
ms.author: jharve
translationtype: Human Translation
ms.sourcegitcommit: 08de3cb0d62e800db88238764e4f9f221d2add06
ms.openlocfilehash: 42c1b022a22cefae15d32690856bdb80b0eaffa9


---
# <a name="microsoft-azure-government-developer-guide"></a>Руководство для разработчиков Microsoft Azure Government
<p> Azure для государственных организаций — это физический экземпляр, который отделен от остальной сети корпорации Майкрософт.  В данном руководстве для разработчиков представлены подробные сведения о различиях, с которыми столкнутся разработчики приложений и администраторы при работе с этими отдельными регионами Azure.

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->


## <a name="in-this-topic"></a>Содержание раздела
* [Обзор](#Overview)
* [Рекомендации для разработчиков](#Guidance)
* [Функции Microsoft Azure Government, доступные в настоящее время](#Features)
* [Сопоставление конечных точек](#Endpoint)
* [Дальнейшие действия](#next)

## <a name="a-nameoverviewaoverview"></a><a name="Overview"></a>Обзор
Microsoft Azure Government – это отдельный экземпляр службы Microsoft Azure, соответствующий требованиям безопасности и законодательным нормам федеральных органов США, местных властей и властей штатов, а также поставщиков решений для указанных организаций. Microsoft Azure Government предлагает физическую и сетевую изоляцию от развертываний, не относящихся к государственным органам, и обеспечивает защищенную работу их сотрудников.

Корпорация Майкрософт предлагает ряд средств для создания и развертывания облачных приложений в глобальных службах Microsoft Azure («глобальная служба») и Microsoft Azure Government.

При создании и развертывании приложений для Azure Government разработчики должны понимать основные различия между этой службой и глобальным развертыванием.  В частности, различия по подготовке и настройке среды программирования, настройке конечных точек, разработке приложений и развертыванию их в качестве служб в Azure Government.

В этом документе описываются эти различия и дополняются сведения, приведенные на сайте [Azure Government](http://www.azure.com/gov "Azure Government") и в [технической библиотеке Microsoft Azure](http://msdn.microsoft.com/cloud-app-development-msdn "MSDN") в MSDN. Официальные сведения доступны также и в других расположениях, например в [Microsoft Azure Trust Center] (https://azure.microsoft.com/support/trust-center/ центре управления безопасностью Microsoft Azure/), [центре документации Azure](https://azure.microsoft.com/documentation/) и [Azure Blogs] (https://azure.microsoft.com/blog/ Блогах Azure/).

Эти сведения предназначены для партнеров и разработчиков, развертывающих приложения в Microsoft Azure Government.

## <a name="a-nameguidanceaguidance-for-developers"></a><a name="Guidance"></a>Рекомендации для разработчиков
Поскольку большая часть доступных в настоящее время технических материалов предполагает, что приложения разрабатываются для глобальной службы, а не для Microsoft Azure Government, важно познакомить разработчиков с основными отличиями приложений, размещаемых в Azure Government.

* Во-первых, есть различия в работе служб и функций, то есть определенные функции в некоторых регионах глобальной службы могут оказаться недоступными в Azure Government.
* Во-вторых, для функций, доступных в Azure Government, есть изменения в процессе конфигурации по отношению к глобальной службе.  Таким образом, необходимо изучить примеры кода, конфигурации и действия, необходимые для построения и выполнения приложений в среде облачных служб Azure Government.

## <a name="a-namefeaturesa-features-currently-available-in-microsoft-azure-government"></a><a name="Features"></a> Функции Microsoft Azure Government, доступные в настоящее время
Azure Goverment в настоящее время имеет следующие службы, доступные в регионах штата US GOV IOWA и US GOV VIRGINIA:

* Виртуальные машины
* Наборы для масштабирования виртуальных машин
* Служба контейнеров
* Учетные записи пакетной службы
* Коллекции удаленных приложений
* Группы доступности
* Виртуальные сети
* Балансировщики нагрузки
* Шлюзы приложений
* Шлюзы виртуальной сети
* Локальные сетевые шлюзы
* Таблицы маршрутов
* Профили диспетчера трафика
* Каналы ExpressRoute
* Группа безопасности сети
* Сетевые интерфейсы
* Общедоступные IP-адреса
* Подключения
* Учетные записи хранения
* Диспетчеры StorSimple
* Службы приложений
* Службы мультимедиа
* Базы данных SQL
* Хранилища данных SQL
* База данных SQL Server Stretch
* Кэши Redis
* пулы эластичных БД SQL
* Серверы SQL Server
* Служба Log Analytics
* Концентраторы событий
* Пространства имен служебной шины
* Azure Active Directory
* Многофакторная проверка подлинности
* Управление правами
* Учетные записи службы автоматизации
* Marketplace

Доступны и другие службы, и их список будет расширяться.  Актуальный список доступных служб см. на [странице регионов](https://azure.microsoft.com/regions/#services). Там будут отмечены все доступные регионы и службы в них.

В данный момент Azure Government поддерживают центры обработки данных US GOV Iowa и US GOV Virginia.  На указанной выше странице регионов можно посмотреть доступные в данный момент центры обработки данных и службы.

## <a name="a-nameendpointaendpoint-mapping"></a><a name="Endpoint"></a>Сопоставление конечных точек
Используйте следующую таблицу для сопоставления общедоступных конечных точек Microsoft Azure и баз данных SQL Azure с конечными точками Azure Government.

| Имя | Конечная точка Azure для государственных организаций |
| --- | --- |
| ActiveDirectoryServiceEndpointResourceId  | https://management.core.usgovcloudapi.net/ |
| GalleryUrl | https://gallery.usgovcloudapi.net/ |
| ManagementPortalUrl | https://manage.windowsazure.us |
| ServiceManagementUrl | https://management.core.usgovcloudapi.net/ |
| PublishSettingsFileUrl | https://manage.windowsazure.us/publishsettings/index |
| ResourceManagerUrl | https://management.usgovcloudapi.net/ |
| SqlDatabaseDnsSuffix | .database.usgovcloudapi.net |
| StorageEndpointSuffix | core.usgovcloudapi.net |
| ActiveDirectoryAuthority | https://login-us.microsoftonline.com/ |
| GraphUrl | https://graph.windows.net/ |
| GraphEndpointResourceId | https://graph.windows.net/ |
| TrafficManagerDnsSuffix | usgovtrafficmanager.net |
| AzureKeyVaultDnsSuffix | vault.usgovcloudapi.net |
| AzureKeyVaultServiceEndpointResourceId | https://vault.usgovcloudapi.net |

* Сведения об аутентификации Azure Resource Manager посредством Azure AD см. в [этой статье](https://msdn.microsoft.com/library/azure/dn790557.aspx).

## <a name="a-namenextanext-steps"></a><a name="next"></a>Дальнейшие действия
Если вы заинтересованы с получении более подробной информации о платформе Azure Government, воспользуйтесь некоторыми из указанных ниже ссылок.

* **[Регистрация для получения пробной версии](https://azuregov.microsoft.com/trial/azuregovtrial)**
* **[Приобретение Azure для государственных организаций и получение доступа](http://azure.com/gov)**
* **[Обзор Azure для государственных организаций](/azure-government-overview)**
* **[Блог по Azure для государственных организаций](http://blogs.msdn.com/b/azuregov/)**
* **[Соответствие требованиям Azure](https://azure.microsoft.com/support/trust-center/compliance/)**

<!--Anchors-->



<!-- Images. -->

[1]: ./media/azure-government-developer-guide/publisherguide.png


<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines-windows-hero-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: storage-whatis-account.md



<!--HONumber=Dec16_HO2-->


