---
title: Управляемое удостоверение службы (MSI) для Azure Active Directory
description: Обзор применения управляемого удостоверения службы (MSI) для ресурсов Azure.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 12/19/2017
ms.author: skwan
ms.openlocfilehash: e4f9d9e4e0f84610ad072d889abf68b62c0dd41f
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
#  <a name="managed-service-identity-msi-for-azure-resources"></a>Управляемое удостоверение службы (MSI) для ресурсов Azure

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Распространенная проблема при создании облачных приложений — управление учетными данными, которые необходимы в коде для аутентификации в облачных службах. Обеспечение безопасности этих учетных данных является важной задачей. В идеальном случае они никогда не передаются на рабочие станции разработчиков и не записываются после изменения в систему управления версиями. Azure Key Vault позволяет обеспечить безопасное хранение учетных данных, а также других ключей и секретов, но для их получения код должен выполнять аутентификацию в Key Vault. Управляемое удостоверение службы (MSI) упрощает решение этой задачи, предоставляя службам Azure автоматически управляемое удостоверение в Azure Active Directory (Azure AD). Это удостоверение можно использовать для аутентификации в любой службе, которая поддерживает аутентификацию Azure AD, включая Key Vault, не храня какие-либо учетные данные в коде.

## <a name="how-does-it-work"></a>Как это работает?

Когда вы включаете управляемое удостоверение службы для службы Azure, Azure автоматически создает удостоверение для экземпляра службы в клиенте Azure AD, используемом вашей подпиской Azure.  На самом деле Azure подготавливает учетные данные для удостоверения экземпляра службы.  Затем ваш код может отправить локальный запрос на получение маркеров доступа для служб, которые поддерживают аутентификацию Azure AD.  Azure выполняет развертывание учетных данных, используемых экземпляром службы.  При удалении экземпляра службы Azure автоматически очищает учетные данные и удостоверение в Azure AD.

Ниже приведен пример работы управляемого удостоверения службы с виртуальными машинами Azure.

![Пример MSI виртуальной машины](../media/msi-vm-example.png)

1. Azure Resource Manager получает сообщение, предписывающее включить MSI для виртуальной машины.
2. Azure Resource Manager создает субъект-службу в Azure AD, представляющий удостоверение виртуальной машины. В клиенте Azure AD, который является доверенным для этой подписки, создается субъект-служба.
3. Azure Resource Manager настраивает данные субъекта-службы в расширении виртуальной машины для MSI на виртуальной машине.  Этот этап включает в себя настройку идентификатора клиента и сертификата, используемых расширением для получения маркеров доступа из Azure AD.
4. Теперь, когда известно удостоверение субъекта-службы виртуальной машины, ей может быть предоставлен доступ к ресурсам Azure.  Например, если ваш код должен вызывать Azure Resource Manager, то следует назначить субъекту-службе виртуальной машины соответствующую роль с помощью управления доступом на основе ролей (RBAC) в Azure AD.  Если код должен отправить вызов к Key Vault, то ему следует предоставить доступ к определенному секрету или ключу в Key Vault.
5. Ваш код, запущенный на виртуальной машине, запрашивает маркер из локальной конечной точки, размещенной расширением MSI для виртуальной машины: http://localhost:50342/oauth2/token.  Параметр ресурса указывает службу, в которую отправляется маркер. Например, если нужно выполнить аутентификацию кода в Azure Resource Manager, используйте параметр resource=https://management.azure.com/.
6. Расширение виртуальной машины для MSI использует настроенный для него идентификатор клиента и сертификат для запроса маркера доступа из Azure AD.  Azure AD возвращает маркер доступа JSON Web Token (JWT).
7. Код отправляет этот маркер доступа при вызове службы, которая поддерживает аутентификацию Azure AD.

Каждая служба Azure, которая поддерживает управляемое удостоверение службы, применяет собственный метод получения маркера доступа для вашего кода. Ознакомьтесь с руководствами для каждой из служб, чтобы узнать, какие методы получения маркера они используют.

## <a name="try-managed-service-identity"></a>Использование управляемого удостоверения службы

Чтобы ознакомиться с комплексными сценариями для доступа к различным ресурсам Azure, используйте руководство по управляемому удостоверению службы:
<br><br>
| Из ресурса, поддерживающего MSI | Сценарий для ознакомления |
| ------- | -------- |
| Виртуальная машина Azure (Windows) | [Получение доступа к Azure Data Lake Store с помощью управляемого удостоверения службы виртуальной машины Windows](tutorial-windows-vm-access-datalake.md) |
|                    | [Получение доступа к Azure Resource Manager с помощью управляемого удостоверения службы виртуальной машины Windows](tutorial-windows-vm-access-arm.md) |
|                    | [Получение доступа к Azure SQL с помощью управляемого удостоверения службы виртуальной машины Windows](tutorial-windows-vm-access-sql.md) |
|                    | [Получение доступа к службе хранилища Azure с ключом доступа при помощи управляемого удостоверения службы виртуальной машины Windows](tutorial-windows-vm-access-storage.md) |
|                    | [Получение доступа к службе хранилища Azure с SAS при помощи управляемого удостоверения службы виртуальной машины Windows](tutorial-windows-vm-access-storage-sas.md) |
|                    | [Получение доступа к ресурсу, размещенному не в Azure AD, с помощью управляемого удостоверения службы виртуальной машины Windows и Azure Key Vault](tutorial-windows-vm-access-nonaad.md) |
| Виртуальная машина Azure (Linux)   | [Получение доступа к Azure Data Lake Store с помощью управляемого удостоверения службы виртуальной машины Linux](tutorial-linux-vm-access-datalake.md) |
|                    | [Получение доступа к Azure Resource Manager с помощью управляемого удостоверения службы виртуальной машины Linux](tutorial-linux-vm-access-arm.md) |
|                    | [Получение доступа к службе хранилища Azure с ключом доступа при помощи управляемого удостоверения службы виртуальной машины Linux](tutorial-linux-vm-access-storage.md) |
|                    | [Получение доступа к службе хранилища Azure с SAS при помощи управляемого удостоверения службы виртуальной машины Linux](tutorial-linux-vm-access-storage-sas.md) |
|                    | [Получение доступа к ресурсу, размещенному не в Azure AD, с помощью управляемого удостоверения службы виртуальной машины Linux и Azure Key Vault](tutorial-linux-vm-access-nonaad.md) |
| Служба приложений Azure  | [Использование управляемого удостоверения службы со службой приложений Azure или службой "Функции Azure"](/azure/app-service/app-service-managed-service-identity) |
| Функции Azure    | [Использование управляемого удостоверения службы со службой приложений Azure или службой "Функции Azure"](/azure/app-service/app-service-managed-service-identity) |
| Azure Service Bus  | [Использование удостоверения управляемой службы со служебной шиной Azure](../../service-bus-messaging/service-bus-managed-service-identity.md) |
| Концентраторы событий Azure   | [Использование удостоверения управляемой службы с концентраторами событий Azure](../../event-hubs/event-hubs-managed-service-identity.md) |

