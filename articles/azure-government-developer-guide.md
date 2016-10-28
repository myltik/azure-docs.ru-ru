<properties 
	pageTitle="Руководство для разработчиков Azure Government" 
	description="Данное руководство включает сравнительный анализ характеристик и рекомендации по разработке приложений для разработчиков Azure." 
	services="" 
	cloud="gov"
	documentationCenter="" 
	authors="Joharve2" 
	manager="Chrisnie" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="azure-government" 
	ms.date="10/29/2015" 
	ms.author="jharve"/>


#  Руководство для разработчиков Microsoft Azure Government 

<p> Microsoft Azure Government  — это физически изолированный от сети экземпляр Microsoft Azure. В данном руководстве для разработчиков представлены подробные сведения о различиях, с которыми столкнутся разработчики приложений и администраторы при работе с этими отдельными регионами Azure.

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->


## Содержание раздела


+ [Обзор](#Overview)
+ [Рекомендации для разработчиков](#Guidance)
+ [Функции Microsoft Azure Government, доступные в настоящее время](#Features)
+ [Сопоставление конечных точек](#Endpoint)
+ [Дальнейшие действия](#next)


## <a name="Overview"></a>Обзор

Microsoft Azure Government – это отдельный экземпляр службы Microsoft Azure, соответствующий требованиям безопасности и законодательным нормам федеральных органов США, местных властей и властей штатов, а также поставщиков решений для указанных организаций. Microsoft Azure Government предлагает физическую и сетевую изоляцию от развертываний, не относящихся к государственным органам, и обеспечивает защищенную работу их сотрудников.

Корпорация Майкрософт предлагает ряд средств для создания и развертывания облачных приложений в глобальных службах Microsoft Azure («глобальная служба») и Microsoft Azure Government.

При создании и развертывании приложений для Azure Government разработчики должны понимать основные различия между этой службой и глобальным развертыванием. В частности, различия по подготовке и настройке среды программирования, настройке конечных точек, разработке приложений и развертыванию их в качестве служб в Azure Government.

Данный документ описывает эти различия и дополняет информацию, приведенную на сайте [Azure Government](http://www.azure.com/gov "Azure Government") и в [технической библиотеке Microsoft Azure](http://msdn.microsoft.com/cloud-app-development-msdn "MSDN") в MSDN. Официальные сведения доступны также и в других расположениях, например в [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/ центре управления безопасностью Microsoft Azure/), [центре документации Azure](https://azure.microsoft.com/documentation/) и [Azure Blogs](https://azure.microsoft.com/blog/ Блогах Azure/).

Эти сведения предназначены для партнеров и разработчиков, развертывающих приложения в Microsoft Azure Government.



## <a name="Guidance"></a>Рекомендации для разработчиков
Поскольку большая часть доступных в настоящее время технических материалов предполагает, что приложения разрабатываются для глобальной службы, а не для Microsoft Azure Government, важно познакомить разработчиков с основными отличиями приложений, размещаемых в Azure Government.

- Во-первых, есть различия в работе служб и функций, то есть определенные функции в некоторых регионах глобальной службы могут оказаться недоступными в Azure Government.

- Во-вторых, для функций, доступных в Azure Government, есть изменения в процессе конфигурации по отношению к глобальной службе. Таким образом, необходимо изучить примеры кода, конфигурации и действия, необходимые для построения и выполнения приложений в среде облачных служб Azure Government.


## <a name="Features"></a> Функции Microsoft Azure Government, доступные в настоящее время
Azure Goverment в настоящее время имеет следующие службы, доступные в регионах штата US GOV IOWA и US GOV VIRGINIA:

- Виртуальные машины
- Облачные службы
- Хранилище
- Active Directory
- Планировщик
- Виртуальная сеть
- База данных SQL
- Файлы Azure
- Службы носителей
- Диспетчер трафика
- Service Bus
- StorSimple
- Кэш Redis
- Служба архивации Azure
- Автоматизация
- ExpressRoute
- и т. д.

Доступны и другие службы, и их список будет расширяться. Актуальный список доступных служб см. на [странице регионов](https://azure.microsoft.com/regions/#services). Там будут отмечены все доступные регионы и службы в них.

В данный момент Azure Government поддерживают центры обработки данных US GOV Iowa и US GOV Virginia. На указанной выше странице регионов можно посмотреть доступные в данный момент центры обработки данных и службы.

## <a name="Endpoint"></a>Сопоставление конечных точек

Используйте следующую таблицу для сопоставления общедоступных конечных точек Microsoft Azure и баз данных SQL Azure с конечными точками Azure Government.


Тип службы|Azure Public|Azure Government
---|---|---
Портал управления|manage.windowsazure.com|manage.windowsazure.us
Общие сведения|*.windows.net|*.usgovcloudapi.net
Core|*.core.windows.net|*.core.usgovcloudapi.net
Среда выполнения приложений|*.cloudapp.net|*.usgovcloudapp.net
Хранилище BLOB-объектов|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net
Хранилище очередей|*.queue.core.windows.net|*.queue.core.usgovcloudapi.net
Хранилище таблиц|*.table.core.windows.net|*.table.core.usgovcloudapi.net
Service Management|management.core.windows.net|management.core.usgovcloudapi.net
База данных SQL|*.database.windows.net|*.database.usgovcloudapi.net
Конечная точка с балансировкой нагрузки ARM|.https://management.windows.net|https://management.usgovcloudapi.net  

* Подробнее о проверке подлинности ARM через Azure AD см. в разделе [Проверка подлинности запросов диспетчера ресурсов Azure](https://msdn.microsoft.com/library/azure/dn790557.aspx)

## <a name="next"></a>Дальнейшие действия

Если вы заинтересованы с получении более подробной информации о платформе Azure Government, воспользуйтесь некоторыми из указанных ниже ссылок.

- **[Регистрация для пробного периода](https://azuregov.microsoft.com/trial/azuregovtrial)**

- **[Получение доступа к платформе Azure Government](http://azure.com/gov)**

- **[Обзор платформы Azure Government](/azure-government-overview)**

- **[Блог по платформе Azure Government](http://blogs.msdn.com/b/azuregov/)**

- **[Соответствие требованиям Azure](https://azure.microsoft.com/support/trust-center/compliance/)**

<!--Anchors-->



<!-- Images. -->

[1]: ./media/azure-government-developer-guide/publisherguide.png


<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines-windows-hero-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: storage-whatis-account.md

<!---HONumber=AcomDC_0817_2016-->
