<properties
   pageTitle="Руководство разработчика хранилища ключей | Microsoft Azure"
   description="Разработчики могут использовать хранилище ключей Azure для управления криптографическими ключами в среде Microsoft Azure."
   services="key-vault"
   documentationCenter=""
   authors="BrucePerlerMS"
   manager="mbaldwin"
   editor="bruceper" />
<tags
   ms.service="key-vault"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="1/19/2016"
   ms.author="bruceper" />

# Руководство разработчика хранилища ключей Azure

> [AZURE.VIDEO azure-key-vault-developer-quick-start]

Разработчики могут использовать хранилище ключей Azure для управления криптографическими ключами в среде Microsoft Azure. Хранилище ключей поддерживает ряд типов ключей и алгоритмов и может использоваться с аппаратными модулями безопасности (HSM) для ключей, представляющих большую ценность. Кроме того, в хранилище ключей можно надежно хранить секреты, которые представляют объекты октетов ограниченного размера без конкретной семантики. Управление доступом для разных типов объектов осуществляется независимо друг от друга.

При условии успешной авторизации вы можете выполнять следующие действия:

- управление ключами шифрования с помощью операций [Создания](https://msdn.microsoft.com/library/azure/dn903634.aspx), [Импорта](https://msdn.microsoft.com/library/azure/dn903626.aspx), [Обновления](https://msdn.microsoft.com/library/azure/dn903616.aspx), [Удаления](https://msdn.microsoft.com/library/azure/dn903611.aspx) и других;

- управление секретами с помощью операций [Получения](https://msdn.microsoft.com/library/azure/dn903633.aspx), [Обновления](https://msdn.microsoft.com/library/azure/dn986818.aspx), [Удаления](https://msdn.microsoft.com/library/azure/dn903613.aspx) и других;

- использование криптографических ключей с операциями [Входа](https://msdn.microsoft.com/library/azure/dn878096.aspx)/[Проверки](https://msdn.microsoft.com/library/azure/dn878082.aspx), [Обертывания ключа](https://msdn.microsoft.com/library/azure/dn878066.aspx)/[Развертывания ключа](https://msdn.microsoft.com/library/azure/dn878079.aspx) и [Шифрования](https://msdn.microsoft.com/library/azure/dn878060.aspx)/[Расшифрования](https://msdn.microsoft.com/library/azure/dn878097.aspx).

Операции с хранилищами ключей проходят проверку подлинности и авторизацию с использованием Azure Active Directory.

## Программирование для хранилища ключей

Система управления хранилищем ключей для программистов состоит из нескольких интерфейсов, в основе которых лежит REST. [Справочник по REST API хранилища ключей](https://msdn.microsoft.com/library/azure/dn903609.aspx)

|[![.NET](./media/key-vault-developers-guide/net.png)](https://msdn.microsoft.com/library/azure/dn903301.aspx)|[![Node.js](./media/key-vault-developers-guide/nodejs.png)](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest)
|:--:|:--:|
|[Документация к пакету SDK для .NET](https://msdn.microsoft.com/library/azure/dn903301.aspx)|[Документация к пакету SDK для Node.js](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest)|
|[Пакет SDK для .NET](https://azure.microsoft.com/ru-RU/documentation/api/)|[Пакет SDK для Node.js](https://www.npmjs.com/package/azure-keyvault)|

## Управление хранилищами ключей

Контейнерами хранилищ ключей Azure можно управлять с помощью REST, PowerShell или интерфейса командной строки (CLI), как описано в следующих статьях:

- [Создание хранилищ ключей и управление ими с помощью REST](https://msdn.microsoft.com/library/azure/mt620024.aspx)
- [Создание хранилищей ключей и управление ими с помощью PowerShell](key-vault-get-started.md)
- [Создание хранилищей ключей и управление ими с помощью CLI](key-vault-manage-with-cli.md)


## Инструкции

В следующих статьях приводятся учебники по решению конкретных задач:

- [Создание ключей, защищенных аппаратным модулем безопасности, и их передача в хранилище ключей Azure](key-vault-hsm-protected-keys.md)

## Примеры

- В данной загрузке содержится и пример приложения HelloKeyVault, и пример веб-службы Azure. [Azure Key Vault code samples](http://www.microsoft.com/download/details.aspx?id=45343)
- В этом учебнике показано, как использовать хранилище ключей Azure из веб-приложения Azure. [Использование хранилища ключей Azure из веб-приложения](key-vault-use-from-web-application.md)

## Поддержка библиотек

- [Библиотека ядра хранилища ключей Microsoft Azure](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core/1.0.0) предоставляет интерфейсы IKey и IKeyResolver для поиска ключей по идентификаторам и выполнения операций с ключами.

- [Расширения хранилища ключей Microsoft Azure](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions/1.0.0) предоставляют расширенные возможности для хранилища ключей Azure.

<!---HONumber=AcomDC_0121_2016-->