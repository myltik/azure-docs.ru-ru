<properties
   pageTitle="Руководство разработчика хранилища ключей | Microsoft Azure"
   description="Разработчики могут использовать хранилище ключей Azure для управления криптографическими ключами в среде Microsoft Azure."
   services="key-vault"
   documentationCenter=""
   authors="BrucePerlerMS"
   manager="mbaldwin"
   editor="mbaldwin" />
<tags
   ms.service="key-vault"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/22/2015"
   ms.author="mbaldwin" />

# Руководство разработчика хранилища ключей Azure

> [AZURE.VIDEO azure-key-vault-developer-quick-start]

Разработчики могут использовать хранилище ключей Azure для управления криптографическими ключами в среде Microsoft Azure. Хранилище ключей поддерживает ряд типов ключей и алгоритмов и может использоваться с аппаратными модулями безопасности (HSM) для пользовательских ключей, представляющих большую ценность. Кроме того, в хранилище ключей можно надежно хранить секреты, которые представляют объекты октетов ограниченного размера без конкретной семантики. Хранилище ключей может одновременно содержать и ключи, и секреты. Управление доступом для разных типов объектов осуществляется независимо друг от друга.

Пользователи, которые успешно прошли авторизацию, могут выполнять следующие действия:

- управление ключами шифрования с помощью операций [Создания](https://msdn.microsoft.com/library/azure/dn903634.aspx), [Импорта](https://msdn.microsoft.com/library/azure/dn903626.aspx), [Обновления](https://msdn.microsoft.com/library/azure/dn903616.aspx), [Удаления](https://msdn.microsoft.com/library/azure/dn903611.aspx) и других;

- управление секретами с помощью операций [Получения](https://msdn.microsoft.com/library/azure/dn903633.aspx), [Обновления](https://msdn.microsoft.com/library/azure/dn986818.aspx, [Удаления](https://msdn.microsoft.com/library/azure/dn903613.aspx) и других;

- использование криптографических ключей с операциями [Входа](https://msdn.microsoft.com/library/azure/dn878096.aspx)/[Проверки](https://msdn.microsoft.com/library/azure/dn878082.aspx), [Обертывания ключа](https://msdn.microsoft.com/library/azure/dn878066.aspx)/[Развертывания ключа](https://msdn.microsoft.com/library/azure/dn878079.aspx) и [Шифрования](https://msdn.microsoft.com/library/azure/dn878060.aspx)/[Расшифрования](https://msdn.microsoft.com/library/azure/dn878097.aspx).

Операции с хранилищами ключей проходят проверку подлинности и авторизацию с использованием Azure Active Directory.

## Программирование для хранилища ключей

Система управления хранилищем ключей для программистов состоит из нескольких интерфейсов, в основе которых лежит REST. Эта статья в справочнике по REST предоставляет основные понятия [о ключах и секретах](https://msdn.microsoft.com/library/azure/dn903623.aspx).

### REST

Интерфейс API REST является основой всего программного взаимодействия с хранилищем ключей.

У хранилища ключей есть собственная конечная точка REST, которая описана в статье [Справочник по интерфейсу REST API для хранилища ключей](https://msdn.microsoft.com/library/azure/dn903609.aspx).

### .NET

API-интерфейс .NET — набор оболочек, обеспечивающий реализацию через модель программирования C# без необходимости прямого взаимодействия с конечной точкой REST. Здесь можно найти [справочник по API-интерфейсу клиента .NET для хранилища ключей Azure](https://msdn.microsoft.com/library/azure/dn903301.aspx).

### Node.js

API-интерфейс Node.js — набор оболочек, обеспечивающий реализацию через модель программирования node.js без необходимости прямого взаимодействия с конечной точкой REST. Здесь можно найти [пакет Microsoft Azure SDK для Node.js — управление хранилищем ключей](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest/).

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

<!---HONumber=Nov15_HO2-->