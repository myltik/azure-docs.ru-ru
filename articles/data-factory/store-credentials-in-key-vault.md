---
title: "Хранение учетных данных в Azure Key Vault | Документация Майкрософт"
description: "Узнайте, как хранить учетные данные для хранилищ данных, используемых в Azure Key Vault, которые фабрика данных Azure может автоматически извлекать в среду выполнения."
services: data-factory
author: linda33wj
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: jingwang
ms.translationtype: HT
ms.sourcegitcommit: 469246d6cb64d6aaf995ef3b7c4070f8d24372b1
ms.openlocfilehash: cb4545dcd41fe9a2c73bc86096843b717d61a785
ms.contentlocale: ru-ru
ms.lasthandoff: 09/27/2017

---

# <a name="store-credential-in-azure-key-vault"></a>Хранение учетных данных в Azure Key Vault

Учетные данные для хранилищ данных можно хранить в [Azure Key Vault](../key-vault/key-vault-whatis.md). Фабрика данных Azure извлекает учетные данные при выполнении действия, которое использует хранилище данных.

> [!NOTE]
> В настоящее время только [соединитель Dynamics](connector-dynamics-crm-office-365.md) поддерживает эту функцию.

## <a name="steps"></a>Действия

При создании фабрики данных создается удостоверение службы. Это управляемое приложение, зарегистрированное в Azure Activity Directory и представляющее эту определенную фабрику данных.

