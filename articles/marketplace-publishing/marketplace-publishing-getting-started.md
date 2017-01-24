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
ms.date: 12/07/2016
ms.author: hascipio
translationtype: Human Translation
ms.sourcegitcommit: 98840e0bc1565eab0e5936cb65b68852e1423450
ms.openlocfilehash: fd71f55f636fa1b7918536d7e6b51fb6a2a05903


---
# <a name="how-to-publish-and-manage-an-offer-in-the-azure-marketplace"></a>Как опубликовать предложение и управлять им в Azure Marketplace
Эта статья поможет разработчикам создать и развернуть решения, а также управлять ими в Azure Marketplace, чтобы их могли приобрести и использовать другие клиенты и партнеры Azure.

## <a name="what-is-the-azure-marketplace"></a>Что такое Azure Marketplace?
Azure Marketplace позволяет издателям Azure предлагать и продавать свои инновационные решения или службы другим разработчикам, независимым продавцам ПО и ИТ-специалистам, которые хотят быстро разрабатывать облачные приложения и мобильные решения. Если решение предназначено для бизнес-пользователей, воспользуйтесь магазином [AppSource](http://appsource.microsoft.com).


## <a name="supported-types-of-solutions"></a>Поддерживаемые типы решений
Первое, что нужно сделать в качестве издателя, — определить тип решений, которые предлагает ваша компания. Azure Marketplace поддерживает следующие типы предложений:

|Тип решения|Виртуальная машина|Шаблон решения|
|---|---|---|
|Определение|Предварительно настроенные образы с полностью установленной операционной системой и одним или несколькими приложениями. Образ виртуальной машины содержит всю информацию для создания и развертывания виртуальных машин в службе виртуальных машин Azure.|Структура данных, которая ссылается на одну или несколько независимых служб Azure (включая опубликованные другими издателями), позволяя клиентам Azure согласованно развертывать одно или несколько предложений.|
|Пример|**Пример**. Как издатель Azure, вы можете создать и проверить виртуальную машину с инновационной службой базы данных, которая будет настолько интересным решением, что другие подписчики Azure захотят приобрести и развернуть такую виртуальную машину в своей облачной среде.|**Например**, как издатель Azure, вы можете включить несколько независимых служб Azure в пакет, который позволяет быстро развернуть безопасные облачные службы с высоким уровнем доступности и балансировкой нагрузки. Другие подписчики Azure могут сэкономить время, подготовив шаблон решения, соответствующий цели, а не выполнив поиск одних и тех же или схожих служб Azure, подготовив их, настроив и развернув.|

> [!NOTE]
> Обратите внимание, что некоторые шаги общие для разных типов решений, а другие используются только в конкретном решении. В этой статье представлен короткий обзор шагов, которые необходимо выполнить для каждого типа решения.

## <a name="how-to-publish-a-solution"></a>Публикация решения
![рисунок](media/marketplace-publishing-getting-started/img01.png)

### <a name="1-nominate-your-solution-for-pre-approval"></a>1. Предложите свое решение для предварительного утверждения
> [!IMPORTANT]
> Перед началом работы с Azure Marketplace вы должны получить [предварительное утверждение](http://azure.com/certified).

- [Подача запроса на предварительное утверждение, сертифицированное Microsoft Azure.](marketplace-publishing-azure-certification.md)

### <a name="2-register-your-account-as-a-microsoft-seller"></a>2) Зарегистрируйте свою учетную запись в качестве продавец Майкрософт
- [Зарегистрируйте свою учетную запись Майкрософт в качестве учетной записи разработчика Майкрософт](marketplace-publishing-accounts-creation-registration.md)

### <a name="3-publish-your-solution"></a>3. Опубликуйте свое решения
1. Выполните технические требования
  - [Выполнение нетехнических предварительных требований.](marketplace-publishing-pre-requisites.md)
  - [Предварительные технические требования для виртуальной машины](marketplace-publishing-vm-image-creation-prerequisites.md)
  - [Предварительные технические требования для шаблона решения](marketplace-publishing-solution-template-creation-prerequisites.md)
2. Создайте предложение
  - [Виртуальная машина](marketplace-publishing-vm-image-creation.md)
  - [Шаблон решения](marketplace-publishing-solution-template-creation.md)
3. [Создание предложения с маркетинговыми материалами](marketplace-publishing-push-to-staging.md)
4. Протестируйте предложение на этапе промежуточного развертывания
  - [Протестируйте свое предложение виртуальной машины на этапе промежуточного развертывания](marketplace-publishing-vm-image-test-in-staging.md)
  - [Тестирование предложения шаблона решения на этапе промежуточного развертывания](marketplace-publishing-solution-template-test-in-staging.md)
5. [Развертывание предложения в Azure Marketplace](marketplace-publishing-push-to-production.md)


### <a name="virtual-machine-image-specific"></a>Рекомендации, касающиеся образа виртуальной машины
* [Локальное создание образа виртуальной машины](marketplace-publishing-vm-image-creation-on-premise.md)
* [Создание первой виртуальной машины Windows на портале Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Создание виртуальной машины Linux в Azure с помощью портала](../virtual-machines/virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Как устранить распространенные неполадки, возникающие в процессе создания виртуального жесткого диска](marketplace-publishing-vm-image-creation-troubleshooting.md)

## <a name="how-to-manage-your-solution"></a>Управление решением
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



<!--HONumber=Dec16_HO2-->


