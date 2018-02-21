---
title: "Настройка PowerShell для создания виртуальной машины для Marketplace | Документация Майкрософт"
description: "Указания по настройке Azure PowerShell и, в качестве дополнения, описание процесса создания образов виртуальных машин для развертывания и продажи в Azure Marketplace"
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: e19d6cda-76df-4e42-be84-c9fe47a636db
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/04/2016
ms.author: hascipio
ms.openlocfilehash: bbcce5093d2bbd5326523063db7d0e565fe4de6d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-azure-powershell-to-create-an-offer-for-the-azure-marketplace"></a>Настройка Azure PowerShell для создания предложения для Azure Marketplace
Подробные сведения о том, как настроить PowerShell в Azure, см. в статье [Приступая к работе с командлетами Azure PowerShell](/powershell/azure/overview). Простой подход заключается в использовании сертификата. Для этого скачивается и импортируется сертификат, необходимый для проверки подлинности. Чтобы получить необходимый сертификат, используйте командлет **Get-AzurePublishSettingsFile**. Когда появится запрос, сохраните файл. Чтобы импортировать сертификат в сеанс PowerShell, используйте командлет **Import-AzurePublishSettingsFile**.

Чтобы настроить и сохранить общие параметры подписки Microsoft Azure для сеанса PowerShell, используйте командлеты **Set-AzureSubscription** и **Select-AzureSubscription**.

        Set-AzureSubscription -SubscriptionName “mySubName” -CurrentStorageAccountName “mystorageaccount”
        Select-AzureSubscription -SubscriptionName "mySubName" –Current

Первая команда связывает учетную запись хранения по умолчанию с подпиской (требуется для некоторых операций подготовки виртуальной машины).  Вторая делает подписку текущей (для других командлетов).

## <a name="see-also"></a>См. также
* [Приступая к работе: как опубликовать предложение в Azure Marketplace](marketplace-publishing-getting-started.md)
* [Создание образа виртуальной машины для Marketplace](marketplace-publishing-vm-image-creation.md)

