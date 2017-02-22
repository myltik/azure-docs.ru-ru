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
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 68be118bc40e3a62aad73cb43119f49415f5b6a9


---
# <a name="set-up-azure-powershell-to-create-an-offer-for-the-azure-marketplace"></a>Настройка Azure PowerShell для создания предложения для Azure Marketplace
Подробные сведения о том, как настроить PowerShell в Azure, см. в статье [Приступая к работе с командлетами Azure PowerShell](/powershell/azureps-cmdlets-docs). Простой подход заключается в использовании сертификата. Для этого скачивается и импортируется сертификат, необходимый для проверки подлинности. Чтобы получить необходимый сертификат, используйте командлет **Get-AzurePublishSettingsFile**. Когда появится запрос, сохраните файл. Чтобы импортировать сертификат в сеанс PowerShell, используйте командлет **Import-AzurePublishSettingsFile**.

Чтобы настроить и сохранить общие параметры подписки Microsoft Azure для сеанса PowerShell, используйте командлеты **Set-AzureSubscription** и **Select-AzureSubscription**.

        Set-AzureSubscription -SubscriptionName “mySubName” -CurrentStorageAccountName “mystorageaccount”
        Select-AzureSubscription -SubscriptionName "mySubName" –Current

Первая команда связывает учетную запись хранения по умолчанию с подпиской (требуется для некоторых операций подготовки виртуальной машины).  Вторая делает подписку текущей (для других командлетов).

## <a name="see-also"></a>Дополнительные материалы
* [Приступая к работе: как опубликовать предложение в Azure Marketplace](marketplace-publishing-getting-started.md)
* [Создание образа виртуальной машины для Marketplace](marketplace-publishing-vm-image-creation.md)




<!--HONumber=Dec16_HO2-->


