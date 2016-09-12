.<properties
   pageTitle="Распространенные исключения FabricClient | Microsoft Azure"
   description="Описание распространенных исключений и ошибок, которые могут выдаваться интерфейсами API FabricClient при управлении приложениями и кластерами."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/25/2016"
   ms.author="ryanwi"/>

# Распространенные исключения и ошибки при работе с интерфейсами API FabricClient
Интерфейсы API [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) позволяют администраторам кластеров и приложений выполнять административные задачи в приложении, службе или кластере Service Fabric. К ним могут относиться развертывание приложений, обновление и удаление приложений, проверка работоспособности кластера или тестирование службы. Разработчики приложений и администраторы кластера могут использовать API FabricClient для разработки средств управления кластером и приложениями Service Fabric.

С помощью FabricClient можно выполнять операции различных типов. Каждый метод может выдавать исключения из-за неправильных входных данных, ошибок времени выполнения или временных проблем инфраструктуры. Чтобы узнать, какие исключения выдаются определенным методом, обратитесь к справочной документации по API. При этом некоторые исключения могут выдаваться различными интерфейсами API [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx). В следующей таблице перечислены исключения, которые являются общими для всех интерфейсов API FabricClient.

|Исключение| Вызывается, когда|
|---------|:-----------|
|[System.Fabric.FabricObjectClosedException](https://msdn.microsoft.com/library/system.fabric.fabricobjectclosedexception.aspx)|Объект [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) находится в закрытом состоянии. Удалите используемый объект [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) и создайте новый экземпляр объекта [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx). |
|[System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx)|Истекло время ожидания операции. Исключение [OperationTimedOut](https://msdn.microsoft.com/library/system.fabric.fabricerrorcode.aspx) возвращается, если время, необходимое для завершения операции, превышает MaxOperationTimeout.|
|[System.UnauthorizedAccessException](https://msdn.microsoft.com/ru-RU/library/system.unauthorizedaccessexception.aspx)|Не удалось выполнить проверку доступа для операции. Возвращается E\_ACCESSDENIED.|
|[System.Fabric.FabricException](https://msdn.microsoft.com/library/system.fabric.fabricexception.aspx)|При выполнении операции произошла ошибка времени выполнения. Любой из методов FabricClient может вызвать исключение [FabricException](https://msdn.microsoft.com/library/system.fabric.fabricexception.aspx); причина этого исключения определяется [ErrorCode](https://msdn.microsoft.com/library/system.fabric.fabricexception.errorcode.aspx). Коды ошибок определяются в перечислении [FabricErrorCode](https://msdn.microsoft.com/library/system.fabric.fabricerrorcode.aspx).|
|[System.Fabric.FabricTransientException](https://msdn.microsoft.com/library/system.fabric.fabrictransientexception.aspx)|Не удалось выполнить операцию из-за временной ошибки какого-либо вида. Например, операция может завершиться ошибкой, так как кворум реплик временно недоступен. Временные исключения соответствуют невыполненным операциям, которые могут быть выполнены повторно.|

Некоторые наиболее распространенные ошибки [FabricErrorCode](https://msdn.microsoft.com/library/system.fabric.fabricerrorcode.aspx), которые могут быть возвращены в [FabricException](https://msdn.microsoft.com/library/system.fabric.fabricexception.aspx), включают:

|Ошибка| Условие|
|---------|:-----------|
|CommunicationError|Ошибка связи привела к сбою операции; повторите операцию.|
|InvalidCredentialType|Недопустимый тип учетных данных.|
|InvalidX509FindType|Недопустимый тип поиска X509.|
|InvalidX509StoreLocation|Недопустимое расположение хранилища X509.|
|InvalidX509StoreName|Недопустимое имя хранилища X509.|
|InvalidX509Thumbprint|Недопустимая строка отпечатка сертификата X509.|
|InvalidProtectionLevel|Недопустимый уровень защиты.|
|InvalidX509Store|Не удается открыть хранилище сертификатов X509.|
|InvalidSubjectName|Недопустимое имя субъекта.|
|InvalidAllowedCommonNameList|Недопустимый формат строки списка общих имен. Это должен быть список с разделителями-запятыми.|

<!---HONumber=AcomDC_0831_2016-->