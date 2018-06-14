---
title: Заметки о выпуске API для хранилища ключей .NET 2.x | Документация Майкрософт
description: Разработчики .NET смогут использовать этот API в коде при работе с хранилищем ключей Azure.
services: key-vault
author: lleonard-msft
manager: mbaldwin
editor: alleonar
ms.assetid: 1cccf21b-5be9-4a49-8145-483b695124ba
ms.service: key-vault
ms.devlang: CSharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/02/2017
ms.author: alleonar
ms.openlocfilehash: a7735f8c1c4332bf2472bc83c0c37baf49019004
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
ms.locfileid: "27909760"
---
# <a name="azure-key-vault-net-20---release-notes-and-migration-guide"></a>Хранилище ключей Azure .NET 2.0. Руководство по миграции и заметки о выпуске
Приведенные ниже сведения помогут перейти к использованию версии 2.0 библиотеки Azure Key Vault для C# и .NET.  Приложения, написанные для предыдущих версий, необходимо обновить для поддержки последней версии.  Эти изменения необходимы для полной поддержки новых и усовершенствованных возможностей, таких как **сертификаты Key Vault**.

## <a name="key-vault-certificates"></a>Сертификаты Key Vault

Сертификаты Key Vault управляют сертификатами x509 и поддерживают следующие функции:  

* Создание сертификатов в процессе создания Key Vault или импорт существующего сертификата. Это применимо как для самозаверяющих сертификатов, так и для сертификатов, созданных центром сертификации (ЦС).
* Безопасное хранение сертификатов x509 и управление их хранилищем без использования закрытого ключа.  
* Определение политик, которые указывают Key Vault, как управлять жизненным циклом сертификатов.  
* Указание контактной информации для событий жизненного цикла, таких как предупреждения о завершении срока действия и уведомления о продлении.  
* Автоматическое продление сертификатов от выбранных издателей (являющихся партнерами поставщиков сертификатов x509 для Key Vault и центров сертификации). *Поддержка сертификатов от альтернативных поставщиков (не являющихся партнерами) и центров сертификации (не поддерживается автоматическое продление).  

## <a name="net-support"></a>Поддержка .NET

* **.NET 4.0** не поддерживается в версии 2.0 библиотеки .NET для хранилища ключей Azure.
* **.NET Framework 4.5.2** поддерживается в версии 2.0 библиотеки .NET для хранилища ключей Azure.
* **.NET Standard 1.4** поддерживается в версии 2.0 библиотеки .NET для хранилища ключей Azure.

## <a name="namespaces"></a>Пространства имен

* Пространство имен **Microsoft.Azure.KeyVault** для **моделей** изменено на **Microsoft.Azure.KeyVault.Models**.
* Пространство имен **Microsoft.Azure.KeyVault.Internal** удалено.
* Для зависимостей пространств имен пакета SDK для Azure: 

    - вместо **Hyak.Common** теперь используется **Microsoft.Rest**;
    - вместо **Hyak.Common.Internals** теперь используется **Microsoft.Rest.Serialization**.

## <a name="type-changes"></a>Изменения в типах

* *Secret* — изменено на *SecretBundle*.
* *Dictionary* — изменено на *IDictionary*.
* *List<T>, string []*  — изменено на *IList<T>*.
* *NextList* — изменено на *NextPageLink*.

## <a name="return-types"></a>Возвращаемые типы

* **KeyList** и **SecretList** теперь возвращают *IPage<T>* вместо *ListKeysResponseMessage*.
* Созданный метод **BackupKeyAsync** теперь возвращает *BackupKeyResult* (содержит *Value* — большой двоичный объект для резервного копирования). Ранее метод был внутренним и возвращал только значение.

## <a name="exceptions"></a>Исключения

* *KeyVaultClientException* — изменено на *KeyVaultErrorException*.
* Ошибка службы *exception.Error* изменена на *exception.Body.Error.Message*.
* Из сообщения об ошибке для **[JsonExtensionData]** удалены дополнительные сведения.

## <a name="constructors"></a>Конструкторы

* В качестве аргумента конструктор принимает только *HttpClientHandler* или *[DelegatingHandler]* вместо *HttpClient*.

## <a name="downloaded-packages"></a>Скачанные пакеты

Когда клиент обрабатывает зависимость Key Vault, скачиваются приведенные ниже пакеты.

### <a name="previous-package-list"></a>Предыдущий список пакетов

* `package id="Hyak.Common" version="1.0.2" targetFramework="net45"`
* `package id="Microsoft.Azure.Common" version="2.0.4" targetFramework="net45"`
* `package id="Microsoft.Azure.Common.Dependencies" version="1.0.0" targetFramework="net45"`
* `package id="Microsoft.Azure.KeyVault" version="1.0.0" targetFramework="net45"`
* `package id="Microsoft.Bcl" version="1.1.9" targetFramework="net45"`
* `package id="Microsoft.Bcl.Async" version="1.0.168" targetFramework="net45"`
* `package id="Microsoft.Bcl.Build" version="1.0.14" targetFramework="net45"`
* `package id="Microsoft.Net.Http" version="2.2.22" targetFramework="net45"`

### <a name="current-package-list"></a>Текущий список пакетов

* `package id="Microsoft.Azure.KeyVault" version="2.0.0-preview" targetFramework="net45"`
* `package id="Microsoft.Rest.ClientRuntime" version="2.2.0" targetFramework="net45"`
* `package id="Microsoft.Rest.ClientRuntime.Azure" version="3.2.0" targetFramework="net45"`

## <a name="class-changes"></a>Изменения в классах

* Класс **UnixEpoch** удален.
* Класс **Base64UrlConverter** переименован в **Base64UrlJsonConverter**.

## <a name="other-changes"></a>Другие изменения

* В этой версии API можно настраивать политику повтора операции KV при временных сбоях.

## <a name="microsoftazuremanagementkeyvault-nuget"></a>Microsoft.Azure.Management.KeyVault NuGet

* Для операций, которые возвращали *vault*, возвращаемый тип был представлен классом, который содержал свойство **Vault**. Теперь возвращаемый тип — *Vault*.
* *PermissionsToKeys* и *PermissionsToSecrets* — изменено на *Permissions.Keys* и *Permissions.Secrets*.
* Определенные изменения типов возвращаемых значений применяются также на уровне управления.

## <a name="microsoftazurekeyvaultextensions-nuget"></a>Пакет NuGet Microsoft.Azure.KeyVault.Extensions

* Пакет разбит на **Microsoft.Azure.KeyVault.Extensions** и **Microsoft.Azure.KeyVault.Cryptography** для шифрования.

