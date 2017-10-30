---
title: "Обзор интерфейсов API ретранслятора Azure для платформы .NET Standard | Документация Майкрософт"
description: "Обзор интерфейсов API ретранслятора Azure для .NET Standard."
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: b1da9ac1-811b-4df7-a22c-ccd013405c40
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: sethm
ms.openlocfilehash: 58451bae409c74c319f41c38a1cec5f051619e0c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-relay-hybrid-connections-net-standard-api-overview"></a>Обзор API-интерфейсов гибридных подключений ретранслятора Azure для платформы .NET Standard

В этой статье перечислены некоторые ключевые [клиентские API-интерфейсы](/dotnet/api/microsoft.azure.relay) гибридных подключений ретранслятора Azure для платформы .NET Standard.
  
## <a name="relay-connection-string-builder"></a>Построитель строк подключения ретранслятора

Класс [RelayConnectionStringBuilder][RelayConnectionStringBuilder] форматирует строки подключения, которые относятся к гибридным подключениям ретранслятора. Его можно использовать для проверки формата строки подключения, а также для создания строки подключения с нуля. В качестве примера ниже приведен код.

```csharp
var endpoint = "{Relay namespace}";
var entityPath = "{Name of the Hybrid Connection}";
var sharedAccessKeyName = "{SAS key name}";
var sharedAccessKey = "{SAS key value}";

var connectionStringBuilder = new RelayConnectionStringBuilder()
{
    Endpoint = endpoint,
    EntityPath = entityPath,
    SharedAccessKeyName = sasKeyName,
    SharedAccessKey = sasKeyValue
};
```

Вы также можете передать строку подключения непосредственно в метод `RelayConnectionStringBuilder`. Эта операция позволит убедиться, что строка подключения имеет допустимый формат. Если какой-либо из параметров окажется недоступным, конструктор выдаст `ArgumentException`.

```csharp
var myConnectionString = "{RelayConnectionString}";
// Declare the connectionStringBuilder so that it can be used outside of the loop if needed
RelayConnectionStringBuilder connectionStringBuilder;
try
{
    // Create the connectionStringBuilder using the supplied connection string
    connectionStringBuilder = new RelayConnectionStringBuilder(myConnectionString);
}
catch (ArgumentException ae)
{
    // Perform some error handling
}
```

## <a name="hybrid-connection-stream"></a>Поток гибридного подключения
Класс [HybridConnectionStream][HCStream] является основным объектом, с помощью которого выполняется отправка и получение данных через конечную точку ретранслятора Azure независимо от того, что используется —[HybridConnectionClient][HCClient] или [HybridConnectionListener][HCListener].

### <a name="getting-a-hybrid-connection-stream"></a>Получение потока гибридного подключения

#### <a name="listener"></a>Прослушиватель
С помощью [HybridConnectionListener][HCListener] можно получить объект `HybridConnectionStream` следующим образом:

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var listener = new HybridConnectionListener(csb.ToString());
// Open a connection to the Relay endpoint
await listener.OpenAsync();
// Get a `HybridConnectionStream`
var hybridConnectionStream = await listener.AcceptConnectionAsync();
```

#### <a name="client"></a>Клиент
С помощью [HybridConnectionClient][HCClient] можно получить объект `HybridConnectionStream` следующим образом:

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var client = new HybridConnectionClient(csb.ToString());
// Open a connection to the Relay endpoint and get a `HybridConnectionStream`
var hybridConnectionStream = await client.CreateConnectionAsync();
```

### <a name="receiving-data"></a>Получение данных
Класс [HybridConnectionStream][HCStream] можно использовать для двустороннего обмена данными. В большинстве случаев вы будете непрерывно получать данные из потока. Если выполняется чтение текста из потока, вам также может потребоваться использовать объект [StreamReader](https://msdn.microsoft.com/library/system.io.streamreader(v=vs.110).aspx) для упрощения анализа данных. Например, можно считывать данные как текст, а не как `byte[]`.

Следующий код считывает отдельные строки текста из потока, пока не будет запрошена отмена.

```csharp
// Create a CancellationToken, so that we can cancel the while loop
var cancellationToken = new CancellationToken();
// Create a StreamReader from the 'hybridConnectionStream`
var streamReader = new StreamReader(hybridConnectionStream);

while (!cancellationToken.IsCancellationRequested)
{
    // Read a line of input until a newline is encountered
    var line = await streamReader.ReadLineAsync();
    if (string.IsNullOrEmpty(line))
    {
        // If there's no input data, we will signal that 
        // we will no longer send data on this connection
        // and then break out of the processing loop.
        await hybridConnectionStream.ShutdownAsync(cancellationToken);
        break;
    }
}
```

### <a name="sending-data"></a>Отправка данных
Когда подключение будет установлено, можно отправить сообщение на конечную точку ретранслятора. Так как объект подключения наследует [поток](https://msdn.microsoft.com/library/system.io.stream(v=vs.110).aspx), отправляйте данные в виде `byte[]`. В приведенном ниже примере показано, как это сделать.

```csharp
var data = Encoding.UTF8.GetBytes("hello");
await clientConnection.WriteAsync(data, 0, data.Length);
```

Однако если вы хотите отправить текст напрямую, без необходимости кодирования этих строк каждый раз, можно поместить объект `hybridConnectionStream` в объект [StreamWriter](https://msdn.microsoft.com/library/system.io.streamwriter(v=vs.110).aspx).

```csharp
// The StreamWriter object only needs to be created once
var textWriter = new StreamWriter(hybridConnectionStream);
await textWriter.WriteLineAsync("hello");
```

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о ретрансляторе Azure доступны по следующим ссылкам:

* [Microsoft.Azure.Relay Namespace](/dotnet/api/microsoft.azure.relay) (Пространство имен Microsoft.Azure.Relay)
* [Что такое ретранслятор Azure?](relay-what-is-it.md)
* [Available Relay APIs](relay-api-overview.md) (Доступные API-интерфейсы ретранслятора)

[RelayConnectionStringBuilder]: /dotnet/api/microsoft.azure.relay.relayconnectionstringbuilder
[HCStream]: /dotnet/api/microsoft.azure.relay.hybridconnectionstream
[HCClient]: /dotnet/api/microsoft.azure.relay.hybridconnectionclient
[HCListener]: /dotnet/api/microsoft.azure.relay.hybridconnectionlistener