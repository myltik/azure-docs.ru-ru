---
title: "Управление служебной шиной Azure при помощи службы автоматизации Azure | Документация Майкрософт"
description: "Узнайте, как использовать службу автоматизации Azure для управления служебной шиной Azure."
services: service-bus-messaging, automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: 
ms.assetid: 2a06e94b-92ef-4b5d-a2b7-fe827063df82
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: magoedte;csand
translationtype: Human Translation
ms.sourcegitcommit: 994a379129bffd7457912bc349f240a970aed253
ms.openlocfilehash: 4817cda757b9f85f6225237f79293860c990ca88


---
# <a name="managing-azure-service-bus-using-azure-automation"></a>Управление служебной шиной Azure при помощи службы автоматизации Azure
В этом руководстве представлена служба автоматизации Azure и способы ее использования для упрощения управления служебной шиной Azure.

## <a name="what-is-azure-automation"></a>Что такое служба автоматизации Azure?
[Служба автоматизации Azure](../automation/automation-intro.md) — это служба Azure, которая упрощает управление облаком благодаря автоматизации процессов и настройке требуемого состояния. С помощью службы автоматизации Azure повторяющиеся задачи, которые выполняются вручную, требуют много времени и подвержены ошибкам, можно автоматизировать для повышения надежности, эффективности и экономии времени в вашей организации.

Служба автоматизации Azure предоставляет высоконадежную и высокодоступную подсистему выполнения рабочих процессов, которая масштабируется в соответствии с вашими задачами. В службе автоматизации Azure процессы можно запустить вручную, в сторонних системах или по расписанию, чтобы все задачи выполнялись в нужное время.

Уменьшите операционные затраты и освободите ИТ-сотрудников и специалистов по разработке и операциям для работы над повышением бизнес-ценности ПО и автоматизации задач управления облаком в службе автоматизации Azure.

## <a name="how-can-azure-automation-help-manage-azure-service-bus"></a>Как служба автоматизации Azure может помочь управлять служебной шиной Azure?
Для управления служебной шиной можно использовать средства службы автоматизации Azure посредством [API REST служебной шины](https://docs.microsoft.com/rest/api/servicebus/). В службе автоматизации Azure можно выполнять сценарии PowerShell для выполнения многих задач служебной шины с помощью интерфейсов REST API. Вы также можете связать эти вызовы REST API в службе автоматизации Azure с командлетами для других служб Azure, чтобы автоматизировать сложные задачи в службах Azure и системах сторонних производителей.

Вот несколько примеров использования PowerShell для управления служебной шиной Azure.

* [Пользовательские командлеты PowerShell для управления очередями служебной шины Azure](https://blogs.technet.microsoft.com/uktechnet/2014/12/04/sample-of-custom-powershell-cmdlets-to-manage-azure-servicebus-queues)
* [Как создать запросы, разделы и подписки Service Bus с помощью сценария PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
* [Создание пространств имен служебной шины Azure с помощью PowerShell](http://buildazure.com/2015/09/24/create-azure-service-bus-namespaces-using-powershell-and-x-plat-cli/)

Модуль PowerShell для работы со служебной шиной Azure в модулях Runbook службы автоматизации можно скачать из [коллекции PowerShell](https://www.powershellgallery.com/packages/AzureServiceBusCreation/1.0).

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда вы познакомились с основами службы автоматизации Azure и способами ее использования для управления служебной шиной Azure, пройдите по ссылкам, чтобы получить дополнительные сведения о службе автоматизации Azure.

* Изучите [руководство по началу работы](../automation/automation-first-runbook-graphical.md) со службой автоматизации Azure.
* Узнайте, как [управлять служебной шиной с помощью PowerShell](service-bus-powershell-how-to-provision.md)




<!--HONumber=Jan17_HO2-->