## <a name="which-azure-services-support-managed-service-identity"></a>Какие службы Azure поддерживают управляемое удостоверение службы?

Службы Azure, которые поддерживают управляемое удостоверение службы, могут использовать MSI для аутентификации в службах, поддерживающих аутентификацию Azure AD.  Мы работаем над интеграцией MSI и аутентификации Azure AD на платформе Azure.  Почаще проверяйте наличие обновлений.

### <a name="azure-services-that-support-managed-service-identity"></a>Службы Azure, поддерживающие управляемое удостоверение службы

Ниже приведены службы Azure, которые поддерживают управляемое удостоверение службы.

| Service | Status | Дата | Настройка | Получение маркера |
| ------- | ------ | ---- | --------- | ----------- |
| Виртуальные машины Azure | Предварительный просмотр | Сентябрь 2017 г. | [портал Azure](qs-configure-portal-windows-vm.md)<br>[PowerShell](qs-configure-powershell-windows-vm.md)<br>[интерфейс командной строки Azure](qs-configure-cli-windows-vm.md)<br>[Шаблоны диспетчера ресурсов Azure](qs-configure-template-windows-vm.md) | [REST](how-to-use-vm-token.md#get-a-token-using-http)<br>[.NET](how-to-use-vm-token.md#get-a-token-using-c)<br>[Bash/Curl](how-to-use-vm-token.md#get-a-token-using-curl)<br>[GO](how-to-use-vm-token.md#get-a-token-using-go)<br>[PowerShell](how-to-use-vm-token.md#get-a-token-using-azure-powershell) |
| Служба приложений Azure | Предварительный просмотр | Сентябрь 2017 г. | [портал Azure](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Шаблон Azure Resource Manager](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol) |
| Функции Azure<sup>1</sup> | Предварительный просмотр | Сентябрь 2017 г. | [портал Azure](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Шаблон Azure Resource Manager](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol) |
| Фабрика данных Azure версии 2 | Предварительный просмотр | Ноябрь 2017 г. | [портал Azure](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity)<br>[PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-powershell)<br>[REST](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-rest-api)<br>[Пакет SDK](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-sdk) |

<sup>1</sup> Поддержка решения "Функции Azure" позволяет применять в пользовательском коде идентификатор, но для триггеров и привязок по-прежнему могут требоваться строки подключения.

### <a name="azure-services-that-support-azure-ad-authentication"></a>Службы Azure, поддерживающие аутентификацию Azure AD

Ниже приведены службы, поддерживающие аутентификацию Azure AD, которые были проверены с помощью служб клиента, использующих управляемое удостоверение службы.

| Service | Идентификатор ресурса | Status | Дата | Назначение доступа |
| ------- | ----------- | ------ | ---- | ------------- |
| Диспетчер ресурсов Azure | https://management.azure.com | Доступна | Сентябрь 2017 г. | [портал Azure](howto-assign-access-portal.md) <br>[PowerShell](howto-assign-access-powershell.md) <br>[интерфейс командной строки Azure](howto-assign-access-CLI.md) |
| Хранилище ключей Azure | https://vault.azure.net | Доступна | Сентябрь 2017 г. | |
| Azure Data Lake; | https://datalake.azure.net | Доступна | Сентябрь 2017 г. | |
| Azure SQL | https://database.windows.net | Доступна | Октябрь 2017 г. | |
| Концентраторы событий Azure | https://eventhubs.azure.net | Доступна | Декабрь 2017 г. | |
| Azure Service Bus | https://servicebus.azure.net | Доступна | Декабрь 2017 г. | |

## <a name="how-much-does-managed-service-identity-cost"></a>Сколько стоит использование управляемого удостоверения службы?

Управляемое удостоверение службы поставляется с лицензией Azure Active Directory Free, используемой по умолчанию для подписок Azure.  За использование возможностей управляемого удостоверения службы не взимается дополнительная плата.

## <a name="support-and-feedback"></a>Поддержка и обратная связь

Мы будем рады узнать ваше мнение!

* Задавайте практические вопросы на сайте Stack Overflow, пометив их тегом [azure-msi](http://stackoverflow.com/questions/tagged/azure-msi).
* Запросите новые функции или отправьте отзыв на [форуме обратной связи по Azure AD для разработчиков](https://feedback.azure.com/forums/169401-azure-active-directory/category/164757-developer-experiences).






