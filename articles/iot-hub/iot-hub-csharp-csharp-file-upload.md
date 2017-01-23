---
title: "Отправка файлов из устройств с помощью Центра Интернета вещей Azure | Документация Майкрософт"
description: "Сведения об отправке файлов с устройства в облако с помощью пакета SDK для устройств Azure IoT для .NET. Отправленные файлы хранятся в контейнере больших двоичных объектов службы хранилища Azure."
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: 4759d229-f856-4526-abda-414f8b00a56d
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: elioda
translationtype: Human Translation
ms.sourcegitcommit: a243e4f64b6cd0bf7b0776e938150a352d424ad1
ms.openlocfilehash: ddc181bde6a154cb3fb35254da6b76a91450ba6b


---
# <a name="upload-files-from-devices-to-the-cloud-with-iot-hub"></a>Передача файлов из устройств в облако в центре IoT
## <a name="introduction"></a>Введение
Центр IoT Azure —это полностью управляемая служба, которая обеспечивает надежный и защищенный двунаправленный обмен данными между миллионами устройств IoT и серверной частью решения. В предыдущих руководствах ([Приступая к работе с центром Azure IoT с использованием .NET] и [Как отправлять сообщения из облака на устройства с помощью центра IoT и .NET]) описаны базовые функции центра Интернета вещей для отправки сообщений с устройства в облако и из облака на устройство, а в руководстве [Как обрабатывать сообщения, отправляемые с устройства центра IoT в облако, с помощью .NET] рассмотрен надежный способ хранения сообщений, отправляемых с устройства в облако, в хранилище BLOB-объектов Azure. Тем не менее в некоторых случаях не просто сопоставить данные, отправляемые устройством, с относительно небольшими сообщениями, отправляемыми из устройства в облако, которые принимает центр IoT. В качестве примеров можно привести большие файлы, содержащие изображения, видео, данные вибрации для с высокой частотой выборки или файлы, содержащие предварительно обработанные данные в какой-либо форме. Эти файлы обычно обрабатываются в виде пакета в облаке с помощью таких инструментов, как [фабрика данных Azure] или стек [Hadoop]. Когда передача файлов из устройства предпочтительнее отправки событий, вы все равно можете использовать функции безопасности и надежности центра IoT.

В этом учебнике используется код учебника [Как отправлять сообщения из облака на устройства с помощью центра IoT и .NET] , чтобы показать, как использовать возможности передачи файлов центра IoT. В нем показано следующее:

- как безопасно предоставить устройству универсальный код ресурса (URI) большого двоичного объекта Azure для передачи файла;
- как использовать уведомления о передаче файлов Центра Интернета вещей для активации обработки файла в серверной части приложения.

В конце этого руководства запускаются два консольных приложения для .NET:

* **SimulatedDevice** — измененная версия приложения, созданного в руководстве [Как отправлять сообщения из облака на устройства с помощью центра IoT и .NET]. Это приложение передает файл в хранилище с помощью универсального кода ресурса (URI) SAS, предоставленного вашим Центром Интернета вещей.
* **ReadFileUploadNotification**— приложение, которое получает уведомления о передаче файлов от центра IoT.

> [!NOTE]
> Существуют пакеты SDK для устройств Azure IoT, обеспечивающие поддержку многих платформ устройств и языков (включая C, Java и JavaScript) в центре IoT. Пошаговые инструкции по подключению устройства к Центру Интернета вещей Azure см. в [Центре разработчика Центра Интернета вещей Azure].
> 
> 

Для работы с этим учебником требуется:

* Microsoft Visual Studio 2015;
* Активная учетная запись Azure. Если ее нет, можно создать [бесплатную учетную запись][lnk-free-trial] всего за несколько минут.

## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Связывание учетной записи хранения Azure с центром IoT
Так как приложение виртуального устройства отправляет файл в большой двоичный объект, необходимо связать учетную запись [хранилища Azure] с Центром Интернета вещей. После этого центр Интернета вещей создаст универсальный код ресурса (URI) SAS, который устройство сможет использовать для безопасной отправки файла в контейнер больших двоичных объектов. Служба центра IoT и пакеты SDK для устройств координируют процесс создания URI SAS, делая его доступным для устройства, которое отправляет файл.

Чтобы связать учетную запись хранения Azure с Центром Интернета вещей, следуйте инструкциям в статье о [настройке отправки файлов с помощью портала Azure][lnk-configure-upload].

## <a name="upload-a-file-from-a-simulated-device-app"></a>Передача файла из приложения виртуального устройства
В этом разделе вы измените приложение виртуального устройства, созданное в руководстве [Как отправлять сообщения из облака на устройства с помощью центра IoT и .NET], для получения сообщений из облака на устройство от Центра Интернета вещей.

1. В Visual Studio щелкните правой кнопкой мыши проект **SimulatedDevice**, а затем последовательно выберите **Добавить** и **Существующий элемент**. Перейдите к файлу образа и добавьте его в проект. В этом учебнике используется образ `image.jpg`.
2. Щелкните его правой кнопкой мыши и выберите пункт **Свойства**. Убедитесь, что параметр **Копировать в выходной каталог** имеет значение **Всегда копировать**.
   
    ![][1]
3. Добавьте в начало файла **Program.cs** следующие инструкции:
   
        using System.IO;
4. Добавьте следующий метод в класс **Program** .
   
        private static async void SendToBlobAsync()
        {
            string fileName = "image.jpg";
            Console.WriteLine("Uploading file: {0}", fileName);
            var watch = System.Diagnostics.Stopwatch.StartNew();
   
            using (var sourceData = new FileStream(@"image.jpg", FileMode.Open))
            {
                await deviceClient.UploadToBlobAsync(fileName, sourceData);
            }
   
            watch.Stop();
            Console.WriteLine("Time to upload file: {0}ms\n", watch.ElapsedMilliseconds);
        }
   
    Метод `UploadToBlobAsync` принимает имя передаваемого файла и источник его передачи и обрабатывает передачу в хранилище. Консольное приложение отображает время, необходимое для отправки файла.
5. Добавьте в метод **Main** непосредственно перед строкой `Console.ReadLine()` следующий метод:
   
        SendToBlobAsync();

> [!NOTE]
> Для упрощения в этом руководстве не реализуются какие-либо политики повтора. В рабочем коде следует реализовать политики повтора (например, экспоненциальную задержку), как указано в статье MSDN [Transient Fault Handling](Обработка временного сбоя).
> 
> 

## <a name="receive-a-file-upload-notification"></a>Получение уведомления о передачи файла
В этом разделе вам предстоит создать консольное приложение для .NET, которое получает уведомления об отправке файлов из Центра Интернета вещей.

1. В текущем решении Visual Studio создайте проект Windows на языке Visual C#, используя шаблон проекта **Консольное приложение** . Назовите проект **ReadFileUploadNotification**.
   
    ![Создание проекта в Visual Studio][2]
2. В обозревателе решений щелкните правой кнопкой мыши проект **ReadFileUploadNotification** и выберите пункт **Управление пакетами NuGet**.
   
    Это действие откроет окно "Управление пакетами NuGet".
3. Выполните поиск `Microsoft.Azure.Devices`и щелкните **Установить**, после чего примите условия использования. 
   
    После этого действия будет скачан и установлен [пакет SDK NuGet для служб Azure IoT] и добавлены ссылки на него в проект **ReadFileUploadNotification**.

4. Добавьте в начало файла **Program.cs** следующие инструкции:
   
        using Microsoft.Azure.Devices;
5. Добавьте следующие поля в класс **Program** . Замените значение заполнителя строкой подключения центра Интернета вещей из раздела [Приступая к работе с центром Azure IoT с использованием .NET].
   
        static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";
