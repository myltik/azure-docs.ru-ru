<properties
	pageTitle="Ведение журнала хранилища ключей Azure | Microsoft Azure"
	description="Это руководство поможет вам приступить к работе с журналами хранилища ключей Azure."
	services="key-vault"
	documentationCenter=""
	authors="cabailey"
	manager="mbaldwin"
	tags="azure-resource-manager"/>

<tags
	ms.service="key-vault"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="05/06/2016"
	ms.author="cabailey"/>

# Ведение журнала хранилища ключей Azure #
Хранилище ключей Azure доступно в большинстве регионов. Дополнительные сведения см. на странице [Цены на хранилище ключей](https://azure.microsoft.com/pricing/details/key-vault/).

## Введение  
Создав одно или несколько хранилищ ключей вы, вероятно, захотите знать, кто, как и когда осуществлял доступ к этим хранилищам. Это можно сделать с помощью функции ведения журнала хранилища ключей, которая сохраняет информацию в указанной учетной записи хранения Azure. Для указанной учетной записи автоматически создается новый контейнер с именем **insights-logs-auditevent**. Эту же учетную запись можно использовать для сбора журналов нескольких хранилищ ключей.

Регистрируемые в журналах сведения доступны не позже, чем через 10 минут после выполнения операции с хранилищем ключей. В большинстве случаев это будет еще быстрее. Способ управления журналами в своей учетной записи хранения вы выбираете сами.

- Используйте стандартные методы контроля доступа, предоставляемые Azure, для защиты журналов путем ограничения доступа к ним.
- Удаляйте журналы, которые больше не нужно хранить в учетной записи хранения.

Это руководство поможет вам приступить к работе с журналами хранилища ключей Azure. В нем описывается создание учетной записи хранения, включение функции ведения журналов, а также интерпретация собранных зарегистрированных сведений.


>[AZURE.NOTE]  В этом руководстве нет инструкций по созданию хранилищ ключей, ключей и секретов. Соответствующие сведения см. в статье [Приступая к работе с хранилищем ключей Azure](key-vault-get-started.md). Инструкции по кроссплатформенному интерфейсу командной строки см. в [этом учебнике](key-vault-manage-with-cli.md).
>
>В настоящее время нельзя настроить хранилище ключей Azure на портале Azure. Вместо этого используйте эти указания по работе с Azure PowerShell.

Общую информацию о хранилище ключей Azure см. в статье [Что такое хранилище ключей Azure?](key-vault-whatis.md)

## Предварительные требования

Для работы с этим учебником требуется:

- Существующее хранилище ключей, которое вы используете.
- Azure PowerShell, **начиная с версии 1.0.1**. Чтобы установить решение Azure PowerShell и связать его с подпиской Azure, см. статью [Как установить и настроить Azure PowerShell](../powershell-install-configure.md). Если средство Azure PowerShell у вас установлено, но вы не знаете его версию, в консоли Azure PowerShell введите `(Get-Module azure -ListAvailable).Version`.
- Достаточный объем хранилища в Azure для журналов хранилищ ключей.


## <a id="connect"></a>Подключение к подпискам ##

Запустите сеанс Azure PowerShell и войдите в учетную запись Azure, используя следующую команду:

    Login-AzureRmAccount 

Во всплывающем окне браузера введите имя пользователя и пароль учетной записи Azure. Azure PowerShell получит все подписки, связанные с этой учетной записью, и по умолчанию будет использовать первую из них.

Если у вас есть несколько подписок, возможно, вам нужно будет указать ту, которая использовалась для создания хранилища ключей. Чтобы увидеть подписки для своей учетной записи, введите следующую команду.

    Get-AzureRmSubscription

Затем укажите подписку, связанную с хранилищем ключей, данные которого будут регистрироваться. Для этого введите следующую команду.

    Set-AzureRmContext -SubscriptionId <subscription ID>

Дополнительную информацию о настройке Azure PowerShell см. в статье [Как установить и настроить Azure PowerShell](../powershell-install-configure.md).


## <a id="storage"></a>Создание учетной записи хранения для журналов ##

Хотя вы можете использовать и существующую учетную запись хранения для журналов, мы создадим новую учетную запись, которая будет использоваться для сбора данных хранилища ключей. Эти сведения нам нужно будет указать позже. Сейчас же для удобства работы мы сохраним их в переменной с именем **sa**.

Чтобы упростить управление, мы также будем использовать ту же группу ресурсов, которая содержит наше хранилище ключей. Согласно инструкциям из руководства [Приступая к работе с хранилищем ключей Azure](key-vault-get-started.md) эта группа ресурсов называется **ContosoResourceGroup**. Также мы будем продолжать использовать расположение «Восточная Азия». Замените эти значения собственными.

	$sa = New-AzureRmStorageAccount -ResourceGroupName ContosoResourceGroup -Name ContosoKeyVaultLogs -Type Standard_LRS -Location 'East Asia'


>[AZURE.NOTE]  Если вы планируете использовать существующую учетную запись хранения, для нее должна использоваться та же подписка, что и для хранилища ключей. Кроме того, она должна быть создана с помощью модели развертывания с использованием диспетчера ресурсов, а не классической модели развертывания.

## <a id="identify"></a>Определение хранилища ключей для журналов ##

В руководстве по началу работы мы присвоили хранилищу ключей имя **ContosoKeyVault**. Мы продолжим использовать это имя, сохранив данные в переменной с именем **kv**.

	$kv = Get-AzureRmKeyVault -VaultName 'ContosoKeyVault'


## <a id="enable"></a>Включение ведения журналов ##

Мы включим ведение журналов хранилища ключей с помощью командлета Set-AzureRmDiagnosticSetting и переменных, которые мы создали для новой учетной записи хранения и хранилища ключей. Мы также добавим флаг **-Enabled** для параметра **$true** и зададим категорию AuditEvent (единственная категория для ведения журнала хранилища ключей).

   
	Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent


Результат будет выглядеть так:

**Журналы**

**Enabled : True**

**Category : AuditEvent**

Это подтверждает включение регистрации данных для хранилища ключей и сохранение этих данных в учетной записи хранения.

Регистрируются следующие данные:

- все прошедшие проверку подлинности запросы REST API, включая запросы, ставшие неудачными из-за определенных разрешений на доступ, системных ошибок или неправильных запросов;
- операции с хранилищем ключей, включая создание, удаление и настройку политик доступа к нему, а также обновление таких его атрибутов, как теги;
- операции с ключами и секретами в хранилище ключей, включая создание, изменение или удаление этих ключей и секретов; операции подписания, проверки, шифрования, расшифровки, упаковки и распаковки ключей; операции получения секретов, списка ключей, а также секретов и их версий.
- непроверенные запросы, которые приводят к появлению ответа 401 (например, запросы без токена носителя, с недействительным токеном, а также запросы неправильного формата или просроченные запросы).


## <a id="access"></a>Доступ к журналам ##

Журналы хранилища ключей хранятся в контейнере **insights-logs-auditevent** в указанной вами учетной записи хранения. Чтобы получить список всех больших двоичных объектов (BLOB-объектов) в этом контейнере, введите следующее.

    Get-AzureStorageBlob -Container 'insights-logs-auditevent' -Context $sa.Context

Вы получите приблизительно такой результат:

**Container Uri: https://contosokeyvaultlogs.blob.core.windows.net/insights-logs-auditevent**


**Имя**

**----**

**resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=05/h=01/m=00/PT1H.json**

**resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=02/m=00/PT1H.json**

**resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=18/m=00/PT1H.json****
 

Как видно из этих выходных данных, для BLOB-объектов используется следующее соглашение об именовании: **resourceId=<идентификатор ресурса ARM>/y=<год>/m=<месяц>/d=<день месяца>/h=<час>/m=<минута>/filename.json**

Для значений даты и времени используется время в формате UTC.

Поскольку ту же учетную запись можно использовать при сборе журналов для нескольких ресурсов, для доступа к нужным BLOB-объектам (или для их загрузки) рекомендуется использовать полный идентификатор ресурса в имени BLOB-объекта. Но сначала мы рассмотрим загрузку всех BLOB-объектов.

Во-первых, создайте папку для загрузки BLOB-объектов. Например:

	New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force

Затем выведите список всех BLOB-объектов.

	$blobs = Get-AzureStorageBlob -Container $container -Context $sa.Context

Передайте этот список с помощью команды Get-AzureStorageBlobContent, чтобы загрузить BLOB-объекты в папку назначения.

	$blobs | Get-AzureStorageBlobContent -Destination 'C:\Users\username\ContosoKeyVaultLogs'

При выполнении второй команды разделитель **/** в именах BLOB-объектов используется для создания полной структуры папки в конечной папке. Эта структура будет использоваться для загрузки и хранения BLOB-объектов в виде файлов.

Для выборочной загрузки BLOB-объектов используйте подстановочные знаки. Например:

- Если у вас есть несколько хранилищ ключей, но вы хотите загрузить журналы только для одного хранилища с именем CONTOSOKEYVAULT3.

		Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3

- Если у вас есть несколько групп ресурсов, но вы хотите загрузить журналы только для одной группы, используйте `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`.

		Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'

- Если вы хотите загрузить все журналы за январь 2016 г., используйте `-Blob '*/year=2016/m=01/*'`.

		Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'

Теперь можно переходить к анализу содержимого журналов. Но перед этим мы рассмотрим еще два дополнительных параметра для команды Get-AzureRmDiagnosticSetting:

- Запрос состояния параметров диагностики для ресурса хранилища ключей: `Get-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId`.
 
- Отключение ведения журнала для ресурса хранилища ключей: `Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories AuditEvent`.


## <a id="interpret"></a>Интерпретация журналов хранилища ключей ##

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


| Имя поля | Описание |
| ------------- |-------------|
| time | Дата и время (в формате UTC).|
| resourceId | Идентификатор ресурса диспетчера ресурсов Azure. Для журналов хранилища ключей это всегда идентификатор ресурса хранилища ключей.|
| operationName | Имя операции, как описано в следующей таблице.|
| operationVersion | Запрошенная клиентом версия REST API.|
| category | Для журналов хранилища ключей единственным доступным значением является AuditEvent.|
| resultType | Результат запроса REST API.|
| resultSignature | Состояние HTTP.|
| resultDescription | Дополнительное описание результата, если доступно.|
| durationMs | Время обслуживания запроса REST API в миллисекундах. Сюда не входит задержка сети, поэтому время, зарегистрированное на стороне клиента, может не соответствовать этому значению.|
| callerIpAddress | IP-адрес клиента, отправившего запрос.|
| correlationId | Необязательный GUID, который клиент может передавать для сопоставления журналов на стороне клиента с журналами на стороне службы (хранилища ключей).|
| identity | Удостоверение из маркера, предоставляемое при выполнении запроса REST API. Обычно это «пользователь», «субъект-служба» или комбинация «пользователь + идентификатор приложения» при запросе с помощью командлета Azure PowerShell.|
| properties | Это поле будет содержать разные сведения об операции (operationName). В большинстве случаев оно содержит сведения о клиенте (передаваемая клиентом строка useragent), точный URI запроса REST API и код состояния HTTP. Кроме того, когда объект возвращается в результате запроса (например, KeyCreate или VaultGet), это поле будет содержать URI ключа (как id), URI хранилища или URI секрета.|

 


Значения поля **operationName** отображаются в формате ObjectVerb. Например:

- Все операции с хранилищем ключей отображаются в формате Vault`<action>`, например `VaultGet` и `VaultCreate`.

- Все операции с ключами отображаются в формате Key`<action>`, например `KeySign` и `KeyList`.

- Все операции с секретами отображаются в формате Secret`<action>`, например `SecretGet` и `SecretListVersions`.

В следующей таблице перечислены значения operationName и соответствующие команды REST API.

| operationName | Команда API REST |
| ------------- |-------------|
| Authentication | Через конечную точку Azure Active Directory|
| VaultGet | [Get information about a key vault (Получение сведений о хранилище ключей)](https://msdn.microsoft.com/ru-RU/library/azure/mt620026.aspx)|
| VaultPut | [Create or update a key vault (Создание или обновление хранилища ключей)](https://msdn.microsoft.com/ru-RU/library/azure/mt620025.aspx)|
| VaultDelete | [Delete a key vault (Удаление хранилища ключей)](https://msdn.microsoft.com/ru-RU/library/azure/mt620022.aspx)|
| VaultPatch | [Update a key vault (Создание или обновление хранилища ключей)](https://msdn.microsoft.com/library/azure/mt620025.aspx)|
| VaultList | [List all key vaults in a resource group (Список всех хранилищ ключей в группе ресурсов)](https://msdn.microsoft.com/ru-RU/library/azure/mt620027.aspx)|
| KeyCreate | [Create a key (Создание ключа)](https://msdn.microsoft.com/ru-RU/library/azure/dn903634.aspx)|
| KeyGet | [Получение сведений о ключе](https://msdn.microsoft.com/ru-RU/library/azure/dn878080.aspx)|
| KeyImport | [Import a key into a vault (Импорт ключа в хранилище)](https://msdn.microsoft.com/ru-RU/library/azure/dn903626.aspx)|
| KeyBackup | [Создание резервной копии ключа](https://msdn.microsoft.com/ru-RU/library/azure/dn878058.aspx)|
| KeyDelete | [Delete a key (Удаление ключа)](https://msdn.microsoft.com/ru-RU/library/azure/dn903611.aspx)|
| KeyRestore | [Восстановление ключа](https://msdn.microsoft.com/ru-RU/library/azure/dn878106.aspx)|
| KeySign | [Sign with a key (Вход с помощью ключа)](https://msdn.microsoft.com/ru-RU/library/azure/dn878096.aspx)|
| KeyVerify | [Проверка с помощью ключа](https://msdn.microsoft.com/ru-RU/library/azure/dn878082.aspx)|
| KeyWrap | [Перенос ключа](https://msdn.microsoft.com/ru-RU/library/azure/dn878066.aspx)|
| KeyUnwrap | [Развертывание ключа](https://msdn.microsoft.com/ru-RU/library/azure/dn878079.aspx)|
| KeyEncrypt | [Шифрование с помощью ключа](https://msdn.microsoft.com/ru-RU/library/azure/dn878060.aspx)|
| KeyDecrypt | [Расшифровка с ключом](https://msdn.microsoft.com/ru-RU/library/azure/dn878097.aspx)|
| KeyUpdate | [Update a key (Обновление ключа)](https://msdn.microsoft.com/ru-RU/library/azure/dn903616.aspx)|
| KeyList | [Перечисление ключей в хранилище](https://msdn.microsoft.com/ru-RU/library/azure/dn903629.aspx)|
| KeyListVersions | [List the versions of a key (Перечисление версий ключа)](https://msdn.microsoft.com/ru-RU/library/azure/dn986822.aspx)|
| SecretSet | [Create a secret (Создание секрета)](https://msdn.microsoft.com/ru-RU/library/azure/dn903618.aspx)|
| SecretGet | [Получение сведений о секрете](https://msdn.microsoft.com/ru-RU/library/azure/dn903633.aspx)|
| SecretUpdate | [Update a secret (Обновление секрета)](https://msdn.microsoft.com/ru-RU/library/azure/dn986818.aspx)|
| SecretDelete | [Удаление секрета](https://msdn.microsoft.com/ru-RU/library/azure/dn903613.aspx)|
| SecretList | [Список секретов в хранилище](https://msdn.microsoft.com/ru-RU/library/azure/dn903614.aspx)|
| SecretListVersions | [List versions of a secret (Список версий секрета)](https://msdn.microsoft.com/ru-RU/library/azure/dn986824.aspx)|




## <a id="next"></a>Дальнейшие действия ##

Руководство по использованию хранилища ключей Azure в веб-приложении см. в статье [Использование хранилища ключей Azure из веб-приложения](key-vault-use-from-web-application.md).

Справочные материалы по программированию см. в [руководстве разработчика для хранилища ключей Azure](key-vault-developers-guide.md).

Полный список командлетов Azure PowerShell 1.0 для хранилища ключей Azure см. в статье [Azure Key Vault Cmdlets](https://msdn.microsoft.com/library/azure/dn868052.aspx) (Командлеты хранилища ключей Azure).

Руководство по смене ключей и аудиту журналов с помощью хранилища ключей Azure см. в статье [How to setup Key Vault with end to end key rotation and auditing](key-vault-key-rotation-log-monitoring.md) (Как настроить в хранилище ключей полную смену ключей и аудит).

<!---HONumber=AcomDC_0713_2016-->