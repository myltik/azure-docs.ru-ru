<properties
   pageTitle="Руководство по созданию шаблона решения для Marketplace | Microsoft Azure"
   description="Подробные инструкции по созданию, сертификации и развертыванию шаблона решения с поддержкой нескольких образов виртуальных машин для продажи в Azure Marketplace."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

   <tags
      ms.service="marketplace"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="na"
      ms.date="10/28/2015"
      ms.author="hascipio; v-divte" />

# Руководство по созданию шаблона решения для Azure Marketplace
Выполнив шаг 1 [Создание учетной записи и регистрация][link-acct-creation], вы попадете в раздел [Технические условия, необходимые для создания шаблона решения](marketplace-publishing-solution-template-creation-prerequisites.md) с инструкциями по созданию шаблона решения, совместимого с Azure. Рассмотрим процедуру создания шаблона решения для нескольких виртуальных машин на [портале публикации][link-pubportal] для Azure Marketplace.

## Создание шаблона решения на портале публикации
Откройте страницу [https://publish.windowsazure.com](http://publish.windowsazure.com). **При первом входе в [портал публикации](https://publish.windowsazure.com/) укажите учетную запись, под которой зарегистрирован профиль продавца для вашей компании.** Впоследствии вы сможете добавить в качестве соадминистратора на портале публикации любого сотрудника своей компании.

### 1\. Выберите "Шаблоны решений"

  ![рисунок][img-pubportal-menu-sol-templ]

### 2\. Создайте новый шаблон решения

  ![рисунок][img-pubportal-sol-templ-new]

### 3\. Начните с топологий
Шаблон решения служит родительским элементом для всех своих топологий. В одном шаблоне предложений или решения можно определить сразу несколько топологий. Когда предложение переходит к стадии промежуточного развертывания, вместе с ним отправляются все его топологии. Порядок действий по определению предложения описан ниже. — Создайте топологию — Как правило, в качестве имени топологии для шаблона решения используется **идентификатор топологии**. Идентификатор топологии включается в URL-адрес, как показано ниже.

  Azure Marketplace : http://azure.microsoft.com/marketplace/partners/{PublisherNamespace}/{OfferIdentifier}{TopologyIdentifier}

  Портал предварительной версии Azure: https://ms.portal.azure.com/#gallery/{PublisherNamespace}.{OfferIdentifier}{TopologyIdentifier}

- Добавьте новую версию  

### 4\. Сертифицируйте версии топологии
Передайте ZIP-файл, содержащий все файлы, необходимые для подготовки соответствующей версии топологии. Этот ZIP-файл должен содержать следующее: —файлы *mainTemplate.json* и *createUiDefinition.json* в корне вой папке; — все связанные шаблоны и необходимые скрипты.

Отправив ZIP-файл, нажмите кнопку **Сертификация запроса**. Команда сертификации Майкрософт проверит файлы и выполнит сертификацию топологии.

> [AZURE.TIP]Создание и сертификацию топологий шаблонов решений осуществляют ваши разработчики, а разработкой маркетинговых и юридических материалов может заняться коммерческий, маркетинговый или юридический отдел вашей компании.

## Дальнейшие действия
После создания шаблона решения и отправки ZIP-файла с необходимыми файлами для сертификации выполните инструкции в [руководстве по маркетинговому содержимому Marketplace](marketplace-publishing-push-to-staging.md), прежде чем подготавливать предложение для тестирования в промежуточной среде.

## См. также
- [Приступая к работе: как опубликовать предложение в Azure Marketplace](marketplace-publishing-getting-started.md)

**Образы виртуальных машин** — [Об образах виртуальных машин в Azure](https://msdn.microsoft.com/library/azure/dn790290.aspx)

**Расширения виртуальных машин** — [Общие сведения об агенте и расширениях виртуальных машин Azure](https://msdn.microsoft.com/library/azure/dn832621.aspx) — [Расширения и компоненты виртуальных машин Azure](https://msdn.microsoft.com/library/azure/dn606311.aspx)

**ARM** — [Создание шаблонов Azure ARM](../resource-group-authoring-templates/) — [Простые примеры шаблонов ARM](https://github.com/rjmax/ArmExamples)

**Регулирование учетной записи хранения** — [Отслеживание регулирования учетной записи хранения](http://blogs.msdn.com/b/mast/archive/2014/08/02/how-to-monitor-for-storage-account-throttling.aspx) — [Хранилище класса Premium](../storage/storage-premium-storage-preview-portal/#scalability-and-performance-targets-whru-RUing-premium-storage)

[img-pubportal-menu-sol-templ]: media/marketplace-publishing-solution-template-creation/pubportal-menu-solution-templates.png
[img-pubportal-sol-templ-new]: media/marketplace-publishing-solution-template-creation/pubportal-solution-template-new.png
[link-acct-creation]: marketplace-publishing-microsoft-accounts-creation-registration.md
[link-pubportal]: https://publish.windowsazure.com

<!---HONumber=Nov15_HO3-->