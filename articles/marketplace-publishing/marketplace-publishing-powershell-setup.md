<properties
   pageTitle="Настройка PowerShell для создания виртуальной машины для Marketplace | Microsoft Azure"
   description="Указания по настройке Azure PowerShell и, в качестве дополнения, описание процесса создания образов виртуальных машин для развертывания и продажи в Azure Marketplace."
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
   ms.date="10/08/2015"
   ms.author="hascipio"/>

# Настройка Azure PowerShell для создания предложения для Azure Marketplace
Подробные сведения о том, как настроить PowerShell в Azure, см. в разделе [Как установить и настроить Azure PowerShell](../powershell-install-configure.md). Простой подход заключается в том, чтобы использовать сертификат. Для этого скачивается и импортируется сертификат, необходимый для аутентификации. Чтобы получить необходимый сертификат, используйте командлет *Get-AzurePublishSettingsFile*. При появлении запроса сохраните файл. Чтобы импортировать сертификат в сеанс PowerShell, используйте *Import-AzurePublishSettingsFile*.

Чтобы настроить и сохранить общие параметры подписки Microsoft Azure для сеанса PowerShell, используйте командлеты *Set-AzureSubscription* и *Select-AzureSubscription*.

        Set-AzureSubscription -SubscriptionName “mySubName” -CurrentStorageAccountName “mystorageaccount”
        Select-AzureSubscription -SubscriptionName "mySubName" –Current

Первая команда связывает учетную запись хранения по умолчанию с подпиской (требуется для некоторых операций подготовки виртуальной машины). Вторая делает подписку текущей (для других командлетов).

## См. также
- [Приступая к работе: как опубликовать предложение в Azure Marketplace](marketplace-publishing-getting-started.md)
- [Создание образа виртуальной машины для Marketplace](marketplace-publishing-vm-image-creation.md)

<!---HONumber=Nov15_HO3-->