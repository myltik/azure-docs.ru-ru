---
title: "Руководство по Azure для государственных организаций для разработчиков | Документация Майкрософт"
description: "Эта статья содержит сравнительный анализ функциональных возможностей и рекомендации по разработке приложений, которые будут использоваться в Azure для государственных организаций."
services: azure-government
cloud: gov
documentationcenter: 
author: smichelotti
manager: zakramer
ms.assetid: 6e04e9aa-1a73-442c-a46c-2e4ff87e58d5
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 02/13/2017
ms.author: stemi
translationtype: Human Translation
ms.sourcegitcommit: 0bab116e977a8490bf0c114c8b829aa1ce07e923
ms.openlocfilehash: 41af18426ce3f8124458a61c3af280c3dd17cfcd


---
# <a name="azure-government-developer-guide"></a>Руководство по Azure для государственных организаций для разработчика
Azure для государственных организаций — это физическая среда, отделенная от остальной сети корпорации Майкрософт. В статье обсуждаются различия, которые нужно понимать разработчикам приложений и администраторам, чтобы эффективно работать с этими самостоятельными регионами Azure.

## <a name="overview"></a>Обзор
Azure для государственных организаций — это отдельный экземпляр службы Microsoft Azure. Он соответствует требованиям по безопасности и соблюдению стандартов, которые налагаются федеральными органами США, органами штатов и местного самоуправления и поставщиками решений для этих органов. Azure для государственных организаций на физическом и сетевом уровнях изолирован от развертываний, не относящихся к государственным органам. Доступ к этой среде имеют только граждане США, прошедшие проверку безопасности.

Корпорация Майкрософт предлагает ряд средств, которые помогут разработчикам создавать и развертывать облачные приложения в глобальных службах Microsoft Azure ("глобальная служба") и службах Microsoft Azure для государственных организаций.

Если разработчик использует для создания и развертывания приложений службы Azure для государственных организаций, а не глобальные службы, он должен понимать основные различия между ними. Особенно важны следующие аспекты: подготовка и настройка среды программирования, настройка конечных точек, разработка приложений и развертывание их в качестве служб в Azure для государственных организаций.

В этом документе обсуждаются различия между этими двумя службами. Он служит дополнением к информации, приведенной на сайте [Azure для государственных организаций](http://www.azure.com/gov "Azure для государственных организаций") и в [технической библиотеке Microsoft Azure](http://msdn.microsoft.com/cloud-app-development-msdn "MSDN") на сайте MSDN. Официальные сведения доступны также и в других расположениях, например [центре управления безопасностью Microsoft Azure](https://azure.microsoft.com/support/trust-center/ "Центр управления безопасностью Microsoft Azure"), [центре документации Azure](https://azure.microsoft.com/documentation/) и [блогах Azure](https://azure.microsoft.com/blog/ "блоги Azure").

Эти сведения предназначены для партнеров и разработчиков, развертывающих приложения в Microsoft Azure Government.

## <a name="guidance-for-developers"></a>Рекомендации для разработчиков
Большая часть существующих технических материалов предполагает, что приложения разрабатываются для глобальной службы, а не для Azure для государственных организаций. Поэтому важно понимать два ключевых отличия в приложениях, которые будут размещаться в Azure для государственных организаций.

* Некоторые функции и службы, существующие в некоторых регионах глобальной службы, будут недоступны в Azure для государственных организаций.
* Конфигурации компонентов в Azure для государственных организаций могут отличаться от конфигураций для глобальной службы. Поэтому вам необходимо изучить все используемые примеры кода, конфигурации и действия, чтобы гарантировать соблюдение условий разработки и выполнения приложений в среде облачных служб Azure для государственных организаций.

Сейчас Azure для государственных организаций поддерживается в центрах обработки данных Айова (для обслуживания государственных организаций США) и Виргиния (для обслуживания государственных организаций США). Актуальный список центров обработки данных и доступных служб см. на странице [Доступность продуктов по регионам](https://azure.microsoft.com/regions/services).


## <a name="endpoint-mapping"></a>Сопоставление конечных точек
Следующая таблица описывает сопоставления общедоступных конечных точек Microsoft Azure и баз данных SQL Azure с конечными точками Azure для государственных организаций.

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



## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения об Azure для государственных организаций см. в следующих статьях.

* [Регистрация для получения пробной версии](https://azuregov.microsoft.com/trial/azuregovtrial)
* [Microsoft Azure для государственных организаций](http://azure.com/gov)
* [Azure Government Overview](/azure-government-overview) (Обзор Azure для государственных организаций)
* [Блог по Azure для государственных организаций](http://blogs.msdn.microsoft.com/azuregov/)
* [Azure Compliance](https://www.microsoft.com/trustcenter/compliance/complianceofferings) (Соответствие Azure нормативным требованиям)



<!--HONumber=Feb17_HO3-->


