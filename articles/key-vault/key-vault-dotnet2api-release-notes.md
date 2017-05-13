---
title: "Заметки о выпуске API для хранилища ключей .NET 2.x | Документация Майкрософт"
description: "Разработчики .NET смогут использовать этот API в коде при работе с хранилищем ключей Azure."
services: key-vault
author: BrucePerlerMS
manager: mbaldwin
editor: bruceper
ms.assetid: 1cccf21b-5be9-4a49-8145-483b695124ba
ms.service: key-vault
ms.devlang: CSharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/02/2017
ms.author: bruceper
ms.translationtype: Human Translation
ms.sourcegitcommit: db034a8151495fbb431f3f6969c08cb3677daa3e
ms.openlocfilehash: 21b34e30a7e07b37ccae3f101f2ab4dfadfad9bf
ms.contentlocale: ru-ru
ms.lasthandoff: 04/29/2017


---
# <a name="azure-key-vault-net-20---release-notes-and-migration-guide"></a>Хранилище ключей Azure .NET 2.0. Руководство по миграции и заметки о выпуске
Следующие примечания и инструкции предназначены для разработчиков, использующих библиотеки .NET и C# при работе с Azure Key Vault. Переходная версия между 1.0 и 2.0 включает ряд таких обновлений, как реализация поддержки **сертификатов хранилища ключей**. Чтобы воспользоваться новыми возможностями и улучшениями, необходимо программным образом выполнить перенос.

## <a name="key-vault-certificates"></a>Сертификаты Key Vault

Поддержка сертификатов хранилища ключей не только позволяет управлять сертификатами x509, но и предоставляет следующие возможности.  

* Владелец сертификата может создать сертификат при создании хранилища ключей или импорте существующего сертификата. Это применимо как для самозаверяющих сертификатов, так и для сертификатов, созданных центром сертификации.
* Владелец сертификата хранилища ключей может безопасно хранить сертификаты X509 и управлять ими без необходимости использовать закрытый ключ.  
* Владелец сертификата может создать политику, которая определяет для хранилища ключей параметры управления жизненным циклом сертификата.  
* Владелец сертификата может предоставить контактные данные для получения уведомлений о таких событиях жизненного цикла, как истечение срока действия и обновление сертификата.  
* Автоматическое обновление с возможностью выбора издателей сертификатов хранилища ключей — партнерских поставщиков сертификатов X509 и центров сертификации.
  
  * ПРИМЕЧАНИЕ. Также разрешено использовать сертификаты, которые предоставляются поставщиками и центрами, не являющимися партнерами. Но в таком случае функция автоматического обновления недоступна.

## <a name="net-support"></a>Поддержка .NET

* **.NET 4.0** не поддерживается в версии 2.0 библиотеки .NET и C# для хранилища ключей Azure.
* **.NET Core** поддерживается в версии 2.0 библиотеки .NET и C# для хранилища ключей Azure.

## <a name="namespaces"></a>Пространства имен

* Пространство имен **Microsoft.Azure.KeyVault** для **моделей** изменено на **Microsoft.Azure.KeyVault.Models**.
* Пространство имен **Microsoft.Azure.KeyVault.Internal** удалено.
* Пространство имен **Hyak.Common** и **Hyak.Common.Internals** зависимостей пакета SDK для Azure изменено на **Microsoft.Rest** и **Microsoft.Rest.Serialization** соответственно.

## <a name="type-changes"></a>Изменения в типах

* *Secret* — изменено на *SecretBundle*.
* *Dictionary* — изменено на *IDictionary*.
* *List<T>, string []* — изменено на *IList<T>*.
* *NextList* — изменено на *NextPageLink*.

## <a name="return-types"></a>Возвращаемые типы

* **KeyList** и **SecretList** возвращают *IPage<T>* вместо *ListKeysResponseMessage*.
* Созданный метод **BackupKeyAsync** возвращает *BackupKeyResult* (содержит *Value* — большой двоичный объект для резервного копирования). Ранее метод был внутренним и возвращал только значение.

## <a name="exceptions"></a>Исключения

* *KeyVaultClientException* — изменено на *KeyVaultErrorException*.
* Ошибка службы *exception.Error* изменена на *exception.Body.Error.Message*.
* Из сообщения об ошибке для **[JsonExtensionData]** удалены дополнительные сведения.

## <a name="constructors"></a>Конструкторы

* В качестве аргумента конструктор принимает только *HttpClientHandler* или *[DelegatingHandler]* вместо *HttpClient*.

## <a name="downloaded-packages"></a>Скачанные пакеты

При обработке клиентом зависимости в хранилище ключей раньше скачивались следующие пакеты.

### <a name="previous-package-list"></a>Предыдущий список пакетов

* package id="Hyak.Common" version="1.0.2" targetFramework="net45"
* package id="Microsoft.Azure.Common" version="2.0.4" targetFramework="net45"
* package id="Microsoft.Azure.Common.Dependencies" version="1.0.0" targetFramework="net45"
* package id="Microsoft.Azure.KeyVault" version="1.0.0" targetFramework="net45"
* package id="Microsoft.Bcl" version="1.1.9" targetFramework="net45"
* package id="Microsoft.Bcl.Async" version="1.0.168" targetFramework="net45"
* package id="Microsoft.Bcl.Build" version="1.0.14" targetFramework="net45"
* package id="Microsoft.Net.Http" version="2.2.22" targetFramework="net45"

### <a name="current-package-list"></a>Текущий список пакетов

* package id="Microsoft.Azure.KeyVault" version="2.0.0-preview" targetFramework="net45"
* package id="Microsoft.Rest.ClientRuntime" version="2.2.0" targetFramework="net45"
* package id="Microsoft.Rest.ClientRuntime.Azure" version="3.2.0" targetFramework="net45"

## <a name="class-changes"></a>Изменения в классах

* Класс **UnixEpoch** удален.
* Класс **Base64UrlConverter** переименован в **Base64UrlJsonConverter**.

## <a name="other-changes"></a>Другие изменения

* В этой версии API можно настраивать политику повтора операции KV при временных сбоях.

## <a name="microsoftazuremanagementkeyvault-nuget"></a>Microsoft.Azure.Management.KeyVault NuGet

* Для операций, которые возвращали *vault*, возвращаемый тип был представлен классом, который содержал свойство Vault. Теперь возвращаемый тип — *Vault*.
* *PermissionsToKeys* и *PermissionsToSecrets* — изменено на *Permissions.Keys* и *Permissions.Secrets*.
* Некоторые изменения возвращаемых типов применяются также на уровне управления.

## <a name="microsoftazurekeyvaultextensions-nuget"></a>Пакет NuGet Microsoft.Azure.KeyVault.Extensions

* Пакет разбит на **Microsoft.Azure.KeyVault.Extensions** и **Microsoft.Azure.KeyVault.Cryptography** для шифрования.


