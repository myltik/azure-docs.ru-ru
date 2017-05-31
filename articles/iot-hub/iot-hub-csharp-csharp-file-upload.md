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
ms.date: 03/08/2017
ms.author: elioda
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: 0fc61b9f0f99055ec99383f6ac32f5fff762fa84
ms.contentlocale: ru-ru
ms.lasthandoff: 05/16/2017


---
# <a name="upload-files-from-your-simulated-device-to-the-cloud-with-iot-hub"></a>Передача файлов с виртуального устройства в облако в Центре Интернета вещей
## <a name="introduction"></a>Введение
Центр IoT Azure —это полностью управляемая служба, которая обеспечивает надежный и защищенный двунаправленный обмен данными между миллионами устройств IoT и серверной частью решения. В руководствах [Подключение виртуального устройства к Центру Интернета вещей с помощью .NET] и [Отправка сообщений из облака на устройство с помощью Центра Интернета вещей (.NET)] описаны базовые функции Центра Интернета вещей для обмена сообщениями между устройством и облаком. В статье [Руководство. Как обрабатывать сообщения, отправляемые с устройства Центра Интернета вещей в облако, с помощью .NET] описан способ надежного хранения сообщений, отправляемых с устройства в облако, в хранилище больших двоичных объектов. Тем не менее в некоторых случаях не просто сопоставить данные, отправляемые устройством, с относительно небольшими сообщениями, отправляемыми из устройства в облако, которые принимает центр IoT. Например, большие файлы, содержащие изображения, видео, данные вибрации с высокой частотой выборки или предварительно обработанные данные в какой-либо форме. Эти файлы обычно обрабатываются в виде пакета в облаке с помощью таких инструментов, как [фабрика данных Azure] или стек [Hadoop]. Когда передача файлов из устройства предпочтительнее отправки событий, вы все равно можете использовать функции безопасности и надежности центра IoT.

В этом учебнике используется код учебника [Отправка сообщений из облака на устройство с помощью Центра Интернета вещей (.NET)] , чтобы показать, как использовать возможности передачи файлов центра IoT. В нем показано следующее:

- как безопасно предоставить устройству универсальный код ресурса (URI) большого двоичного объекта Azure для передачи файла;
- как использовать уведомления о передаче файлов Центра Интернета вещей для активации обработки файла в серверной части приложения.

В конце этого руководства запускаются два консольных приложения для .NET:

* **SimulatedDevice** — измененная версия приложения, созданного в руководстве [Отправка сообщений из облака на устройство с помощью Центра Интернета вещей (.NET)]. Это приложение передает файл в хранилище с помощью универсального кода ресурса (URI) SAS, предоставленного вашим Центром Интернета вещей.
* **ReadFileUploadNotification**— приложение, которое получает уведомления о передаче файлов от центра IoT.

> [!NOTE]
> Существуют пакеты SDK для устройств Azure IoT, обеспечивающие поддержку многих платформ устройств и языков (включая C, Java и JavaScript) в центре IoT. Пошаговые инструкции по подключению устройства к Центру Интернета вещей Azure см. в [Центре разработчика Центра Интернета вещей Azure].
> 
> 

Для работы с этим учебником требуется:

* Visual Studio 2015 или Visual Studio 2017
* Активная учетная запись Azure. Если ее нет, можно создать [бесплатную учетную запись][lnk-free-trial] всего за несколько минут.

## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Связывание учетной записи хранения Azure с центром IoT
Так как приложение виртуального устройства отправляет файл в большой двоичный объект, необходимо связать учетную запись [хранилища Azure] с Центром Интернета вещей. При связывании учетной записи хранения Azure с Центром Интернета вещей Центр Интернета вещей создает URI SAS. Устройство может использовать этот URI SAS для безопасной передачи файла в контейнер больших двоичных объектов. Служба центра IoT и пакеты SDK для устройств координируют процесс создания URI SAS, делая его доступным для устройства, которое отправляет файл.

