## Отправка интерактивных сообщений из виртуального устройства

В этом разделе приложение виртуального устройства, созданное в руководстве [Приступая к работе с центром IoT], будет изменено для отправки интерактивных сообщений, передаваемых из устройства в облако, в центр IoT.

1. В Visual Studio в проекте **SimulatedDevice** добавьте в класс **Program** следующий метод:

    ```
    private static async void SendDeviceToCloudInteractiveMessagesAsync()
    {
      while (true)
      {
        var interactiveMessageString = "Alert message!";
        var interactiveMessage = new Message(Encoding.ASCII.GetBytes(interactiveMessageString));
        interactiveMessage.Properties["messageType"] = "interactive";
        interactiveMessage.MessageId = Guid.NewGuid().ToString();

        await deviceClient.SendEventAsync(interactiveMessage);
        Console.WriteLine("{0} > Sending interactive message: {1}", DateTime.Now, interactiveMessageString);

        Task.Delay(10000).Wait();
      }
    }
    ```

    Этот метод очень похож на метод **SendDeviceToCloudMessagesAsync** в проекте **SimulatedDevice**. Единственными отличиями являются свойство **MessageId**, которое теперь устанавливается, и пользовательское свойство **messageType**. Код присваивает глобальный уникальный идентификатор \(guid\) свойству **MessageId**, которое служебная шина может использовать для дедупликации получаемых сообщений. В примере используется свойство **messageType**, чтобы отличить интерактивные сообщения от сообщений точки данных. Приложение передает эти сведения в свойствах сообщения, а не в его основной части, чтобы обработчику событий не требовалось десериализовывать сообщение для маршрутизации.

    > [AZURE.NOTE] Важно создать идентификатор сообщения **MessageId**, используемый для дедупликации интерактивных сообщений в коде устройства, так как временные ошибки связи \(или другие сбои\) могут привести к нескольким повторным передачам одного и того же сообщения с этого устройства. Вместо идентификатора guid также можно использовать семантический идентификатор сообщения — такой как хэш полей данных соответствующего сообщения.

2. Добавьте в метод **Main** прямо перед строкой `Console.ReadLine()` следующий метод:

    ````
    SendDeviceToCloudInteractiveMessagesAsync();
    ````

    > [AZURE.NOTE] Для упрощения в этом руководстве не реализуются какие-либо политики повтора. В рабочем коде следует реализовать политику повтора \(например, экспоненциальную задержку\), как предлагается в статье MSDN [Transient Fault Handling].

<!-- Links -->
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh675232.aspx
[Приступая к работе с центром IoT]: ../articles/iot-hub/iot-hub-csharp-csharp-getstarted.md

<!---HONumber=AcomDC_0413_2016-->