- При создании фабрики данных с помощью **портала Azure или PowerShell** удостоверение службы всегда будет создаваться автоматически с момента выпуска общедоступной предварительной версии.
- При создании фабрики данных с помощью пакета **SDK** удостоверение службы будет создаваться, только если в создаваемом объекте фабрики указать "Identity = new FactoryIdentity()". Пример см. в инструкциях по [созданию фабрики данных из краткого руководства по .NET](quickstart-create-data-factory-dot-net.md#create-a-data-factory).
- При создании фабрики данных с помощью **REST API** удостоверение службы будет создано, только если в тексте запроса указан раздел identity. Пример см. в инструкциях по [созданию фабрики данных из краткого руководства по REST](quickstart-create-data-factory-rest-api.md#create-a-data-factory).

Чтобы указать учетные данные, хранимые в Azure Key Vault, необходимо:

1. Скопировать идентификатор приложения удостоверения службы, созданный вместе с фабрикой данных. См. дополнительные сведения о [получении удостоверения службы](#retrieve-service-identity).
2. Предоставьте удостоверению службы доступ к Azure Key Vault. В "хранилище ключей -> Управление доступом -> Добавить" найдите идентификатор приложения удостоверения службы, чтобы добавить разрешения на чтение. Это позволит указанной фабрике получить доступ к секрету в хранилище ключей.
3. Создайте связанную службу, указывающую на Azure Key Vault. Дополнительные сведения см. в разделе [Связанная служба Azure Key Vault](#azure-key-vault-linked-service).
4. Создайте связанную службу хранилища данных, внутри которой указывается соответствующий секрет, хранящийся в хранилище ключей. См. дополнительные сведения об [указании учетных данных, хранимых в хранилище ключей](#reference-credential-stored-in-key-vault).

## <a name="retrieve-service-identity"></a>Получение удостоверения службы

Удостоверение службы можно получить с помощью портала Azure или программных средств. В разделах ниже приведено несколько примеров.

>[!TIP]
> Если удостоверение службы не отображается, [создайте его](#generate-service-identity), обновив фабрику.

### <a name="using-azure-portal"></a>Использование портала Azure

Сведения об удостоверении службы можно найти на портале Azure. Выберите свою фабрику, данных, а затем щелкните "Параметры" -> "Свойства":

- ИДЕНТИФИКАТОР УДОСТОВЕРЕНИЯ СЛУЖБЫ
- КЛИЕНТ УДОСТОВЕРЕНИЯ СЛУЖБЫ
- **Идентификатор приложения удостоверения службы** — скопируйте это значение, чтобы предоставить доступ к Key Vault.

![Получение удостоверения службы](media/store-credentials-in-key-vault/retrieve-service-identity-portal.png)

### <a name="using-powershell"></a>с использованием PowerShell.

Идентификатор субъекта удостоверения службы и идентификатор клиента возвращаются при получении определенной фабрики данных:

```powershell
PS C:\WINDOWS\system32> (Get-AzureRmDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

Скопируйте идентификатор субъекта, а затем запустите команду Azure Active Directory ниже с идентификатором субъекта в качестве параметра, чтобы получить **идентификатор приложения**, используемый для предоставления доступа к Key Vault.

```powershell
PS C:\WINDOWS\system32> Get-AzureRmADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

## <a name="azure-key-vault-linked-service"></a>Связанная служба Azure Key Vault

Для связанной службы Azure Key Vault поддерживаются следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Для свойства type необходимо задать значение **AzureKeyVault**. | Да |
| BaseUrl | Укажите URL-адрес Azure Key Vault. | Да |

**Пример**

```json
{
    "name": "AzureKeyVaultLinkedService",
    "properties": {
        "type": "AzureKeyVault",
        "typeProperties": {
        "baseUrl": "https://<azureKeyVaultName>.vault.azure.net"
        }
    }
}
```

## <a name="reference-credential-stored-in-key-vault"></a>Указание учетных данных, хранимых в хранилище ключей

Следующие свойства поддерживаются при настройке поля в связанной службе, указывающей секрет хранилища ключей:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Для свойства type поля необходимо задать значение **AzureKeyVaultSecret**. | Да |
| secretName | Имя секрета в Azure Key Vault. | Да |
| secretVersion | Версия секрета в Azure Key Vault.<br/>Если не указано, используется последняя версия секрета.<br/>Если указано, то он придерживается указанной версии.| Нет |
| store | Ссылается на связанную службу Azure Key Vault, которая используется для хранения учетных данных. | Да |

**Пример: (ознакомьтесь с разделом "Пароль")**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "<>",
            "organizationName": "<>",
            "authenticationType": "<>",
            "username": "<>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "mySecret",
                "store":{
                    "linkedServiceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

## <a name="generate-service-identity"></a>Создание удостоверения службы

Если при выполнении инструкций по [получению удостоверения службы](#retrieve-service-identity) вы обнаружили, что для фабрики данных соответствующее удостоверение отсутствует, вы можете создать его, обновив фабрику данных программными средствами — с помощью инициатора удостоверений.

> [!NOTE]
> - **Удостоверение службы невозможно изменить**. Обновление фабрики данных, в которой уже есть удостоверение службы, ничего не изменит. Удостоверение службы останется таким же.
> - **Удостоверение службы невозможно удалить**. Если обновить фабрику данных, в которой уже есть удостоверение службы, не указав параметр identity в объекте фабрики либо не указав раздел identity в тексте запроса REST, произойдет ошибка.

В следующих разделах приведено несколько примеров по созданию удостоверения службы для фабрики, если оно отсутствует.

### <a name="using-powershell"></a>с использованием PowerShell.

Вызовите команду **Set-AzureRmDataFactoryV2** еще раз. Будут созданы поля Identity:

```powershell
PS C:\WINDOWS\system32> Set-AzureRmDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName> -Location <region>

DataFactoryName   : ADFV2DemoFactory
DataFactoryId     : /subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory
ResourceGroupName : <resourceGroupName>
Location          : East US
Tags              : {}
Identity          : Microsoft.Azure.Management.DataFactory.Models.FactoryIdentity
ProvisioningState : Succeeded
```

### <a name="using-rest-api"></a>Использование интерфейса REST API

Вызовите API с разделом identity в тексте запроса, как в примере ниже.

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2017-09-01-preview
```

**Текст запроса**: add "identity": { "type": "SystemAssigned" }.

```json
{
    "name": "<dataFactoryName>",
    "location": "<region>",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
```

**Ответ**: удостоверение службы создается автоматически, и раздел identity заполняется соответствующим образом.

```json
{
    "name": "ADFV2DemoFactory",
    "tags": {},
    "properties": {
        "provisioningState": "Succeeded",
        "loggingStorageAccountKey": "**********",
        "createTime": "2017-09-26T04:10:01.1135678Z",
        "version": "2017-09-01-preview"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "765ad4ab-XXXX-XXXX-XXXX-51ed985819dc",
        "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory",
    "type": "Microsoft.DataFactory/factories",
    "location": "EastUS"
}
```

### <a name="using-sdk"></a>Использование пакета SDK

Вызовите функцию create_or_update фабрики данных с Identity=new FactoryIdentity(). Пример кода с использованием .NET:

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="next-steps"></a>Дальнейшие действия
В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md##supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.
