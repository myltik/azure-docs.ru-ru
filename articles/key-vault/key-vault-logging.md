---
title: Ведение журнала хранилища ключей Azure | Документация Майкрософт
description: Это руководство поможет вам приступить к работе с журналами хранилища ключей Azure.
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 43f96a2b-3af8-4adc-9344-bc6041fface8
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 10/16/2017
ms.author: barclayn
ms.openlocfilehash: e7dcb3778de31258f4aa3c946ffa214d87cb858a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32178829"
---
# <a name="azure-key-vault-logging"></a>Ведение журнала хранилища ключей Azure
Хранилище ключей Azure доступно в большинстве регионов. Дополнительные сведения см. на странице [цен на хранилище ключей](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="introduction"></a>Введение
Создав одно или несколько хранилищ ключей вы, вероятно, захотите знать, кто, как и когда осуществлял доступ к этим хранилищам. Это можно сделать с помощью функции ведения журнала хранилища ключей, которая сохраняет информацию в указанной учетной записи хранения Azure. Для указанной учетной записи автоматически создается новый контейнер с именем **insights-logs-auditevent**. Эту же учетную запись можно использовать для сбора журналов нескольких хранилищ ключей.

Регистрируемые в журналах сведения доступны не позже, чем через 10 минут после выполнения операции с хранилищем ключей. В большинстве случаев это будет еще быстрее.  Способ управления журналами в своей учетной записи хранения вы выбираете сами.

* Используйте стандартные методы контроля доступа, предоставляемые Azure, для защиты журналов путем ограничения доступа к ним.
* Удаляйте журналы, которые больше не нужно хранить в учетной записи хранения.

Это руководство поможет вам приступить к работе с журналами хранилища ключей Azure. В нем описывается создание учетной записи хранения, включение функции ведения журналов, а также интерпретация собранных зарегистрированных сведений.  

> [!NOTE]
> В этом руководстве нет инструкций по созданию хранилищ ключей, ключей и секретов. Соответствующие сведения см. в статье [Приступая к работе с хранилищем ключей Azure](key-vault-get-started.md). Инструкции по кроссплатформенному интерфейсу командной строки см. в [этом руководстве](key-vault-manage-with-cli2.md).
>
> В настоящее время нельзя настроить хранилище ключей Azure на портале Azure. Вместо этого используйте эти указания по работе с Azure PowerShell.
>
>

Общие сведения о хранилище ключей Azure см. в статье [Что такое хранилище ключей Azure?](key-vault-whatis.md)

## <a name="prerequisites"></a>предварительным требованиям
Для работы с этим учебником требуется:

* Существующее хранилище ключей, которое вы используете.  
* Azure PowerShell, **начиная с версии 1.0.1**. Чтобы установить решение Azure PowerShell и связать его с подпиской Azure, см. статью [Установка и настройка Azure PowerShell](/powershell/azure/overview). Если средство Azure PowerShell у вас установлено, но вы не знаете его версию, в консоли Azure PowerShell введите `(Get-Module azure -ListAvailable).Version`.  
* Достаточный объем хранилища в Azure для журналов хранилищ ключей.

## <a id="connect"></a>Подключение к подпискам
Запустите сеанс Azure PowerShell и войдите в учетную запись Azure, используя следующую команду:  

    Connect-AzureRmAccount

Во всплывающем окне браузера введите имя пользователя и пароль учетной записи Azure. Azure PowerShell получит все подписки, связанные с этой учетной записью, и по умолчанию будет использовать первую из них.

Если у вас есть несколько подписок, возможно, вам нужно будет указать ту, которая использовалась для создания хранилища ключей Azure. Чтобы увидеть подписки для своей учетной записи, введите следующую команду:

    Get-AzureRmSubscription

Затем укажите подписку, связанную с хранилищем ключей, данные которого будут регистрироваться. Для этого введите следующую команду:

    Set-AzureRmContext -SubscriptionId <subscription ID>

> [!NOTE]
> Этот шаг очень важен и особенно полезен, если с учетной записью связано несколько подписок. Если вы пропустите этот шаг, то при регистрации Microsoft.Insights может появиться сообщение об ошибке.
>   
>

Дополнительные сведения о настройке Azure PowerShell см. в статье [Установка и настройка Azure PowerShell](/powershell/azure/overview).

## <a id="storage"></a>Создание учетной записи хранения для журналов
Хотя вы можете использовать и существующую учетную запись хранения для журналов, мы создадим новую учетную запись, которая будет использоваться для сбора данных хранилища ключей. Эти сведения нам нужно будет указать позже. Сейчас же для удобства работы мы сохраним их в переменной с именем **sa**.

Чтобы упростить управление, мы также будем использовать ту же группу ресурсов, которая содержит наше хранилище ключей. Согласно инструкциям из руководства [Приступая к работе с хранилищем ключей Azure](key-vault-get-started.md) эта группа ресурсов называется **ContosoResourceGroup**. Кроме того, мы будем продолжать использовать регион "Восточная Азия". Замените эти значения собственными.

    $sa = New-AzureRmStorageAccount -ResourceGroupName ContosoResourceGroup -Name contosokeyvaultlogs -Type Standard_LRS -Location 'East Asia'


> [!NOTE]
> Если вы планируете использовать существующую учетную запись хранения, для нее должна использоваться та же подписка, что и для хранилища ключей. Кроме того, она должна быть создана с помощью модели развертывания Resource Manager, а не классической модели развертывания.
>
>

## <a id="identify"></a>Определение хранилища ключей для журналов
В руководстве по началу работы мы присвоили хранилищу ключей имя **ContosoKeyVault**. Мы продолжим использовать это имя, сохранив данные в переменной с именем **kv**.

    $kv = Get-AzureRmKeyVault -VaultName 'ContosoKeyVault'


## <a id="enable"></a>Включение ведения журналов
Мы включим ведение журналов хранилища ключей с помощью командлета Set-AzureRmDiagnosticSetting и переменных, которые мы создали для новой учетной записи хранения и хранилища ключей. Мы также установим для флага **-Enabled** значение **$true** и зададим категорию AuditEvent (единственная категория для ведения журнала хранилища ключей):

    Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent

Результат будет выглядеть так:

    StorageAccountId   : /subscriptions/<subscription-GUID>/resourceGroups/ContosoResourceGroup/providers/Microsoft.Storage/storageAccounts/ContosoKeyVaultLogs
    ServiceBusRuleId   :
    StorageAccountName :
        Logs
        Enabled           : True
        Category          : AuditEvent
        RetentionPolicy
        Enabled : False
        Days    : 0


Это подтверждает включение регистрации данных для хранилища ключей и сохранение этих данных в учетной записи хранения.

При необходимости можно также задать политику хранения для журналов, например политику, в соответствии с которой старые журналы будут автоматически удаляться. Например, задайте политику хранения, установив для флага **-RetentionEnabled** значение **$true**, а для параметра **-RetentionInDays** — значение **90**, чтобы автоматически удалять журналы, которые хранятся более 90 дней.

    Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent -RetentionEnabled $true -RetentionInDays 90

Регистрируются следующие данные:

* все прошедшие проверку подлинности запросы REST API, включая запросы, ставшие неудачными из-за определенных разрешений на доступ, системных ошибок или неправильных запросов;
* операции с хранилищем ключей, включая создание, удаление и настройку политик доступа к нему, а также обновление таких его атрибутов, как теги;
* операции с ключами и секретами в хранилище ключей, включая создание, изменение или удаление этих ключей и секретов; операции подписания, проверки, шифрования, расшифровки, упаковки и распаковки ключей; операции получения секретов, списка ключей, а также секретов и их версий.
* непроверенные запросы, которые приводят к появлению ответа 401 (например, запросы без токена носителя, с недействительным токеном, а также запросы неправильного формата или просроченные запросы).  

## <a id="access"></a>Доступ к журналам
Журналы хранилища ключей хранятся в контейнере **insights-logs-auditevent** в указанной вами учетной записи хранения. Чтобы получить список всех больших двоичных объектов (BLOB-объектов) в этом контейнере, введите следующее.

Сначала создайте переменную для имени контейнера. Она будет использоваться для выполнения действий в этом пошаговом руководстве.

    $container = 'insights-logs-auditevent'

Чтобы получить список всех больших двоичных объектов (BLOB-объектов) в этом контейнере, введите следующее.

    Get-AzureStorageBlob -Container $container -Context $sa.Context
Вы получите приблизительно такой результат:

**URI контейнера: https://contosokeyvaultlogs.blob.core.windows.net/insights-logs-auditevent**

**Имя**

- - -
**resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=05/h=01/m=00/PT1H.json**

**resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=02/m=00/PT1H.json**

**resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=18/m=00/PT1H.json****

Как видно из этих выходных данных, имена больших двоичных объектов соответствуют соглашению об именовании: **resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json**.

Для значений даты и времени используется время в формате UTC.

Поскольку ту же учетную запись можно использовать при сборе журналов для нескольких ресурсов, для доступа к нужным BLOB-объектам (или для их загрузки) рекомендуется использовать полный идентификатор ресурса в имени BLOB-объекта. Но сначала мы рассмотрим загрузку всех BLOB-объектов.

Во-первых, создайте папку для загрузки BLOB-объектов. Например: 

    New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force

Затем выведите список всех BLOB-объектов.  

    $blobs = Get-AzureStorageBlob -Container $container -Context $sa.Context

Передайте этот список с помощью команды Get-AzureStorageBlobContent, чтобы загрузить BLOB-объекты в папку назначения.

    $blobs | Get-AzureStorageBlobContent -Destination 'C:\Users\username\ContosoKeyVaultLogs'

При выполнении второй команды разделитель **/** в именах больших двоичных объектов используется для создания полной структуры папки в конечной папке. Эта структура будет использоваться для скачивания и хранения больших двоичных объектов в виде файлов.

Для выборочной загрузки BLOB-объектов используйте подстановочные знаки. Например: 

* Если у вас есть несколько хранилищ ключей, но вы хотите загрузить журналы только для одного хранилища с именем CONTOSOKEYVAULT3.

        Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
* Если у вас есть несколько групп ресурсов, но вы хотите загрузить журналы только для одной из них, используйте `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

        Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
* Если вы хотите загрузить все журналы за январь 2016 г., используйте `-Blob '*/year=2016/m=01/*'`:

        Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'

Теперь можно переходить к анализу содержимого журналов. Но перед этим мы рассмотрим еще два дополнительных параметра для команды Get-AzureRmDiagnosticSetting:

* Запрос состояния параметров диагностики для ресурса хранилища ключей: `Get-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId`
* Отключение ведения журнала для ресурса хранилища ключей: `Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories AuditEvent`

## <a id="interpret"></a>Интерпретация журналов хранилища ключей
Отдельные BLOB-объекты хранятся как текст в формате JSON. Вот пример записи журнала после выполнения команды `Get-AzureRmKeyVault -VaultName 'contosokeyvault'`:

    {
        "records":
        [
            {
                "time": "2016-01-05T01:32:01.2691226Z",
                "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
                "operationName": "VaultGet",
                "operationVersion": "2015-06-01",
                "category": "AuditEvent",
                "resultType": "Success",
                "resultSignature": "OK",
                "resultDescription": "",
                "durationMs": "78",
                "callerIpAddress": "104.40.82.76",
                "correlationId": "",
                "identity": {"claim":{"http://schemas.microsoft.com/identity/claims/objectidentifier":"d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn":"live.com#username@outlook.com","appid":"1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},
                "properties": {"clientInfo":"azure-resource-manager/2.0","requestUri":"https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id":"https://contosokeyvault.vault.azure.net/","httpStatusCode":200}
            }
        ]
    }


В следующей таблице перечислены имена полей и описания.

| Имя поля | ОПИСАНИЕ |
| --- | --- |
| Twitter в режиме реального |Дата и время (в формате UTC). |
| ResourceId |Идентификатор ресурса диспетчера ресурсов Azure. Для журналов хранилища ключей это всегда идентификатор ресурса хранилища ключей. |
| operationName |Имя операции, как описано в следующей таблице. |
| operationVersion |Запрошенная клиентом версия REST API. |
| category |Для журналов хранилища ключей единственным доступным значением является AuditEvent. |
| resultType |Результат запроса REST API. |
| resultSignature |Состояние HTTP. |
| resultDescription |Дополнительное описание результата, если доступно. |
| durationMs |Время обслуживания запроса REST API в миллисекундах. Сюда не входит задержка сети, поэтому время, зарегистрированное на стороне клиента, может не соответствовать этому значению. |
| callerIpAddress |IP-адрес клиента, отправившего запрос. |
| correlationId |Необязательный GUID, который клиент может передавать для сопоставления журналов на стороне клиента с журналами на стороне службы (хранилища ключей). |
| identity |Удостоверение из маркера, предоставляемое при выполнении запроса REST API. Обычно это «пользователь», «субъект-служба» или комбинация «пользователь + идентификатор приложения» при запросе с помощью командлета Azure PowerShell. |
| properties |Это поле будет содержать разные сведения об операции (operationName). В большинстве случаев оно содержит сведения о клиенте (передаваемая клиентом строка useragent), точный URI запроса REST API и код состояния HTTP. Кроме того, когда объект возвращается в результате запроса (например, KeyCreate или VaultGet), это поле будет содержать URI ключа (как id), URI хранилища или URI секрета. |

Значения поля **operationName** отображаются в формате ObjectVerb. Например: 

* Все операции с хранилищем ключей отображаются в формате Vault`<action>`, например `VaultGet` и `VaultCreate`.
* Все операции с ключами отображаются в формате Key`<action>`, например `KeySign` и `KeyList`.
* Все операции с секретами отображаются в формате Secret`<action>`, например `SecretGet` и `SecretListVersions`.

В следующей таблице перечислены значения operationName и соответствующие команды REST API.

| operationName | Команда API REST |
| --- | --- |
| Authentication |Через конечную точку Azure Active Directory |
| VaultGet |[Получение сведений о хранилище ключей](https://msdn.microsoft.com/library/azure/mt620026.aspx) |
| VaultPut |[Создание или обновление хранилища ключей](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| VaultDelete |[Удаление хранилища ключей](https://msdn.microsoft.com/library/azure/mt620022.aspx) |
| VaultPatch |[Update a key vault (Создание или обновление хранилища ключей)](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| VaultList |[Список всех хранилищ ключей в группе ресурсов](https://msdn.microsoft.com/library/azure/mt620027.aspx) |
| KeyCreate |[Создание ключа](https://msdn.microsoft.com/library/azure/dn903634.aspx) |
| KeyGet |[Получение сведений о ключе](https://msdn.microsoft.com/library/azure/dn878080.aspx) |
| KeyImport |[Импорт ключа в хранилище](https://msdn.microsoft.com/library/azure/dn903626.aspx) |
| KeyBackup |[Создание резервной копии ключа](https://msdn.microsoft.com/library/azure/dn878058.aspx) |
| KeyDelete |[Удаление ключа](https://msdn.microsoft.com/library/azure/dn903611.aspx) |
| KeyRestore |[Восстановление ключа](https://msdn.microsoft.com/library/azure/dn878106.aspx) |
| KeySign |[Вход с помощью ключа](https://msdn.microsoft.com/library/azure/dn878096.aspx) |
| KeyVerify |[Проверка с помощью ключа](https://msdn.microsoft.com/library/azure/dn878082.aspx) |
| KeyWrap |[Перенос ключа](https://msdn.microsoft.com/library/azure/dn878066.aspx) |
| KeyUnwrap |[Развертывание ключа](https://msdn.microsoft.com/library/azure/dn878079.aspx) |
| KeyEncrypt |[Шифрование с помощью ключа](https://msdn.microsoft.com/library/azure/dn878060.aspx) |
| KeyDecrypt |[Расшифровка с помощью ключа](https://msdn.microsoft.com/library/azure/dn878097.aspx) |
| KeyUpdate |[Обновление ключа](https://msdn.microsoft.com/library/azure/dn903616.aspx) |
| KeyList |[Перечисление ключей в хранилище](https://msdn.microsoft.com/library/azure/dn903629.aspx) |
| KeyListVersions |[Перечисление версий ключа](https://msdn.microsoft.com/library/azure/dn986822.aspx) |
| SecretSet |[Создание секрета](https://msdn.microsoft.com/library/azure/dn903618.aspx) |
| SecretGet |[Получение сведений о секрете](https://msdn.microsoft.com/library/azure/dn903633.aspx) |
| SecretUpdate |[Обновление секрета](https://msdn.microsoft.com/library/azure/dn986818.aspx) |
| SecretDelete |[Удаление секрета](https://msdn.microsoft.com/library/azure/dn903613.aspx) |
| SecretList |[Список секретов в хранилище](https://msdn.microsoft.com/library/azure/dn903614.aspx) |
| SecretListVersions |[Список версий секрета](https://msdn.microsoft.com/library/azure/dn986824.aspx) |

## <a id="loganalytics"></a>Использование Log Analytics

Решение хранилища ключей Azure в Log Analytics позволяет просматривать журналы AuditEvent хранилища ключей Azure. Дополнительные сведения, включая инструкции по настройке, см. в статье [Решение Azure Key Vault Analytics в Log Analytics](../log-analytics/log-analytics-azure-key-vault.md). В этой статье также содержатся инструкции на случай переноса из старого решения Key Vault, которое предлагалось в предварительной версии Log Analytics, где сначала требовалось направить журналы в учетную запись хранения Azure и настроить Log Analytics для чтения из этой учетной записи.

## <a id="next"></a>Дальнейшие действия
Руководство по использованию хранилища ключей Azure в веб-приложении см. в статье [Использование хранилища ключей Azure из веб-приложения](key-vault-use-from-web-application.md).

Справочные материалы по программированию см. в статье [Руководство разработчика хранилища ключей Azure](key-vault-developers-guide.md).

Полный список командлетов Azure PowerShell 1.0 для хранилища ключей Azure см. в статье [Azure Key Vault Cmdlets](/powershell/module/azurerm.keyvault/#key_vault) (Командлеты хранилища ключей Azure).

Руководство по смене ключей и аудиту журналов с помощью хранилища ключей Azure см. в статье [Как настроить полную смену ключей и аудит в хранилище ключей](key-vault-key-rotation-log-monitoring.md).
