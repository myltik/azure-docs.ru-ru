<properties
	pageTitle="Управление служебной шиной Azure при помощи службы автоматизации Azure | Microsoft Azure"
	description="Узнайте, как использовать службу автоматизации Azure для управления служебной шиной Azure."
	services="service-bus, automation"
	documentationCenter=""
	authors="csand-msft"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-bus"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/12/2016"
	ms.author="csand"/>

# Управление служебной шиной Azure при помощи службы автоматизации Azure

В этом руководстве представлена служба автоматизации Azure и способы ее использования для упрощения управления служебной шиной Azure.

## Что такое служба автоматизации Azure?

[Служба автоматизации Azure](https://azure.microsoft.com/services/automation/) — это служба Azure для упрощения управления облаком путем автоматизации процессов и настройки требуемого состояния. С помощью службы автоматизации Azure повторяющиеся задачи, которые выполняются вручную, требуют много времени и подвержены ошибкам, можно автоматизировать для повышения надежности, эффективности и экономии времени в вашей организации.

Служба автоматизации Azure предоставляет высоконадежную и высокодоступную подсистему выполнения рабочих процессов, которая масштабируется в соответствии с вашими задачами. В службе автоматизации Azure процессы можно запустить вручную, в сторонних системах или по расписанию, чтобы все задачи выполнялись в нужное время.

Уменьшите операционные затраты и освободите ИТ-сотрудников и специалистов по разработке и операциям для работы над повышением бизнес-ценности ПО и автоматизации задач управления облаком в службе автоматизации Azure.

## Как служба автоматизации Azure может помочь управлять служебной шиной Azure?

Для управления служебной шиной можно использовать средства службы автоматизации Azure посредством [API REST служебной шины](https://msdn.microsoft.com/library/azure/hh780717.aspx). В службе автоматизации Azure можно выполнять сценарии PowerShell для выполнения многих задач служебной шины с помощью интерфейсов REST API. Вы также можете связать эти вызовы REST API в службе автоматизации Azure с командлетами PowerShell для других служб Azure, чтобы автоматизировать сложные задачи в службах Azure и системах сторонних производителей.

Вот несколько примеров использования PowerShell для управления служебной шиной Azure.

- [Пользовательские командлеты PowerShell для управления очередями служебной шины Azure](https://blogs.technet.microsoft.com/uktechnet/2014/12/04/sample-of-custom-powershell-cmdlets-to-manage-azure-servicebus-queues/)
- [Как создать запросы, разделы и подписки Service Bus с помощью сценария PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
- [Создание пространств имен служебной шины Azure с помощью PowerShell](http://buildazure.com/2015/09/24/create-azure-service-bus-namespaces-using-powershell-and-x-plat-cli/)
- [Модуль с DSCResource для добавления узлов настройки для создания служебной шины Azure](https://www.powershellgallery.com/packages/AzureServiceBusCreation/1.0)

## Дальнейшие действия

Теперь, когда вы познакомились с основами службы автоматизации Azure и способами ее использования для управления служебной шиной Azure, пройдите по ссылкам, чтобы получить дополнительные сведения о службе автоматизации Azure.

* См. [учебник по началу работы](https://azure.microsoft.com/documentation/learning-paths/automation/) со службой автоматизации Azure.
* Узнайте, как [управлять служебной шиной с помощью PowerShell](service-bus-powershell-how-to-provision.md).

<!---HONumber=AcomDC_0413_2016-->