---
title: "Руководство разработчика хранилища ключей Azure"
description: "Разработчики могут использовать хранилище ключей Azure для управления криптографическими ключами в среде Microsoft Azure."
services: key-vault
author: BrucePerlerMS
manager: mbaldwin
ms.service: key-vault
ms.topic: article
ms.workload: identity
ms.date: 08/04/2017
ms.author: bruceper
ms.translationtype: HT
ms.sourcegitcommit: 99523f27fe43f07081bd43f5d563e554bda4426f
ms.openlocfilehash: 115862aca98926e354e4703f86cb4a7e1d1f72a2
ms.contentlocale: ru-ru
ms.lasthandoff: 08/05/2017

---
# <a name="azure-key-vault-developers-guide"></a>Руководство разработчика хранилища ключей Azure

Хранилище Key Vault обеспечивает безопасный доступ к конфиденциальной информации из приложений:

- Ключи и секреты защищены без необходимости написания кода. Вы сможете легко использовать их в своих приложениях.
- Ваши клиенты смогут управлять собственными ключами, поэтому вы можете сосредоточиться на предоставлении базовых функций программного обеспечения. В этом случае ваши приложения не будут отвечать за ключи и секреты клиентов.
- Ваше приложение может использовать ключи для подписывания и шифрования, но осуществляет управление ключами во внешней среде, чтобы ваше решение могло работать в качестве географически распределенного приложения.
- Начиная с выпуска Key Vault от сентября 2016 г. в приложениях можно использовать [сертификаты](https://docs.microsoft.com/rest/api/keyvault/certificate-operations) Key Vault. См. дополнительные сведения о [ключах, секретах и сертификатах](https://docs.microsoft.com/rest/api/keyvault/about-keys--secrets-and-certificates).

Дополнительные сведения о хранилище ключей Azure см. в статье [Что такое хранилище ключей Azure?](key-vault-whatis.md).

## <a name="public-previews"></a>Общедоступные предварительные версии

Периодически мы выпускаем общедоступные предварительные версии нового компонента Key Vault. Мы предлагаем вам испытать их и отправить отзыв на наш адрес электронной почты для обратной связи: azurekeyvault@microsoft.com.

### <a name="storage-account-keys---july-10-2017"></a>Ключи учетной записи хранения — 10 июля 2017 г.

>[!NOTE]
>В этом обновлении Azure Key Vault в режиме предварительной версии доступна только функция **Ключи учетной записи хранения**.

Эта предварительная версия включает новую функцию "Ключи учетной записи хранения", доступную через интерфейсы [.NET/C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.keyvault/), [REST](https://docs.microsoft.com/rest/api/keyvault/) или [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/). 

Дополнительные сведения о новой функции "Ключи учетной записи хранения" см. в статье, посвященной [учетной записи хранилища ключей Azure Key Vault](key-vault-ovw-storage-keys.md).

## <a name="videos"></a>Видеоролики

В этом видео показано, как создать собственное хранилище ключей и использовать его в примере приложения HelloKeyVault.

- [Key Vault developer - quick start guide](https://channel9.msdn.com/Blogs/Azure/Azure-Key-Vault-Developer-Quick-Start/player) (Краткое руководство по началу работы для разработчиков Key Vault)

Ресурсы, используемые в этом видео:

- [Azure PowerShell](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)
- [Пример кода для хранилища ключей Azure](http://go.microsoft.com/fwlink/?LinkId=521527&clcid=0x409)

## <a name="creating-and-managing-key-vaults"></a>Создание хранилищ ключей и управление ими

Прежде чем приступить к работе с хранилищем ключей Azure в коде, можно создать и контролировать хранилища с помощью REST, шаблонов диспетчера ресурсов, PowerShell или интерфейса командной строки, как описано в следующих статьях:

- [Создание хранилищ ключей и управление ими с помощью REST](https://docs.microsoft.com/rest/api/keyvault/)
- [Создание хранилищей ключей и управление ими с помощью PowerShell](key-vault-get-started.md)
- [Создание хранилищей ключей и управление ими с помощью CLI](key-vault-manage-with-cli2.md)
- [Create a key vault and add a secret via an Azure Resource Manager template](../azure-resource-manager/resource-manager-template-keyvault.md) (Создание хранилища ключей и добавление секрета с помощью шаблона Azure Resource Manager)

> [!NOTE]
> Операции с хранилищами ключей проходят проверку подлинности в AAD и авторизуются с помощью собственной политики доступа хранилища ключей, определенной для каждого хранилища.

## <a name="coding-with-key-vault"></a>Программирование с помощью хранилища ключей

Система управления Key Vault для программистов включает несколько интерфейсов на основе REST. Интерфейс REST предоставляет доступ ко всем ресурсам хранилищ ключей: ключам, секретам и сертификатам. [Справочник по REST API для Key Vault](https://docs.microsoft.com/rest/api/keyvault/) 

### <a name="supported-programming-languages"></a>Поддерживаемые языки программирования

#### <a name="net"></a>.NET

- [Справочник по API .NET для Key Vault](https://docs.microsoft.com/dotnet/api/microsoft.azure.keyvault) 

Дополнительные сведения о версии 2.x пакета SDK для .NET см. в [заметках о выпуске](key-vault-dotnet2api-release-notes.md).

#### <a name="java"></a>Java

- [Пакет Java SDK для Key Vault](https://docs.microsoft.com/java/api/com.microsoft.azure.keyvault)

#### <a name="nodejs"></a>Node.js

В Node.js API функции управления хранилищем не включены в API объекта хранилища. Управление Key Vault позволяет создавать и обновлять хранилище ключей. API операций Key Vault предназначен для работы с такими объектами хранилища, как ключи, секреты и сертификаты. 

- [Справочник по API Node.js для управления Key Vault](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest/)
- [Справочник по API Node.js для операций Key Vault](http://azure.github.io/azure-sdk-for-node/azure-keyvault/latest/) 

### <a name="quick-start"></a>Быстрый запуск

- [Создание хранилища ключей](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)
- [Приступая к работе с Key Vault в Node.js](https://azure.microsoft.com/en-us/resources/samples/key-vault-node-getting-started/)

### <a name="code-examples"></a>Примеры кода

Полные примеры использования хранилища ключей с приложениями см. в следующих документах:

- [Примеры кода Azure Key Vault](http://www.microsoft.com/download/details.aspx?id=45343) — пример приложения .NET *HelloKeyVault* и пример веб-службы Azure. 
- [Использование Azure Key Vault из Web-приложений](key-vault-use-from-web-application.md) — руководство по использованию Azure Key Vault из веб-приложений Azure. 

## <a name="how-tos"></a>Инструкции

В следующих статьях приводятся рекомендации по решению конкретных задач при работе с Azure Key Vault:

- [Изменение идентификатора клиента хранилища ключей после перемещения подписки](key-vault-subscription-move-fix.md). При перемещении подписки Azure из клиента A в клиент Б имеющиеся хранилища ключей недоступны для субъектов (пользователей и приложений) в клиенте Б. Устраните эту проблему, следуя указаниям в этом руководстве.
- [Доступ к хранилищу ключей под защитой брандмауэра](key-vault-access-behind-firewall.md). Для доступа к хранилищу ключей необходимо, чтобы клиентское приложение имело доступ к нескольким конечным точкам, требуемым для различных функций.
- [Создание ключей, защищенных аппаратным модулем безопасности, и их передача в хранилище ключей Azure](key-vault-hsm-protected-keys.md) — Данная статья поможет вам подготовить и создать ваши собственные ключи, защищенные аппаратным модулем безопасности, а затем передать их в хранилище ключей Azure.
- [Передача безопасных значений (например, паролей) во время развертывания](../azure-resource-manager/resource-manager-keyvault-parameter.md) — Если в процессе развертывания в качестве параметра необходимо передать безопасное значение (например, пароль), его можно сохранить как секретный код в хранилище ключей Azure и вставить ссылку на это значение в другие шаблоны Resource Manager.
- [Использование хранилища ключей для расширенного управления ключами с помощью SQL Server](https://msdn.microsoft.com/library/dn198405.aspx) —Соединитель SQL Server для хранилища ключей Azure позволяет SQL Server и SQL в виртуальных машинах использовать службу хранилища ключей Azure в качестве поставщика расширенного управления ключами (EKM) для защиты ключей шифрования для связи приложений, а также предоставляет прозрачное шифрование данных, шифрование резервной копии и шифрование на уровне столбцов.
- [Развертывание сертификатов на виртуальных машинах из хранилища ключей](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/) : облачное приложение, которое выполняется на виртуальной машине в Azure, требует сертификата. Как получить этот сертификат для виртуальной машины?
- [Настройка полной смены ключей и аудита в Key Vault](key-vault-key-rotation-log-monitoring.md) — руководство по настройке смены ключей и аудита журналов с помощью Azure Key Vault.
- [Deploying Azure Web App Certificate through Key Vault]( https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/) (Развертывание сертификата службы веб-приложения Azure с помощью Key Vault) — пошаговое руководство по развертыванию сертификатов, хранящихся в Key Vault, в рамках предложения [Сертификаты службы приложений](https://azure.microsoft.com/blog/internals-of-app-service-certificate/).
- [Предоставление разрешения на доступ к Key Vault нескольким приложениям](key-vault-group-permissions-for-apps.md). Политика контроля доступа Key Vault поддерживает не более 16 приложений. Однако вы можете создать группу безопасности Azure Active Directory. Добавьте все связанные субъекты-службы в эту группу безопасности, а затем предоставьте доступ данной группе безопасности к Key Vault.
- Руководство по интеграции и использованию хранилищ ключей в Azure см. в этой статье [с примерами шаблонов Azure Resource Manager для хранилища ключей от Райана Джонса (Ryan Jones)](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
- В разделе [Как использовать обратимое удаление в Key Vault с помощью интерфейса командной строки](key-vault-soft-delete-cli.md) описывается использование и жизненный цикл хранилища ключей, а также различных объектов хранилища ключей с возможностью обратимого удаления.
- В разделе [Как использовать обратимое удаление в Key Vault с помощью PowerShell](key-vault-soft-delete-powershell.md) описывается использование и жизненный цикл хранилища ключей, а также различных объектов хранилища ключей, поддерживающих обратимое удаление.

## <a name="integrated-with-key-vault"></a>Интеграция с хранилищем ключей

Эти статьи посвящены другим сценариям и службам, использующим Key Vault или интегрирующимся с ним.

- [Дисковое шифрование Azure](../security/azure-security-disk-encryption.md) использует стандартные для отрасли функции — [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) в Windows и [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) в Linux, — которые обеспечивают шифрование томов для системных дисков и дисков данных. Это решение интегрировано с хранилищем ключей Azure. Решение позволяет управлять ключами и секретами дискового шифрования через подписку хранилища ключей. Шифрование выполняется для всех данных на дисках виртуальных машин в хранилище Azure.
- В [Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md) можно включить шифрование данных, хранящихся в учетной записи. Data Lake Store предоставляет два режима для управления главными ключами шифрования (MEKs), необходимыми для расшифровки любых данных, хранящихся в Data Lake Store. Вы можете позволить Data Lake Store управлять главными ключами шифрования или управлять ими самостоятельно с помощью учетной записи хранилища ключей Azure. Способ управления ключами можно задать во время создания учетной записи Data Lake Store. 
- [Azure Information Protection](/information-protection/plan-design/plan-implement-tenant-key) позволяет управлять собственным ключом клиента. Например, вместо того, чтобы вашим ключом клиента управляла корпорация Майкрософт (по умолчанию), вы можете сами управлять им в соответствии с определенными нормами своей организации. Сценарий с использованием собственного ключа клиента называется BYOK.

## <a name="key-vault-overviews-and-concepts"></a>Основные сведения о Key Vault

- [Общие сведения о функции обратимого удаления в Azure Key Vault](key-vault-ovw-soft-delete.md). Описание функции, которая позволяет восстанавливать случайно или намеренно удаленные объекты.
- [Руководство по регулированию Azure Key Vault](key-vault-ovw-throttling.md). Описание основных механизмов регулирования числа запросов и подходов к созданию приложения.
- [Key Vault storage account keys overview](key-vault-ovw-storage-keys.md) (Общие сведения об учетной записи хранения Key Vault). Описание интеграции Key Vault с ключами учетных записей службы хранилища Azure.
- [Системы безопасности и географические ограничения Azure Key Vault](key-vault-ovw-security-worlds.md). Описание взаимосвязей между регионами и областями безопасности.

## <a name="social"></a>Социальные сети

- [Блог хранилища ключей](http://aka.ms/kvblog)
- [Форум хранилища ключей](http://aka.ms/kvforum)

## <a name="supporting-libraries"></a>Поддержка библиотек

- [Библиотека ядра Key Vault Microsoft Azure](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core) предоставляет интерфейсы **IKey** и **IKeyResolver** для поиска ключей по идентификаторам и выполнения операций с ключами.
- [Расширения хранилища ключей Microsoft Azure](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions) предоставляют расширенные возможности для хранилища ключей Azure.



