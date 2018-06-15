---
title: Общие сведения о создании и развертывании предложения в Marketplace | Документация Майкрософт
description: Описание шагов, необходимых для того, чтобы стать утвержденным разработчиком Майкрософт и создавать и развертывать образ виртуальной машины, шаблон, службу данных и службу разработчика в Azure Marketplace
services: marketplace-publishing
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: ''
ms.assetid: 5343bd26-c6e4-4589-85b7-4a2c00bba8ab
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2017
ms.author: mbaldwin
ms.openlocfilehash: 02df78de48c8466f3488ef5f8346e1852b4e3ded
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/16/2018
ms.locfileid: "29943179"
---
> [!NOTE]
> Этот документ больше не актуален и не содержит надежные сведения. С инструкциями по публикации предложений в Microsoft Azure Marketplace можно ознакомиться в [руководстве по Microsoft Azure Marketplace для продавца](https://docs.microsoft.com/azure/marketplace/seller-guide/cloud-partner-portal-seller-guide).

# <a name="publish-and-manage-an-offer-in-the-azure-marketplace"></a>Публикация предложения и управление им в Azure Marketplace
Эта статья поможет разработчикам создать и развернуть решения, а также управлять ими в Azure Marketplace, чтобы их могли приобрести и использовать другие клиенты и партнеры Azure.

## <a name="marketplace-publishing"></a>Публикация в Marketplace
Издатель Azure может распространять и продавать свои инновационные решения или службы другим разработчикам, независимым поставщикам программного обеспечения и ИТ-специалистам в Marketplace. Через Marketplace вы можете взаимодействовать с клиентами, которые хотят быстро разрабатывать облачные приложения и мобильные решения. Если решение предназначено для бизнес-пользователей, воспользуйтесь магазином [AppSource](http://appsource.microsoft.com).


## <a name="supported-types-of-solutions"></a>Поддерживаемые типы решений
Первое, что нужно сделать издателю, — определить тип решений, которые предлагает компания. Marketplace поддерживает следующие типы предложений:

|Тип решения|Виртуальная машина.|Шаблон решения|
|---|---|---|
|**Определение**|Предварительно настроенные образы с полностью установленной операционной системой и одним или несколькими приложениями. Образ виртуальной машины содержит всю информацию для создания и развертывания виртуальных машин в службе виртуальных машин Azure.|Структура данных, которая может ссылаться на одну или несколько отдельных служб Azure, включая службы, опубликованные другими продавцами. Таким образом, подписчики Azure могут согласованно развертывать одно или несколько предложений.|
|**Пример**|Издатель Azure может создать и проверить виртуальную машину с помощью инновационной службы базы данных. Другие подписчики Azure захотят приобрести и развернуть такую виртуальную машину в своей облачной среде.|Издатель Azure может включить несколько независимых служб Azure в пакет, которые позволят быстро развернуть безопасные облачные службы с высоким уровнем доступности и балансировкой нагрузки. Другие подписчики Azure могут сэкономить время, подготовив шаблон решения в соответствии с целью. Им не придется самостоятельно искать, приобретать, развертывать и настраивать аналогичные службы Azure.|

> [!NOTE]
> Некоторые шаги подходят для разных типов решений, а другие используются только в конкретном решении. В этой статье представлен короткий обзор шагов, которые необходимо выполнить для каждого типа решения.

## <a name="publish-a-solution"></a>Публикация решения
![Назначение, регистрация и публикация](media/marketplace-publishing-getting-started/img01.png)

### <a name="nominate-your-solution-for-pre-approval"></a>Предложите свое решение для предварительного утверждения
Для публикации [решения](https://createopportunity.azurewebsites.net) виртуальной машины в Marketplace заполните **форму на предварительное утверждение решения** для получения сертификации Microsoft Azure Certified.

>[!NOTE]
> Если вы работаете с менеджером PAM или менеджером партнеров DX, попросите, чтобы они представили ваше решение к участию в программе сертификации Azure. Вы также можете перейти на веб-страницу [Microsoft Azure Certified](http://createopportunity.azurewebsites.net) и заполнить форму заявки. В поле **Microsoft Sponsor Contact** (Контактное лицо) введите электронную почту вашего менеджера PAM или менеджера партнеров DX.

Если вы соответствуете основным требованиям [политик участия в Azure Marketplace](http://go.microsoft.com/fwlink/?LinkID=526833) и ваше приложение утверждено, мы начнем с вами совместную работу по адаптации решения в Marketplace.

### <a name="register-your-account-as-a-microsoft-seller"></a>Зарегистрируйте свою учетную запись в качестве продавец Майкрософт
[Зарегистрируйте свою учетную запись Майкрософт в качестве учетной записи разработчика Майкрософт](marketplace-publishing-accounts-creation-registration.md).

### <a name="publish-your-solution"></a>Опубликуйте свое решения
Для публикации решения в Marketplace сделайте следующее:
1. Выполните нетехнические требования.

    a. Выполните [предварительные нетехнические требования](marketplace-publishing-pre-requisites.md).

    Б. Выполните [предварительные технические требования к виртуальной машине](marketplace-publishing-vm-image-creation-prerequisites.md).

    c. Выполните [предварительные технические требования для шаблона решения](marketplace-publishing-solution-template-creation-prerequisites.md).

2. Создайте предложение.

    a. [Создайте предложение виртуальной машины](marketplace-publishing-vm-image-creation.md).

    Б. [Создайте предложение шаблона решения](marketplace-publishing-solution-template-creation.md).

3. [Создайте предложение с маркетинговыми материалами](marketplace-publishing-push-to-staging.md).

4. Протестируйте предложение на этапе промежуточного развертывания.

    a. [Протестируйте свое предложение виртуальной машины на этапе промежуточного развертывания](marketplace-publishing-vm-image-test-in-staging.md).

    Б. [Протестируйте предложение шаблона решения на этапе промежуточного развертывания](marketplace-publishing-solution-template-test-in-staging.md).

5. [Разверните предложение в Azure Marketplace](marketplace-publishing-push-to-production.md).


### <a name="create-and-manage-a-virtual-machine-image"></a>Создание образа виртуальной машины и управление им
Создайте образ виртуальной машины и управляйте им с помощью этих ресурсов:
* [Локальное](marketplace-publishing-vm-image-creation-on-premise.md) создание образа виртуальной машины.
* Создание первой виртуальной машины [Windows на портале Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Создание виртуальной машины [Linux в Azure с помощью портала](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Устранение распространенных неполадок, возникающих в процессе [создания виртуального жесткого диска](marketplace-publishing-vm-image-creation-troubleshooting.md).

## <a name="manage-your-solution"></a>Управление решением
Управляйте решением с помощью следующих ресурсов:
* [Руководство по эксплуатации предложений виртуальных машин в Azure Marketplace](marketplace-publishing-vm-image-post-publishing.md)
* [Обновление нетехнических сведений о предложении или номере SKU](marketplace-publishing-vm-image-post-publishing.md#update-the-nontechnical-details-of-an-offer-or-a-sku)
* [Обновление нетехнических сведений о предложении или номере SKU](marketplace-publishing-vm-image-post-publishing.md#update-the-technical-details-of-a-sku)
* [Добавление нового SKU во внесенное в список предложение](marketplace-publishing-vm-image-post-publishing.md#add-a-new-sku-under-a-listed-offer)
* [Изменение числа дисков данных для внесенного в список номера SKU](marketplace-publishing-vm-image-post-publishing.md#change-the-data-disk-count-for-a-listed-sku)
* [Удаление внесенного в список предложения из Microsoft Azure Marketplace](marketplace-publishing-vm-image-post-publishing.md)
* [Удаление внесенного в список номера SKU из Microsoft Azure Marketplace](marketplace-publishing-vm-image-post-publishing.md#delete-a-listed-sku-from-the-marketplace)
* [Удаление текущей версии внесенного в список номера SKU из Microsoft Azure Marketplace](marketplace-publishing-vm-image-post-publishing.md#delete-the-current-version-of-a-listed-sku-from-the-marketplace)
* [Возврат рабочих значений цен для внесенных в список элементов](marketplace-publishing-vm-image-post-publishing.md#revert-the-listing-price-to-production-values)
* [Возврат рабочих значений для модели выставления счетов](marketplace-publishing-vm-image-post-publishing.md#revert-the-billing-model-to-production-values)
* [Возврат рабочего значения параметра видимости для внесенного в список номера SKU](marketplace-publishing-vm-image-post-publishing.md#revert-the-visibility-setting-of-a-listed-sku-to-the-production-value)

## <a name="additional-resources"></a>Дополнительные ресурсы
[Настройка Azure PowerShell для создания предложения для Azure Marketplace](marketplace-publishing-powershell-setup.md)
