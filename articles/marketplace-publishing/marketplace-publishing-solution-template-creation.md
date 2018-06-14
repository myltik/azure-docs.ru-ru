---
title: Руководство по созданию шаблона решения для Marketplace | Документация Майкрософт
description: Подробные инструкции по созданию, сертификации и развертыванию шаблона решения с поддержкой нескольких образов виртуальных машин для продажи в Azure Marketplace.
services: marketplace-publishing
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: ''
ms.assetid: e14e05f2-2385-4ce0-b351-0747cb74ba19
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2016
ms.author: mbaldwin
ms.openlocfilehash: 83e69701aa5e8c11354e7551d66f3fdd18289000
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/16/2018
ms.locfileid: "29937314"
---
# <a name="guide-to-create-a-solution-template-for-azure-marketplace"></a>Руководство по созданию шаблона решения для Azure Marketplace
Выполнив шаг 1 [Создание учетной записи разработчика Майкрософт][link-acct-creation], вы перейдете к статье [Технические компоненты, необходимые для создания шаблона решения для Azure Marketplace](marketplace-publishing-solution-template-creation-prerequisites.md) с инструкциями по созданию шаблона решения, совместимого с Azure. Рассмотрим процедуру создания шаблона решения для нескольких виртуальных машин на [портале публикации][link-pubportal] для Azure Marketplace.

## <a name="create-your-solution-template-offer-in-the-publishing-portal"></a>Создание шаблона решения на портале публикации
Перейдите на сайт [https://publish.windowsazure.com](http://publish.windowsazure.com). При первом входе в [портал публикации](https://publish.windowsazure.com/)укажите учетную запись, под которой зарегистрирован профиль продавца для вашей компании. Впоследствии вы сможете добавить в качестве соадминистратора на портале публикации любого сотрудника своей компании.

### <a name="1-select-solution-templates"></a>1. Выберите "Шаблоны решений"
  ![рисунок][img-pubportal-menu-sol-templ]

### <a name="2-create-a-new-solution-template"></a>2. Создайте новый шаблон решения
  ![рисунок][img-pubportal-sol-templ-new]

### <a name="3-start-with-topologies"></a>3. Начните с топологий
Шаблон решения служит родительским элементом для всех своих топологий. В одном шаблоне предложений или решения можно определить сразу несколько топологий. Когда предложение переходит к стадии промежуточного развертывания, вместе с ним отправляются все его топологии. Выполните следующее, чтобы определить предложение:     

* Создайте топологию (как правило, в качестве имени топологии для шаблона решения используется "идентификатор топологии"). Идентификатор топологии включается в URL-адрес, как показано ниже.

  Azure Marketplace: http://azure.microsoft.com/marketplace/partners/{PublisherNamespace}/{OfferIdentifier}{TopologyIdentifier}

  Портал Azure: https://portal.azure.com/#gallery/{PublisherNamespace}.{OfferIdentifier}{TopologyIdentifier}
* Добавьте новую версию.

### <a name="4-get-your-topology-versions-certified"></a>4. Сертифицируйте версии топологии
Передайте ZIP-файл, содержащий все файлы, необходимые для подготовки соответствующей версии топологии. Этот ZIP-файл должен содержать следующее:

* файлы *mainTemplate.json* и *createUiDefinition.json* в корневом каталоге;
* связанные шаблоны и все необходимые сценарии.

  > [!TIP]
  > Создание и сертификацию топологий шаблонов решений осуществляют ваши разработчики, а разработкой маркетинговых и юридических материалов может заняться коммерческий, маркетинговый или юридический отдел вашей компании.
  >
  >

## <a name="next-steps"></a>Дополнительная информация
После создания шаблона решения и отправки ZIP-файла выполните инструкции в статье [Завершение создания предложения с маркетинговыми материалами](marketplace-publishing-push-to-staging.md), прежде чем перемещать предложение в промежуточную среду. Полный список статей о публикации в Marketplace см. в статье [Как опубликовать предложение и управлять им в Azure Marketplace](marketplace-publishing-getting-started.md).

Вас также могут заинтересовать следующие связанные статьи:

* Образы виртуальных машин: [Об образах виртуальных машин в Azure](https://msdn.microsoft.com/library/azure/dn790290.aspx)
* Расширения виртуальных машин: [общие сведения об агенте, расширениях](https://msdn.microsoft.com/library/azure/dn832621.aspx) и [компонентах виртуальных машин Azure](https://msdn.microsoft.com/library/azure/dn606311.aspx)
* Azure Resource Manager: [Создание шаблонов Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) и [простые примеры шаблонов](https://github.com/rjmax/ArmExamples)
* Регулирование учетной записи хранения: [How to Monitor for Storage Account Throttling](http://blogs.msdn.com/b/mast/archive/2014/08/02/how-to-monitor-for-storage-account-throttling.aspx) (Отслеживание регулирования учетной записи хранения) и [Хранилище класса "Премиум": высокопроизводительная служба хранилища для рабочих нагрузок виртуальных машин Azure](../virtual-machines/windows/premium-storage.md#scalability-and-performance-targets)

[img-pubportal-menu-sol-templ]:media/marketplace-publishing-solution-template-creation/pubportal-menu-solution-templates.png
[img-pubportal-sol-templ-new]:media/marketplace-publishing-solution-template-creation/pubportal-solution-template-new.png
[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md
[link-pubportal]:https://publish.windowsazure.com
