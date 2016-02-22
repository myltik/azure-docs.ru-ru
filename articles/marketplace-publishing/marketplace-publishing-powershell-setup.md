<properties
   pageTitle="Настройка PowerShell для создания виртуальной машины для Marketplace | Microsoft Azure"
   description="Указания по настройке Azure PowerShell и, в качестве дополнения, описание процесса создания образов виртуальных машин для развертывания и продажи в Azure Marketplace"
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
   ms.date="02/04/2016"
   ms.author="hascipio"/>

# Настройка Azure PowerShell для создания предложения для Azure Marketplace
Подробные сведения о том, как настроить PowerShell в Azure, см. в разделе [Установка и настройка Azure PowerShell](../powershell-install-configure.md). Простой подход заключается в использовании сертификата. Для этого скачивается и импортируется сертификат, необходимый для проверки подлинности. Чтобы получить необходимый сертификат, используйте командлет **Get-AzurePublishSettingsFile**. Когда появится запрос, сохраните файл. Чтобы импортировать сертификат в сеанс PowerShell, используйте командлет **Import-AzurePublishSettingsFile**.

Чтобы настроить и сохранить общие параметры подписки Microsoft Azure для сеанса PowerShell, используйте командлеты **Set-AzureSubscription** и **Select-AzureSubscription**.

        Set-AzureSubscription -SubscriptionName “mySubName” -CurrentStorageAccountName “mystorageaccount”
        Select-AzureSubscription -SubscriptionName "mySubName" –Current

Первая команда связывает учетную запись хранения по умолчанию с подпиской (требуется для некоторых операций подготовки виртуальной машины). Вторая делает подписку текущей (для других командлетов).

## См. также
- [Приступая к работе: как опубликовать предложение в Azure Marketplace](marketplace-publishing-getting-started.md)
- [Создание образа виртуальной машины для Marketplace](marketplace-publishing-vm-image-creation.md)

<!---HONumber=AcomDC_0211_2016-->