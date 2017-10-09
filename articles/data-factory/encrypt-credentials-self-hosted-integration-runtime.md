---
title: "Шифрование учетных данных в фабрике данных Azure | Документация Майкрософт"
description: "Узнайте, как зашифровать и сохранить учетные данные для локальных хранилищ данных на компьютере с локальной средой выполнения интеграции."
services: data-factory
documentationcenter: 
author: nabhishek
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: abnarain
ms.translationtype: HT
ms.sourcegitcommit: a6bba6b3b924564fe7ae16fa1265dd4d93bd6b94
ms.openlocfilehash: 8363187b7c8492e9386715324f87e70d7d8615af
ms.contentlocale: ru-ru
ms.lasthandoff: 09/28/2017

---

# <a name="encrypt-credentials-for-on-premises-data-stores-in-azure-data-factory"></a>Шифрование учетных данных для локальных хранилищ данных в фабрике данных Azure
Вы можете зашифровать и сохранить учетные данные для хранилищ данных в локальной среде (связанные службы с конфиденциальной информацией) на компьютере с локальной средой выполнения интеграции. 

Передайте файл определения JSON с учетными данными в <br/>командлет [**New-AzureRmDataFactoryV2LinkedServiceEncryptCredential**](https://docs.microsoft.com/powershell/module/azurerm.datafactoryv2/New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential?view=azurermps-4.4.0) для создания выходного файла определения JSON с зашифрованными учетными данными. Затем с помощью обновленного определения JSON создайте связанные службы.

## <a name="author-sql-server-linked-service"></a>Создание связанной службы SQL Server
В любой папке создайте JSON-файл с именем **SqlServerLinkedService.json** со следующим содержимым:  

Прежде чем сохранить файл, замените `<servername>`, `<databasename>`, `<username>` и `<password>` значениями своего сервера SQL Server. Кроме того, замените `<integration runtime name>` именем своей среды выполнения интеграции. 

```json
{
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
            }
        },
        "connectVia": {
            "type": "integrationRuntimeReference",
            "referenceName": "<integration runtime name>"
        },
        "name": "SqlServerLinkedService"
    }
}
```

## <a name="encrypt-credentials"></a>Шифрование учетных данных
Чтобы зашифровать конфиденциальные данные в полезных данных JSON в локальной среде выполнения интеграции, можно выполнить командлет **New-AzureRmDataFactoryV2LinkedServiceEncryptCredential** и передать полезные данные JSON. Этот командлет гарантирует, что учетные данные шифруются с помощью API защиты данных и сохраняются локально на узле локальной среда выполнения интеграции. Полезные выходные данные можно перенаправить в другой JSON-файл (в этом случае encryptedLinkedService.json), содержащий зашифрованные учетные данные.

```powershell
New-AzureRmDataFactoryV2LinkedServiceEncryptCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "SqlServerLinkedService" -DefinitionFile ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
```

## <a name="use-the-json-with-encrypted-credentials"></a>Использование JSON с зашифрованными учетными данными
Теперь используйте выходной файл JSON, содержащий зашифрованные учетные данные, из предыдущей команды для настройки **SqlServerLinkedService**.

```powershell
Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -DefinitionFile ".\encryptedSqlServerLinkedService.json" 
```

## <a name="next-steps"></a>Дальнейшие действия
Сведения о рекомендациях по безопасному перемещению данных см. в статье [Azure Data Factory — Security considerations for data movement](data-movement-security-considerations.md) (Вопросы безопасности при перемещении данных в фабрике данных Azure).