6. Добавьте следующий метод в класс **Program** .
   
        private async static Task ReceiveFileUploadNotificationAsync()
        {
            var notificationReceiver = serviceClient.GetFileNotificationReceiver();
   
            Console.WriteLine("\nReceiving file upload notification from service");
            while (true)
            {
                var fileUploadNotification = await notificationReceiver.ReceiveAsync();
                if (fileUploadNotification == null) continue;
   
                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received file upload noticiation: {0}", string.Join(", ", fileUploadNotification.BlobName));
                Console.ResetColor();
   
                await notificationReceiver.CompleteAsync(fileUploadNotification);
            }
        }
   
    Обратите внимание, что шаблон получения здесь аналогичен шаблону, использовавшемуся для получения сообщений из облака на устройство из приложения устройства.
7. Наконец, добавьте следующие строки в метод **Main** :
   
        Console.WriteLine("Receive file upload notifications\n");
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
        ReceiveFileUploadNotificationAsync().Wait();
        Console.ReadLine();

## <a name="run-the-applications"></a>Запуск приложений
Теперь все готово к запуску приложений.

1. В Visual Studio щелкните правой кнопкой мыши свое решение и выберите пункт **Назначить запускаемые проекты**. Выберите **Несколько запускаемых проектов**, а затем выберите действие **Запуск** для обоих приложений — **ReadFileUploadNotification** и **SimulatedDevice**.
2. Нажмите клавишу **F5**. Должны запуститься оба приложения. Вы должны увидеть, как завершится передача в одном консольном приложении и как второе консольное приложение получит уведомление о передаче. Наличие отправленного файла в учетной записи хранения Azure можно проверить с помощью [портала Azure] или обозревателя серверов Visual Studio.
   
   ![][50]

## <a name="next-steps"></a>Дальнейшие действия
В этом учебнике было показано, как использовать возможности передачи файлов центра IoT, чтобы упросить передачу файлов из устройств. Изучение функций и сценариев Центра Интернета вещей можно продолжить в следующих руководствах:

* [Создание Центра Интернета вещей с помощью PowerShell][lnk-create-hub]
* [Знакомство с пакетом SDK для устройств Центра Интернета вещей Azure для C][lnk-c-sdk]
* [IoT Hub SDKs][lnk-sdks] (Пакеты SDK для Центра Интернета вещей)

Для дальнейшего изучения возможностей центра IoT см. следующие статьи:

* [Пакет SDK для шлюза Интернета вещей (бета-версия): отправка сообщений с устройства в облако через виртуальное устройство с помощью Linux][lnk-gateway]

<!-- Images. -->

[50]: ./media/iot-hub-csharp-csharp-file-upload/run-apps1.png
[1]: ./media/iot-hub-csharp-csharp-file-upload/image-properties.png
[2]: ./media/iot-hub-csharp-csharp-file-upload/create-identity-csharp1.png

<!-- Links -->

[портала Azure]: https://portal.azure.com/

[фабрика данных Azure]: https://azure.microsoft.com/documentation/services/data-factory/
[Hadoop]: https://azure.microsoft.com/documentation/services/hdinsight/

[Как отправлять сообщения из облака на устройства с помощью центра IoT и .NET]: iot-hub-csharp-csharp-c2d.md
[Как обрабатывать сообщения, отправляемые с устройства центра IoT в облако, с помощью .NET]: iot-hub-csharp-csharp-process-d2c.md
[Приступая к работе с центром Azure IoT с использованием .NET]: iot-hub-csharp-csharp-getstarted.md
[Центре разработчика Центра Интернета вещей Azure]: http://www.azure.com/develop/iot

[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[хранилища Azure]: ../storage/storage-create-storage-account.md#create-a-storage-account
[lnk-configure-upload]: iot-hub-configure-file-upload.md
[пакет SDK NuGet для служб Azure IoT]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md





<!--HONumber=Dec16_HO1-->


