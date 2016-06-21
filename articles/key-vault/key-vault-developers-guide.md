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
   ms.date="05/25/2016"
   ms.author="bruceper" />

# Руководство разработчика хранилища ключей Azure
С помощью хранилища ключей можно выполнять безопасный доступ к конфиденциальной информации из приложений и обеспечивать при этом выполнение следующих условий:

- Ключи и секреты будут защищены без необходимости написания кода. Вы сможете легко использовать их в своих приложениях.
- Ваши клиенты смогут управлять собственными ключами, поэтому вы можете сосредоточиться на предоставлении базовых функций программного обеспечения. В этом случае ваши приложения не будут отвечать за ключи и секреты клиентов.
- Ваше приложение может использовать ключи для подписывания и шифрования, но будет осуществлять управление ключами во внешней среде, чтобы решение подходило для географически удаленного приложения.

Дополнительную информацию о хранилище ключей Azure см. в статье [Что такое хранилище ключей Azure?](key-vault-whatis.md)

## Видеоролики
В этом видео показано, как создать собственное хранилище ключей и использовать его в примере приложения HelloKeyVault.

Ссылки на материалы, упоминаемые в этом видео:
- [Azure PowerShell](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)
- [Пример кода для хранилища ключей Azure](http://go.microsoft.com/fwlink/?LinkId=521527&clcid=0x409)

Для получения дополнительных сведений следите за [блогом хранилища ключей](http://aka.ms/kvblog) и заходите на [форум хранилища ключей](http://aka.ms/kvforum).



> [AZURE.VIDEO azure-key-vault-developer-quick-start]



## Создание хранилищ ключей и управление ими

Прежде чем приступить к работе с хранилищем ключей Azure в коде, можно создать и контролировать хранилища с помощью REST, шаблонов диспетчера ресурсов, PowerShell или интерфейса командной строки, как описано в следующих статьях:

- [Создание хранилищ ключей и управление ими с помощью REST](https://msdn.microsoft.com/library/azure/mt620024.aspx)
- [Создание хранилищей ключей и управление ими с помощью PowerShell](key-vault-get-started.md)
- [Создание хранилищей ключей и управление ими с помощью CLI](key-vault-manage-with-cli.md)
- [Создание хранилища ключей и добавление секрета с помощью шаблона ARM](../resource-manager-template-keyvault.md)

>[AZURE.NOTE] Операции с хранилищами ключей проходят проверку подлинности в AAD и авторизуются с помощью собственной политики доступа хранилища ключей, определенной для каждого хранилища.

## Программирование с помощью хранилища ключей

Система управления хранилищами ключей для программистов состоит из нескольких интерфейсов, в основе которых лежит REST. См. [Справочник по API REST хранилища ключей](https://msdn.microsoft.com/library/azure/dn903609.aspx).

При условии успешной авторизации вы можете выполнять следующие действия:

- управление ключами шифрования с помощью операций [Создания](https://msdn.microsoft.com/library/azure/dn903634.aspx), [Импорта](https://msdn.microsoft.com/library/azure/dn903626.aspx), [Обновления](https://msdn.microsoft.com/library/azure/dn903616.aspx), [Удаления](https://msdn.microsoft.com/library/azure/dn903611.aspx) и других;

- управление секретами с помощью операций [Получения](https://msdn.microsoft.com/library/azure/dn903633.aspx), [Обновления](https://msdn.microsoft.com/library/azure/dn986818.aspx), [Удаления](https://msdn.microsoft.com/library/azure/dn903613.aspx) и других;

- использование криптографических ключей с операциями [Входа](https://msdn.microsoft.com/library/azure/dn878096.aspx)/[Проверки](https://msdn.microsoft.com/library/azure/dn878082.aspx), [Обертывания ключа](https://msdn.microsoft.com/library/azure/dn878066.aspx)/[Развертывания ключа](https://msdn.microsoft.com/library/azure/dn878079.aspx) и [Шифрования](https://msdn.microsoft.com/library/azure/dn878060.aspx)/[Расшифрования](https://msdn.microsoft.com/library/azure/dn878097.aspx).

Для работы с хранилищем ключей доступны следующие пакеты SDK:

|[![.NET](./media/key-vault-developers-guide/msft.netlogo_purple.png)](https://msdn.microsoft.com/library/azure/dn903301.aspx)|[![Node.js](./media/key-vault-developers-guide/nodejs.png)](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest)
|:--:|:--:|
|[Документация к пакету SDK для .NET](https://msdn.microsoft.com/library/azure/dn903301.aspx)|[Документация к пакету SDK для Node.js](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest)|
|[Пакет SDK для .NET](https://azure.microsoft.com/documentation/api/)|[Пакет SDK для Node.js](https://www.npmjs.com/package/azure-keyvault)|


Полные примеры использования хранилища ключей с приложениями см. в следующих документах:

- Пример приложения .NET *HelloKeyVault* и пример веб-службы Azure. [Azure Key Vault code samples](http://www.microsoft.com/download/details.aspx?id=45343)
- Учебник, в котором показано, как использовать хранилище ключей Azure из веб-приложения Azure. [Использование хранилища ключей Azure из веб-приложения](key-vault-use-from-web-application.md)

## Инструкции

В следующих статьях приводятся рекомендации по решению конкретных задач при работе с хранилищем ключей Azure:

- [Создание ключей, защищенных аппаратным модулем безопасности, и их передача в хранилище ключей Azure](key-vault-hsm-protected-keys.md) — Данная статья поможет вам подготовить и создать ваши собственные ключи, защищенные аппаратным модулем безопасности, а затем передать их в хранилище ключей Azure.
- [Передача безопасных значений (например, паролей) во время развертывания](../resource-manager-keyvault-parameter.md) — Если в процессе развертывания в качестве параметра необходимо передать безопасное значение (например, пароль), его можно сохранить как секретный код в хранилище ключей Azure и вставить ссылку на это значение в другие шаблоны Resource Manager.
- [Использование хранилища ключей для расширенного управления ключами с помощью SQL Server](https://msdn.microsoft.com/library/dn198405.aspx) —Соединитель SQL Server для хранилища ключей Azure позволяет SQL Server и SQL в виртуальных машинах использовать службу хранилища ключей Azure в качестве поставщика расширенного управления ключами (EKM) для защиты ключей шифрования для связи приложений, а также предоставляет прозрачное шифрование данных, шифрование резервной копии и шифрование на уровне столбцов.
- [Развертывание сертификатов на виртуальных машинах из хранилища ключей](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/): облачное приложение, которое выполняется на виртуальной машине в Azure, требует сертификата. Как получить этот сертификат для виртуальной машины?
- [Интеграция хранилища ключей с шифрованием дисков Azure для виртуальных машин IaaS под управлением Windows или Linux](../azure-security-disk-encryption.md): это решение шифрования дисков интегрировано с хранилищем ключей Azure, то есть позволяет управлять ключами и секретами дискового шифрования через подписку хранилища ключей. Шифрование выполняется для всех данных на дисках виртуальных машин в хранилище Azure.

Руководство по интеграции и использованию хранилищ ключей в Azure см. в статье [Примеры шаблонов ARM для хранилища ключей от Райана Джонса](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).

## Поддержка библиотек

- [Библиотека ядра хранилища ключей Microsoft Azure](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core/1.0.0) предоставляет интерфейсы `IKey` и `IKeyResolver` для поиска ключей по идентификаторам и выполнения операций с ключами.

- [Расширения хранилища ключей Microsoft Azure](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions/1.0.0) предоставляют расширенные возможности для хранилища ключей Azure.

## Другие ресурсы хранилища ключей
- [Блог хранилища ключей](http://aka.ms/kvblog)
- [Форум хранилища ключей](http://aka.ms/kvforum)

<!---HONumber=AcomDC_0608_2016-->