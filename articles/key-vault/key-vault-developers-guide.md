<properties
   pageTitle="Руководство разработчика хранилища ключей | Microsoft Azure"
   description="Разработчики могут использовать хранилище ключей Azure для управления криптографическими ключами в среде Microsoft Azure. "
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
   ms.date="10/03/2016"
   ms.author="bruceper" />


# <a name="azure-key-vault-developer's-guide"></a>Руководство разработчика хранилища ключей Azure
С помощью хранилища ключей можно выполнять безопасный доступ к конфиденциальной информации из приложений и обеспечивать при этом выполнение следующих условий:

- Ключи и секреты будут защищены без необходимости написания кода. Вы сможете легко использовать их в своих приложениях.
- Ваши клиенты смогут управлять собственными ключами, поэтому вы можете сосредоточиться на предоставлении базовых функций программного обеспечения. В этом случае ваши приложения не будут отвечать за ключи и секреты клиентов.
- Ваше приложение может использовать ключи для подписывания и шифрования, но будет осуществлять управление ключами во внешней среде, чтобы решение подходило для географически удаленного приложения.

- В выпуске хранилища ключей от сентября 2016 года представлена возможность использования сертификатов хранилища ключей для приложений. Дополнительные сведения см. в статье **About keys, secrets, and certificates** (О ключах, секретах и сертификатах) в [справочнике по REST](https://msdn.microsoft.com/library/azure/dn903623.aspx).

Дополнительные сведения о хранилище ключей Azure см. в статье [Что такое хранилище ключей Azure?](key-vault-whatis.md).

## <a name="videos"></a>Видеоролики
В этом видео показано, как создать собственное хранилище ключей и использовать его в примере приложения HelloKeyVault.

[AZURE.VIDEO azure-key-vault-developer-quick-start]

Ссылки на материалы, упоминаемые в этом видео:
- [Azure PowerShell](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)
- [Пример кода для хранилища ключей Azure](http://go.microsoft.com/fwlink/?LinkId=521527&clcid=0x409)

Для получения дополнительных сведений следите за [блогом хранилища ключей](http://aka.ms/kvblog) и заходите на [форум хранилища ключей](http://aka.ms/kvforum).

## <a name="creating-and-managing-key-vaults"></a>Создание хранилищ ключей и управление ими

Прежде чем приступить к работе с хранилищем ключей Azure в коде, можно создать и контролировать хранилища с помощью REST, шаблонов диспетчера ресурсов, PowerShell или интерфейса командной строки, как описано в следующих статьях:

- [Создание хранилищ ключей и управление ими с помощью REST](https://msdn.microsoft.com/library/azure/mt620024.aspx)
- [Создание хранилищей ключей и управление ими с помощью PowerShell](key-vault-get-started.md)
- [Создание хранилищей ключей и управление ими с помощью CLI](key-vault-manage-with-cli.md)
- [Create a key vault and add a secret via an Azure Resource Manager template](../resource-manager-template-keyvault.md) (Создание хранилища ключей и добавление секрета с помощью шаблона Azure Resource Manager)

>[AZURE.NOTE] Операции с хранилищами ключей проходят проверку подлинности в AAD и авторизуются с помощью собственной политики доступа хранилища ключей, определенной для каждого хранилища.

## <a name="coding-with-key-vault"></a>Программирование с помощью хранилища ключей

Система управления хранилищами ключей для программистов состоит из нескольких интерфейсов, в основе которых лежит REST. См. [Key Vault REST API Reference](https://msdn.microsoft.com/library/azure/dn903609.aspx) (Справочник по REST API хранилища ключей).

При условии успешной авторизации вы можете выполнять следующие действия:

- управление ключами шифрования с помощью операций [создания](https://msdn.microsoft.com/library/azure/dn903634.aspx), [импорта](https://msdn.microsoft.com/library/azure/dn903626.aspx), [обновления](https://msdn.microsoft.com/library/azure/dn903616.aspx), [удаления](https://msdn.microsoft.com/library/azure/dn903611.aspx) и других;

- управление секретами с помощью операций [получения](https://msdn.microsoft.com/library/azure/dn903633.aspx), [обновления](https://msdn.microsoft.com/library/azure/dn986818.aspx), [удаления](https://msdn.microsoft.com/library/azure/dn903613.aspx) и других;

- использование криптографических ключей с операциями [входа](https://msdn.microsoft.com/library/azure/dn878096.aspx)/[проверки](https://msdn.microsoft.com/library/azure/dn878082.aspx), [обертывания ключа](https://msdn.microsoft.com/library/azure/dn878066.aspx)/[развертывания ключа](https://msdn.microsoft.com/library/azure/dn878079.aspx) и [шифрования](https://msdn.microsoft.com/library/azure/dn878060.aspx)/[расшифрования](https://msdn.microsoft.com/library/azure/dn878097.aspx).

Для работы с хранилищем ключей доступны следующие пакеты SDK:

|[![.NET](./media/key-vault-developers-guide/msft.netlogo_purple.png)](https://msdn.microsoft.com/library/mt765854.aspx)|[![Node.js](./media/key-vault-developers-guide/nodejs.png)](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest)
|:--:|:--:|
|[Документация к пакету SDK для .NET](https://msdn.microsoft.com/library/mt765854.aspx)|[Документация к пакету SDK для Node.js](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest)|
|[Пакет SDK для .NET и NuGet](http://www.nuget.org/packages/Microsoft.Azure.KeyVault)|[Пакет SDK для Node.js](https://www.npmjs.com/package/azure-keyvault)|

Дополнительные сведения о версии 2.x пакета SDK для .NET см. в [заметках о выпуске](key-vault-dotnet2api-release-notes.md).

## <a name="example-code"></a>Пример кода
Полные примеры использования хранилища ключей с приложениями см. в следующих документах:

- Пример приложения .NET *HelloKeyVault* и пример веб-службы Azure. [Azure Key Vault code samples](http://www.microsoft.com/download/details.aspx?id=45343)
- Учебник, в котором показано, как использовать хранилище ключей Azure из веб-приложения Azure. [Использование хранилища ключей Azure из веб-приложения](key-vault-use-from-web-application.md)

## <a name="how-tos"></a>Инструкции

В следующих статьях приводятся рекомендации по решению конкретных задач при работе с хранилищем ключей Azure:

- [Изменение идентификатора клиента хранилища ключей после перемещения подписки](key-vault-subscription-move-fix.md). При перемещении подписки Azure из клиента A в клиент Б имеющиеся хранилища ключей недоступны для субъектов (пользователей и приложений) в клиенте Б. Устраните эту проблему, следуя указаниям в этом руководстве.
- [Доступ к хранилищу ключей под защитой брандмауэра](key-vault-access-behind-firewall.md). Для доступа к хранилищу ключей необходимо, чтобы клиентское приложение имело доступ к нескольким конечным точкам, требуемым для различных функций.

- [Создание ключей, защищенных аппаратным модулем безопасности, и их передача в хранилище ключей Azure](key-vault-hsm-protected-keys.md) — Данная статья поможет вам подготовить и создать ваши собственные ключи, защищенные аппаратным модулем безопасности, а затем передать их в хранилище ключей Azure.
- [Передача безопасных значений (например, паролей) во время развертывания](../resource-manager-keyvault-parameter.md) — Если в процессе развертывания в качестве параметра необходимо передать безопасное значение (например, пароль), его можно сохранить как секретный код в хранилище ключей Azure и вставить ссылку на это значение в другие шаблоны Resource Manager.
- [Использование хранилища ключей для расширенного управления ключами с помощью SQL Server](https://msdn.microsoft.com/library/dn198405.aspx) —Соединитель SQL Server для хранилища ключей Azure позволяет SQL Server и SQL в виртуальных машинах использовать службу хранилища ключей Azure в качестве поставщика расширенного управления ключами (EKM) для защиты ключей шифрования для связи приложений, а также предоставляет прозрачное шифрование данных, шифрование резервной копии и шифрование на уровне столбцов.
- [Развертывание сертификатов на виртуальных машинах из хранилища ключей](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/) : облачное приложение, которое выполняется на виртуальной машине в Azure, требует сертификата. Как получить этот сертификат для виртуальной машины?
- [How to setup Key Vault with end to end key rotation and auditing](key-vault-key-rotation-log-monitoring.md) (Как настроить в хранилище ключей полную смену ключей и аудит): руководство по смене ключей и аудиту журналов с помощью хранилища ключей Azure.

Руководство по интеграции и использованию хранилищ ключей в Azure см. в этой статье [с примерами шаблонов Azure Resource Manager для хранилища ключей от Райана Джонса (Ryan Jones)](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).

## <a name="integrated-with-key-vault"></a>Интеграция с хранилищем ключей

Этот цикл статей посвящен другим сценариям и службам, которые позволяют выполнить интеграцию с хранилищем ключей.

- [Дисковое шифрование Azure](../security/azure-security-disk-encryption.md) использует стандартные для отрасли функции — [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) в Windows и [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) в Linux, — которые обеспечивают шифрование томов для системных дисков и дисков данных. Это решение интегрировано с хранилищем ключей Azure. Решение позволяет управлять ключами и секретами дискового шифрования через подписку хранилища ключей. Шифрование выполняется для всех данных на дисках виртуальных машин в хранилище Azure.


## <a name="supporting-libraries"></a>Поддержка библиотек

- [Библиотека ядра хранилища ключей Microsoft Azure](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core) предоставляет интерфейсы `IKey` и `IKeyResolver` для поиска ключей по идентификаторам и выполнения операций с ключами.

- [Расширения хранилища ключей Microsoft Azure](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions) предоставляют расширенные возможности для хранилища ключей Azure.

## <a name="other-key-vault-resources"></a>Другие ресурсы хранилища ключей
- [Блог хранилища ключей](http://aka.ms/kvblog)
- [Форум хранилища ключей](http://aka.ms/kvforum)



<!--HONumber=Oct16_HO2-->


