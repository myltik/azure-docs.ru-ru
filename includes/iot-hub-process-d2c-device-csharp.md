## Отправка интерактивных сообщений из виртуального устройства

В этом разделе приложение виртуального устройства будет изменено для отправки интерактивных сообщений, передаваемых из устройства в облако, в центр IoT.

1. В Visual Studio в проекте **SimulatedDevice** добавьте в класс **Program** следующий метод:
   
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

                Thread.Sleep(10000);
            }
        }

    Он очень похож на метод `SendDeviceToCloudMessagesAsync()`, который был создан в разделе [Начало работы с центром IoT]. Отличие лишь в том, что системное свойство `MessageId` и свойство пользователя с именем `messageType` не установлены. Свойству `MessageId` задан глобальный уникальный идентификатор (GUID), который будет использоваться для дедупликации полученных сообщений. Свойство `messageType` используется для распознавания интерактивных сообщений от сообщений точки данных. Эти сведения передаются в свойствах сообщения, а не в его основной части, чтобы обработчику событий на стороне сервера не требовалось десериализовать целое сообщение ради выполнения маршрутизации.

> [AZURE.NOTE]Важно, что `MessageId`, используемое для дедупликации интерактивных сообщений, создается на устройстве, так как временные ошибки связи (или другие сбои) могут привести к нескольким повторным передачам одного сообщения с устройства. В отличие от GUID, семантический идентификатор сообщения (например, хэш полей данных релевантного сообщения) также должен использоваться.

2. Добавьте в метод **Main** прямо перед строкой `Console.ReadLine()` следующий метод:

        SendDeviceToCloudInteractiveMessagesAsync();

> [AZURE.NOTE]Для упрощения в этом учебнике не реализуются какие-либо политики повтора. В рабочем коде рекомендуется реализовать политики повтора (например, экспоненциальную задержку), как это указано в статье MSDN [Обработка временного сбоя].

<!-- Links -->
[Начало работы с центром IoT]: iot-hub-csharp-csharp-getstarted.md
[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d

<!-- Images -->
[10]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp1.png
[12]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp3.png

[20]: ./media/iot-hub-getstarted-cloud-csharp/create-storage1.png
[21]: ./media/iot-hub-getstarted-cloud-csharp/create-storage2.png
[22]: ./media/iot-hub-getstarted-cloud-csharp/create-storage3.png

<!---HONumber=Oct15_HO1-->