Чтобы связать учетную запись хранения Azure с Центром Интернета вещей, следуйте инструкциям в статье о [настройке отправки файлов с помощью портала Azure][lnk-configure-upload]. Убедитесь, что контейнер больших двоичных объектов связан с вашим Центром Интернета вещей и что уведомления файлов включены. 
   
![Включение уведомлений файлов на портале][3]


## <a name="upload-a-file-from-a-simulated-device-app"></a>Передача файла из приложения виртуального устройства
В этом разделе вы измените приложение виртуального устройства, созданное в руководстве [Отправка сообщений из облака на устройство с помощью Центра Интернета вещей (.NET)], для получения сообщений из облака на устройство от Центра Интернета вещей.

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
       
3. В окне **Диспетчер пакетов NuGet** найдите **Microsoft.Azure.Devices**, а затем щелкните **Установить** и примите условия использования. 
   
    После этого действия будет скачан и установлен [пакет SDK NuGet для служб Azure IoT] и добавлены ссылки на него в проект **ReadFileUploadNotification**.

4. Добавьте в начало файла **Program.cs** следующие инструкции:
   
        using Microsoft.Azure.Devices;
5. Добавьте следующие поля в класс **Program** . Замените значение заполнителя строкой подключения центра Интернета вещей из раздела [Подключение виртуального устройства к Центру Интернета вещей с помощью .NET].
   
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
2. Нажмите клавишу **F5**. Должны запуститься оба приложения. Вы должны увидеть, как завершится передача в одном консольном приложении и как другое консольное приложение получит уведомление о передаче. Наличие отправленного файла в учетной записи хранения Azure можно проверить с помощью [портала Azure] или обозревателя серверов Visual Studio.
   
   ![][50]

## <a name="next-steps"></a>Дальнейшие действия
В этом руководство показано, как использовать возможности передачи файлов Центра Интернета вещей, чтобы упростить передачу файлов из устройств. Изучение функций и сценариев Центра Интернета вещей можно продолжить в следующих руководствах:

* [Создание Центра Интернета вещей с помощью PowerShell][lnk-create-hub]
* [Знакомство с пакетом SDK для устройств Центра Интернета вещей Azure для C][lnk-c-sdk]
* [IoT Hub SDKs][lnk-sdks] (Пакеты SDK для Центра Интернета вещей)

Для дальнейшего изучения возможностей центра IoT см. следующие статьи:

* [Simulating a device with IoT Edge][lnk-iotedge] (Моделирование устройства с помощью Edge Интернета вещей)

<!-- Images. -->

[50]: ./media/iot-hub-csharp-csharp-file-upload/run-apps1.png
[1]: ./media/iot-hub-csharp-csharp-file-upload/image-properties.png
[2]: ./media/iot-hub-csharp-csharp-file-upload/file-upload-project-csharp1.png
[3]: ./media/iot-hub-csharp-csharp-file-upload/enable-file-notifications.png

<!-- Links -->

[портала Azure]: https://portal.azure.com/

[фабрика данных Azure]: https://azure.microsoft.com/documentation/services/data-factory/
[Hadoop]: https://azure.microsoft.com/documentation/services/hdinsight/

[Отправка сообщений из облака на устройство с помощью Центра Интернета вещей (.NET)]: iot-hub-csharp-csharp-c2d.md
[Руководство. Как обрабатывать сообщения, отправляемые с устройства Центра Интернета вещей в облако, с помощью .NET]: iot-hub-csharp-csharp-process-d2c.md
[Подключение виртуального устройства к Центру Интернета вещей с помощью .NET]: iot-hub-csharp-csharp-getstarted.md
[Центре разработчика Центра Интернета вещей Azure]: http://www.azure.com/develop/iot

[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[хранилища Azure]: ../storage/storage-create-storage-account.md#create-a-storage-account
[lnk-configure-upload]: iot-hub-configure-file-upload.md
[пакет SDK NuGet для служб Azure IoT]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: iot-hub-windows-iot-edge-simulated-device.md



