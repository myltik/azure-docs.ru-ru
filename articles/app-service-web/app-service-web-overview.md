---
title: "Обзор веб-приложений | Документация Майкрософт"
description: "Узнайте, как служба приложений Azure помогает разрабатывать и размещать веб-приложения."
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: 94af2caf-a2ec-4415-a097-f60694b860b3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/28/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d366fc36e2731be34d70fd4e4fa082370c452d63


---
# <a name="web-apps-overview"></a>Обзор веб-приложений
*Веб-приложения службы приложений* — это полностью управляемая вычислительная платформа, оптимизированная для размещения веб-сайтов и веб-приложений. Это предложение Microsoft Azure [платформа как услуга](https://en.wikipedia.org/wiki/Platform_as_a_service) (PaaS) позволяет сосредоточиться на бизнес-логике, так как инфраструктуру для запуска и масштабирования приложений предоставляет среда Azure.

Это 5-минутное видео знакомит вас с веб-приложениями службы приложений Azure.

>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Web-Apps-with-Yochay-Kiriaty/player]
>
>

> [!INCLUDE [app-service-linux](../../includes/app-service-linux.md)]
> 
> 

## <a name="what-is-a-web-app-in-app-service"></a>Что такое веб-приложение в службе приложений?
В службе приложений *веб-приложение* — это вычислительные ресурсы, предоставляемые средой Azure для размещения веб-сайта или веб-приложения.  

В зависимости от выбранной ценовой категории вычислительные ресурсы могут находиться на общих или выделенных виртуальных машинах. Код приложения выполняется на управляемой виртуальной машине, которая изолирована от других клиентов.

Этот код может быть создан на любом языке или платформе, поддерживаемой [службой приложений Azure](../app-service/app-service-value-prop-what-is.md), включая ASP.NET, Node.js, Java, PHP или Python. В веб-приложениях также можно запускать скрипты, которые используют [PowerShell и другие языки сценариев](web-sites-create-web-jobs.md#acceptablefiles) .

Примеры типичных сценариев приложений, для которых можно использовать веб-приложения, см. в статье о [сценариях веб-приложений](https://azure.microsoft.com/documentation/scenarios/web-app/) и разделе **Сценарии и рекомендации** статьи [Сравнение службы приложений Azure, виртуальных машин, Service Fabric и облачных служб](choose-web-site-cloud-service-vm.md#scenarios).

## <a name="why-use-web-apps"></a>Зачем использовать веб-приложения?
Ниже представлены некоторые ключевые функции службы приложений, которые применяются к веб-приложениям.

* **Поддержка нескольких языков и платформ.** Служба приложений превосходно поддерживает ASP.NET, Node.js, Java, PHP и Python. Кроме того, вы можете запустить [PowerShell и другие скрипты или исполняемые файлы](web-sites-create-web-jobs.md) в виртуальных машинах службы приложений.
* **Оптимизация DevOps.** Настраивайте [непрерывную интеграцию и развертывание](app-service-continuous-deployment.md) в Visual Studio Team Services, GitHub или BitBucket. Повышайте уровень обновлений с помощью [тестовых и промежуточных сред](web-sites-staged-publishing.md). Выполняйте [тестирование A/B](app-service-web-test-in-production-get-start.md). Управляйте приложениями в службе приложений с помощью оболочки [Azure PowerShell](../powershell-install-configure.md) или [кроссплатформенного интерфейса командной строки (CLI)](../xplat-cli-install.md).
* **Высокодоступное глобальное масштабирование.** [Увеличивайте](web-sites-scale.md) либо [уменьшайте](../monitoring-and-diagnostics/insights-how-to-scale.md) размер вручную или автоматически. Храните приложения в любом месте глобальной инфраструктуры центра обработки данных. При этом [соглашение об уровне обслуживания](https://azure.microsoft.com/support/legal/sla/app-service/) гарантирует высокую доступность.
* **Подключение к платформам SaaS и локальным данным.** На выбор доступно более 50 [соединителей](../connectors/apis-list.md) для корпоративных систем (например, SAP, Siebel и Oracle), служб SaaS (например, Salesforce и Office 365) и популярных интернет-служб (например, Facebook и Twitter). Получайте доступ к локальным данным с помощью [гибридных подключений](../biztalk-services/integration-hybrid-connection-overview.md) и [виртуальных сетей Azure](web-sites-integrate-with-vnet.md).
* **Безопасность и соответствие требованиям.** Служба приложений совместима со стандартами [ISO, SOC и PCI](https://www.microsoft.com/TrustCenter/).
* **Шаблоны приложений.** Вы можете выбрать любой шаблон из обширного списка шаблонов приложений в [Azure Marketplace](https://azure.microsoft.com/marketplace/). Эти шаблоны позволяют устанавливать популярное программное обеспечение с открытым исходным кодом, например WordPress, Joomla и Drupal, с помощью мастера.
* **Интеграция с Visual Studio.** Выделенные инструменты в Visual Studio упрощают создание, развертывание и отладку приложений.

Кроме того, веб-приложение может использовать преимущества функций, предлагаемых [приложениями API](../app-service-api/app-service-api-apps-why-best-platform.md) (например, поддержку CORS) и [мобильными приложениями](../app-service-mobile/app-service-mobile-value-prop.md) (например, push-уведомления). Дополнительные сведения о типах приложений в службе приложений см. в статье [Что такое служба приложений Azure?](../app-service/app-service-value-prop-what-is.md)

Помимо веб-приложений в службе приложений, Azure предлагает и другие службы, которые можно использовать для размещения веб-сайтов и веб-приложений. В большинстве случаев оптимальным вариантом являются веб-приложения.  Сведения об архитектуре микрослужб см. в статье о [Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric), а если вам нужен дополнительный контроль над виртуальными машинами, на которых выполняется код, см. статью о [виртуальных машинах Azure](https://azure.microsoft.com/documentation/services/virtual-machines/). Дополнительные сведения о выборе между этими службами Azure см. в статье [Сравнение службы приложений Azure, виртуальных машин, Service Fabric и облачных служб](choose-web-site-cloud-service-vm.md).

## <a name="getting-started"></a>Приступая к работе
Инструкции по развертыванию примера кода для нового веб-приложения в службе приложений см. в руководстве [Развертывание первого веб-приложения в Azure за пять минут](app-service-web-get-started.md). Вам понадобится бесплатная учетная запись Azure

Чтобы приступить к работе со службой приложений Azure до создания учетной записи Azure, перейдите к разделу [Пробное использование службы приложений](http://go.microsoft.com/fwlink/?LinkId=523751), где вы можете быстро создать кратковременное веб-приложение начального уровня в службе приложений. Никаких кредитных карт и обязательств.




<!--HONumber=Nov16_HO2-->


