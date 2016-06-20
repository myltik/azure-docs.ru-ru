<properties
	pageTitle="Настройка хранилища ключей для виртуальных машин в Azure Resource Manager | Microsoft Azure"
	description="Узнайте, как настроить хранилище ключей для использования с виртуальной машиной Azure Resource Manager."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="singhkay"
	manager="drewm"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/31/2016"
	ms.author="singhkay"/>

# Настройка хранилища ключей для виртуальных машин в Azure Resource Manager

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Классическая модель развертывания

В стеке Azure Resource Manager секреты или сертификаты моделируются как ресурсы, которые предоставляются поставщиком ресурсов хранилища ключей. Чтобы больше узнать о хранилище ключей, ознакомьтесь с разделом [Что такое хранилище ключей Azure?](../key-vault/key-vault-whatis.md)

## Настройка
Чтобы хранилище ключей можно было использовать для виртуальных машин Azure Resource Manager, для свойства *EnabledForDeployment* хранилища ключей должно быть задано значение true. Это можно сделать в различных клиентах, как показано ниже.

## PowerShell
Сведения о создании хранилища ключей с помощью PowerShell см. в разделе [Приступая к работе с хранилищем ключей Azure](../key-vault/key-vault-get-started.md#vault).

Для новых хранилищ ключей можно использовать этот командлет PowerShell.

	New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment

Для существующих хранилищ ключей можно использовать этот командлет PowerShell.

	Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

## Интерфейс командной строки
Сведения о создании хранилища ключей с помощью интерфейса командной строки см. в разделе [Управление хранилищем ключей с помощью CLI](../key-vault/key-vault-manage-with-cli.md#create-a-key-vault).

Чтобы использовать интерфейс командной строки, необходимо сначала создать хранилище ключей, а затем включить политику развертывания. Это можно сделать с помощью следующей команды.

	azure keyvault set-policy ContosoKeyVault –enabled-for-deployment true

## Шаблоны
При использовании шаблонов для свойства `enabledForDeployment` ресурса хранилища ключей нужно задать значение `true`.

	{
      "type": "Microsoft.KeyVault/vaults",
      "name": "ContosoKeyVault",
      "apiVersion": "2015-06-01",
      "location": "<location-of-key-vault>",
      "properties": {
        "enabledForDeployment": "true",
        ....
        ....
      }
    }

С другими параметрами, которые можно настроить при создании хранилища ключей с помощью шаблонов, вы можете ознакомиться [здесь](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).

<!---HONumber=AcomDC_0608_2016-->