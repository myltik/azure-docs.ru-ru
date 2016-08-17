<properties
	pageTitle="Настройка хранилища ключей для виртуальных машин в Azure Resource Manager | Microsoft Azure"
	description="Узнайте, как настроить хранилище ключей для использования с виртуальной машиной Azure Resource Manager."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="singhkays"
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

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] Классическая модель развертывания

В стеке Azure Resource Manager секреты или сертификаты моделируются как ресурсы, которые предоставляются поставщиком ресурсов хранилища ключей. Чтобы больше узнать о хранилище ключей, ознакомьтесь с разделом [Что такое хранилище ключей Azure?](../key-vault/key-vault-whatis.md)

>[AZURE.NOTE] 
>
>1. Чтобы хранилище ключей можно было использовать для виртуальных машин Azure Resource Manager, свойству **EnabledForDeployment** хранилища ключей должно быть присвоено значение true. Это можно сделать на различных клиентах.
>
>2. Хранилище ключей должно быть создано в тех же подписке и расположении, что и виртуальная машина.

## Использование PowerShell для настройки хранилища ключей
Сведения о создании хранилища ключей с помощью PowerShell см. в разделе [Приступая к работе с хранилищем ключей Azure](../key-vault/key-vault-get-started.md#vault).

Для новых хранилищ ключей можно использовать этот командлет PowerShell:

	New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment

Для существующих хранилищ ключей можно использовать этот командлет PowerShell:

	Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

## Использование интерфейса командной строки для настройки хранилища ключей
Сведения об использовании интерфейса командной строки (CLI) для создания хранилища ключей см. в статье [Управление хранилищем ключей с помощью CLI](../key-vault/key-vault-manage-with-cli.md#create-a-key-vault).

Чтобы использовать интерфейс командной строки, необходимо сначала создать хранилище ключей, а затем назначить политику развертывания. Это можно сделать с помощью следующей команды:

	azure keyvault set-policy ContosoKeyVault –enabled-for-deployment true

## Использование шаблонов для настройки хранилища ключей
При использовании шаблона свойству `enabledForDeployment` ресурса хранилища ключей нужно присвоить значение `true`.

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

Сведения о других параметрах, которые можно настроить при создании хранилища ключей с помощью шаблонов, см. в разделе [Создание хранилища ключей](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).

<!---HONumber=AcomDC_0803_2016-->