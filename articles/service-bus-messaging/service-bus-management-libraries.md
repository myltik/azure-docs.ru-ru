---
title: Библиотеки управления служебной шины Azure | Документация Майкрософт
description: Управление пространствами имен служебной шины и сущностями обмена сообщениями из .NET.
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/05/2018
ms.author: sethm
ms.openlocfilehash: 7946958bec8b2f444155b5a9701f1f7401fe4f3c
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/09/2018
ms.locfileid: "29120902"
---
# <a name="service-bus-management-libraries"></a>Библиотеки управления служебной шины

Библиотеки управления служебной шины Azure могут динамически подготавливать пространства имен и сущности служебной шины. Это дает возможность реализовывать сложные развертывания и сценарии обмена сообщениями и позволяет программно определять, какие сущности следует подготовить. В настоящее время эти библиотеки доступны для .NET.

## <a name="supported-functionality"></a>Поддерживаемые функции

* Создание, обновление, удаление пространства имен.
* Создание, обновление, удаление очереди.
* Создание, обновление, удаление раздела.
* Создание, обновление, удаление подписки.

## <a name="prerequisites"></a>предварительным требованиям

Чтобы приступить к работе с библиотеками управления служебной шины, нужно пройти аутентификацию в службе Azure Active Directory (Azure AD). Azure AD требует выполнять аутентификацию в качестве субъекта-службы, который предоставляет доступ к вашим ресурсам Azure. Сведения о создании субъекта-службы см. в одной из приведенных ниже статей:  

* [Создание приложения Active Directory и субъекта-службы с доступом к ресурсам с помощью портала](/azure/azure-resource-manager/resource-group-create-service-principal-portal)
* [Использование Azure PowerShell для создания субъекта-службы и доступа к ресурсам](/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Использование интерфейса командной строки Azure для создания субъекта-службы и доступа к ресурсам](/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)

В этих руководствах вы получите `AppId` (идентификатор клиента), `TenantId` и `ClientSecret` (ключ аутентификации), которые используются библиотеками управления для аутентификации. Необходимо иметь разрешения роли **Владелец** для группы ресурсов, которую вы хотите использовать.

## <a name="programming-pattern"></a>Шаблон программирования

Шаблон обработки любого ресурса служебной шины соответствует общему протоколу.

1. Получите маркер из Azure AD с помощью библиотеки **Microsoft.IdentityModel.Clients.ActiveDirectory**:
   ```csharp
   var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

   var result = await context.AcquireTokenAsync("https://management.core.windows.net/", new ClientCredential(clientId, clientSecret));
   ```
2. Создайте объект `ServiceBusManagementClient`:

   ```csharp
   var creds = new TokenCredentials(token);
   var sbClient = new ServiceBusManagementClient(creds)
   {
       SubscriptionId = SettingsCache["SubscriptionId"]
   };
   ```
3. Присвойте параметрам `CreateOrUpdate` указанные значения:

   ```csharp
   var queueParams = new QueueCreateOrUpdateParameters()
   {
       Location = SettingsCache["DataCenterLocation"],
       EnablePartitioning = true
   };
   ```
4. Выполните вызов:

   ```csharp
   await sbClient.Queues.CreateOrUpdateAsync(resourceGroupName, namespaceName, QueueName, queueParams);
   ```

## <a name="next-steps"></a>Дополнительная информация

* [Пример управления для .NET](https://github.com/Azure-Samples/service-bus-dotnet-management/)
* [Справочник по API Microsoft.Azure.Management.ServiceBus](/dotnet/api/Microsoft.Azure.Management.ServiceBus)
