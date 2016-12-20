---
title: "Общие сведения о создании и развертывании предложения в Marketplace | Документация Майкрософт"
description: "Описание шагов, необходимых для того, чтобы стать утвержденным разработчиком Майкрософт и создавать и развертывать образ виртуальной машины, шаблон, службу данных и службу разработчика в Azure Marketplace."
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 5343bd26-c6e4-4589-85b7-4a2c00bba8ab
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2016
ms.author: hascipio
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: aed47a7e9aa06e48199a71f67ae6d82bcfe27c9a


---
# <a name="how-to-publish-and-manage-an-offer-in-the-azure-marketplace"></a>Как опубликовать предложение и управлять им в Azure Marketplace
Эта статья поможет разработчикам создать и развернуть решения, а также управлять ими в Azure Marketplace, чтобы их могли приобрести и использовать другие клиенты и партнеры Azure.

## <a name="what-is-the-azure-marketplace"></a>Что такое Azure Marketplace?
Azure Marketplace — это платформа для размещения служб, которые помогают подписчикам Azure упростить разработку локальных и облачных решений и приложений. Вы можете использовать [сертифицированные в Azure](http://azure.com/certified) решения в качестве строительных блоков для быстрой разработки инновационных приложений или служб как для своего предприятия, так и для других подписчиков Azure.

Azure Marketplace позволяет издателям Azure предлагать и продавать свои инновационные решения или службы другим разработчикам, независимым продавцам ПО и ИТ-специалистам, которые хотят быстро разрабатывать облачные приложения и мобильные решения.

## <a name="supported-types-of-offers"></a>Поддерживаемые типы предложений
Первое, что нужно сделать в качестве издателя, — определить тип решений, которые предлагает ваша компания. Azure Marketplace поддерживает три типа предложений.

* **Образы виртуальных машин** — предварительно настроенные, с полностью установленной операционной системой и одним или несколькими приложениями. Образ виртуальной машины содержит всю информацию для создания и развертывания виртуальных машин в службе виртуальных машин Azure.

  > [!NOTE]
  > **Пример**. Как издатель Azure, вы можете создать и проверить виртуальную машину с инновационной службой базы данных, которая будет настолько интересным решением, что другие подписчики Azure захотят приобрести и развернуть такую виртуальную машину в своей облачной среде.
  >
  >
* **Службы для разработчиков** — полностью управляемые службы для разработки приложений или управления системами. Они предлагают возможности, которые упрощают быструю разработку облачных приложений в Azure.

  > [!NOTE]
  > **Пример**. Как издатель Azure, вы можете разработать службу, доступную через API-интерфейс (с размещением в Azure или в другой среде), которая создает прогнозы на основе исторических данных. Спецификации этой службы позволят использовать ее и другим подписчикам Azure, которые создают новые решения. Вы можете развернуть эту службу в Azure Marketplace, чтобы другие пользователи могли найти, приобрести и применить ее в своих решениях.
  >
  >
* **Шаблон решения** — структура данных, которая ссылается на одну или несколько независимых служб Azure (включая опубликованные другими издателями), позволяя клиентам Azure согласованно развертывать одно или несколько предложений.

  > [!NOTE]
  > **Пример**. Как издатель Azure, вы можете включить несколько независимых служб Azure в пакет, который позволяет быстро развернуть безопасную облачную службу с высоким уровнем доступности и балансировкой нагрузки. Оценив возможность экономии времени, другие подписчики Azure приобретут ваш шаблон решения, вместо того чтобы самостоятельно искать и настраивать аналогичные службы Azure.
  >
  >

Некоторые действия являются общими для различных типов решений. В этой статье представлен короткий обзор шагов, которые необходимо выполнить для каждого типа решения.

## <a name="1-pre-requisites"></a>1. Предварительные требования
> [!NOTE]
> Перед началом работы с Azure Marketplace вы должны получить [предварительное утверждение](http://azure.com/certified).
>
>

1. [Подача запроса на предварительное утверждение, сертифицированное Microsoft Azure.](marketplace-publishing-azure-certification.md)
2. [Создание и регистрация учетной записи разработчика Майкрософт.](marketplace-publishing-accounts-creation-registration.md)
3. [Выполнение нетехнических предварительных требований.](marketplace-publishing-pre-requisites.md)

## <a name="2-publishing-your-offer"></a>2) Публикация предложения
### <a name="21-complete-offer-specific-technical-pre-requisites"></a>2.1. Выполнение технических требований к определенному предложению
* [Предварительные технические требования для виртуальной машины](marketplace-publishing-vm-image-creation-prerequisites.md)
* [Предварительные технические требования для шаблона решения](marketplace-publishing-solution-template-creation-prerequisites.md)

### <a name="22-create-your-offer"></a>2.2. Создание предложения
1. Создайте предложение, следуя рекомендациям для определенного предложения.
   * [Создание предложения виртуальной машины](marketplace-publishing-vm-image-creation.md)
   * [Создание предложения шаблона решения](marketplace-publishing-solution-template-creation.md)
2. [Создание предложения с маркетинговыми материалами](marketplace-publishing-push-to-staging.md)

### <a name="23-test-your-offer-in-staging"></a>2.3. Тестирование предложения на этапе промежуточного развертывания
* [Протестируйте свое предложение виртуальной машины на этапе промежуточного развертывания](marketplace-publishing-vm-image-test-in-staging.md)
* [Тестирование предложения шаблона решения на этапе промежуточного развертывания](marketplace-publishing-solution-template-test-in-staging.md)

### <a name="24-deploy-your-offer-to-the-marketplace"></a>2.4. Развертывание предложения в Marketplace
* [Развертывание предложения в Azure Marketplace](marketplace-publishing-push-to-production.md)
* [Решение распространенных проблем с публикацией в Marketplace](marketplace-publishing-support-common-issues.md)
* Дополнительные сведения об используемых порталах см. в списке [порталов, которые вам потребуются](marketplace-publishing-portals.md).

### <a name="virtual-machine-image-specific"></a>Рекомендации, касающиеся образа виртуальной машины
* [Локальное создание образа виртуальной машины](marketplace-publishing-vm-image-creation-on-premise.md)
* [Создание первой виртуальной машины Windows на портале Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Создание виртуальной машины Linux в Azure с помощью портала](../virtual-machines/virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Как устранить распространенные неполадки, возникающие в процессе создания виртуального жесткого диска](marketplace-publishing-vm-image-creation-troubleshooting.md)

## <a name="3-post-publishing-management-of-your-offer"></a>3. Управление предложением после публикации
* [Руководство по эксплуатации предложений виртуальных машин](marketplace-publishing-vm-image-post-publishing.md)
* [Обновление нетехнических сведений о предложении или номере SKU](marketplace-publishing-vm-image-post-publishing.md#2-how-to-update-the-non-technical-details-of-an-offer-or-a-sku)
* [Обновление технических сведений SKU](marketplace-publishing-vm-image-post-publishing.md#1-how-to-update-the-technical-details-of-a-sku)
* [Добавление нового SKU во внесенное в список предложение](marketplace-publishing-vm-image-post-publishing.md#3-how-to-add-a-new-sku-under-a-listed-offer)
* [Изменение числа дисков данных для внесенного в список номера SKU](marketplace-publishing-vm-image-post-publishing.md#4-how-to-change-the-data-disk-count-for-a-listed-sku)
* [Удаление внесенного в список предложения из Azure Marketplace](marketplace-publishing-vm-image-post-publishing.md)
* [Удаление внесенного в список номера SKU из Azure Marketplace](marketplace-publishing-vm-image-post-publishing.md#6-how-to-delete-a-listed-sku-from-the-azure-marketplace)
* [Удаление текущей версии внесенного в список номера SKU из Azure Marketplace](marketplace-publishing-vm-image-post-publishing.md#7-how-to-delete-the-current-version-of-a-listed-sku-from-the-azure-marketplace)
* [Возврат рабочих значений цен для внесенных в список элементов](marketplace-publishing-vm-image-post-publishing.md#8-how-to-revert-listing-price-to-production-values)
* [Возврат рабочих значений для модели выставления счетов](marketplace-publishing-vm-image-post-publishing.md#9-how-to-revert-billing-model-to-production-values)
* [Возврат рабочего значения параметра видимости для внесенного в список номера SKU](marketplace-publishing-vm-image-post-publishing.md#10-how-to-revert-visibility-setting-of-a-listed-sku-to-the-production-value)
* [Как изменить статус участия в программе поощрения поставщиков облачных решений](marketplace-publishing-csp-incentive.md)
* [Основные сведения об отчетах Seller Insights](marketplace-publishing-report-seller-insights.md)
* [Основные сведения об отчетах о выплатах](marketplace-publishing-report-payout.md)
* [Поддержка издателей](marketplace-publishing-get-publisher-support.md)

## <a name="additional-resources"></a>дополнительные ресурсы.
* [Настройка Azure PowerShell](marketplace-publishing-powershell-setup.md)



<!--HONumber=Nov16_HO3-->


