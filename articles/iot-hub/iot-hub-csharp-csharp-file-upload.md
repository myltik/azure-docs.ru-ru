---
title: Отправка файлов с устройств в Центр Интернета вещей Azure | Документация Майкрософт
description: Сведения об отправке файлов с устройства в облако с помощью пакета SDK для устройств Azure IoT для .NET. Отправленные файлы хранятся в контейнере больших двоичных объектов службы хранилища Azure.
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: ''
ms.assetid: 4759d229-f856-4526-abda-414f8b00a56d
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/04/2017
ms.author: elioda
ms.openlocfilehash: 901b4b6c631d47a6c37eb232f66d8350faa9be76
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-using-net"></a>Передача файлов с устройства в облако с помощью Центра Интернета вещей и .NET

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

В этом учебнике используется код учебника [Отправка сообщений из облака на устройства с помощью Центра Интернета вещей](iot-hub-csharp-csharp-c2d.md), чтобы показать, как использовать возможности передачи файлов Центра Интернета вещей. В нем показано следующее:

- как безопасно предоставить устройству универсальный код ресурса (URI) большого двоичного объекта Azure для передачи файла;
- как использовать уведомления о передаче файлов Центра Интернета вещей для активации обработки файла в серверной части приложения.

В руководствах [Подключение устройства к Центру Интернета вещей с помощью .NET](iot-hub-csharp-csharp-getstarted.md) и [Отправка сообщений из облака в виртуальное устройство с помощью Центра Интернета вещей (.NET)](iot-hub-csharp-csharp-c2d.md) описаны базовые функции Центра Интернета вещей для обмена сообщениями между устройством и облаком. В статье [Руководство. Как обрабатывать сообщения, отправляемые с устройства Центра Интернета вещей в облако, с помощью .NET](iot-hub-csharp-csharp-process-d2c.md) описан способ надежного хранения сообщений, отправляемых с устройства в облако, в хранилище больших двоичных объектов. Тем не менее в некоторых случаях не просто сопоставить данные, отправляемые устройством, с относительно небольшими сообщениями, отправляемыми из устройства в облако, которые принимает Центр Интернета вещей. Например: 

* Большие файлы, содержащие изображения.
* Видеоролики
* Данные вибрации с высокой частотой выборки.
* Некоторые виды предварительно обработанных данных.

Эти файлы обычно обрабатываются в виде пакета в облаке с помощью таких инструментов, как [фабрика данных Azure](../data-factory/introduction.md) или стек [Hadoop](../hdinsight/index.yml). При передаче файлов с устройства вы можете рассчитывать на безопасность и надежность Центра Интернета вещей.

В конце этого руководства запускаются два консольных приложения для .NET:

* **SimulatedDevice** — измененная версия приложения, созданного в руководстве [Как отправлять сообщения из облака на устройства с помощью Центра Интернета вещей и .NET](iot-hub-csharp-csharp-c2d.md). Это приложение передает файл в хранилище с помощью универсального кода ресурса (URI) SAS, предоставленного вашим Центром Интернета вещей.
* **ReadFileUploadNotification**— приложение, которое получает уведомления о передаче файлов от Центра Интернета вещей.

> [!NOTE]
> Существуют пакеты SDK для устройств Azure IoT, обеспечивающие поддержку многих платформ устройств и языков (включая C, Java и JavaScript) в Центре Интернета вещей. Пошаговые инструкции по подключению устройства к Центру Интернета вещей Azure см. в [Центре разработчика для Центра Интернета вещей Azure].

Для работы с этим учебником требуется:

* Visual Studio 2015 или Visual Studio 2017
* Активная учетная запись Azure. Если ее нет, можно создать [бесплатную учетную запись][lnk-free-trial] всего за несколько минут.

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Передача файла из приложения устройства

В этом разделе вы измените приложение устройства, созданное в руководстве [Отправка сообщений из облака в виртуальное устройство с помощью Центра Интернета вещей (.NET)](iot-hub-csharp-csharp-c2d.md), чтобы с помощью Центра Интернета вещей передавать сообщения из облака на устройство.

1. В Visual Studio щелкните правой кнопкой мыши проект **SimulatedDevice**, а затем последовательно выберите **Добавить** и **Существующий элемент**. Перейдите к файлу образа и добавьте его в проект. В этом учебнике используется образ `image.jpg`.

1. Щелкните его правой кнопкой мыши и выберите пункт **Свойства**. Убедитесь, что параметр **Копировать в выходной каталог** имеет значение **Всегда копировать**.

    ![][1]

1. Добавьте в начало файла **Program.cs** следующие инструкции:

    ```csharp
    using System.IO;
    ```

1. Добавьте следующий метод в класс **Program** .

    ```csharp
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
    ```

    Метод `UploadToBlobAsync` принимает имя передаваемого файла и источник его передачи и обрабатывает передачу в хранилище. Консольное приложение отображает время, необходимое для отправки файла.

1. Добавьте в метод **Main** непосредственно перед строкой `Console.ReadLine()` следующий метод:

    ```csharp
    SendToBlobAsync();
    ```

> [!NOTE]
> Для упрощения в этом руководстве не реализуются какие-либо политики повтора. В рабочем коде следует реализовать политики повтора (например, экспоненциальную задержку), как указано в статье MSDN [Обработка временного сбоя].

## <a name="receive-a-file-upload-notification"></a>Получение уведомления о передачи файла

В этом разделе вам предстоит создать консольное приложение для .NET, которое получает уведомления об отправке файлов из Центра Интернета вещей.

1. В текущем решении Visual Studio создайте проект Windows на языке Visual C#, используя шаблон проекта **Консольное приложение** . Назовите проект **ReadFileUploadNotification**.

    ![Создание проекта в Visual Studio][2]

1. В обозревателе решений щелкните правой кнопкой мыши проект **ReadFileUploadNotification** и выберите пункт **Управление пакетами NuGet**.

1. В окне **Диспетчер пакетов NuGet** найдите **Microsoft.Azure.Devices**, а затем щелкните **Установить** и примите условия использования.

    После этого действия будет скачан и установлен [пакет SDK NuGet для служб Azure IoT] и добавлены ссылки на него в проект **ReadFileUploadNotification**.

1. Добавьте в начало файла **Program.cs** следующие инструкции:

    ```csharp
    using Microsoft.Azure.Devices;
    ```

1. Добавьте следующие поля в класс **Program** . Замените значение заполнителя строкой подключения центра Интернета вещей из статьи "Подключение устройства к Центру Интернета вещей с помощью .NET".

    ```csharp
    static ServiceClient serviceClient;
    static string connectionString = "{iot hub connection string}";
    ```

1. Добавьте следующий метод в класс **Program** .

    ```csharp
    private async static void ReceiveFileUploadNotificationAsync()
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
    ```

    Обратите внимание, что шаблон получения здесь аналогичен шаблону, использовавшемуся для получения сообщений из облака на устройство из приложения устройства.

1. Наконец, добавьте следующие строки в метод **Main** :

    ```csharp
    Console.WriteLine("Receive file upload notifications\n");
    serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
    ReceiveFileUploadNotificationAsync();
    Console.WriteLine("Press Enter to exit\n");
    Console.ReadLine();
    ```

## <a name="run-the-applications"></a>Запуск приложений

Теперь все готово к запуску приложений.

1. В Visual Studio щелкните правой кнопкой мыши свое решение и выберите пункт **Назначить запускаемые проекты**. Выберите **Несколько запускаемых проектов**, а затем выберите действие **Запуск** для обоих приложений — **ReadFileUploadNotification** и **SimulatedDevice**.

1. Нажмите клавишу **F5**. Должны запуститься оба приложения. Вы должны увидеть, как завершится передача в одном консольном приложении и как другое консольное приложение получит уведомление о передаче. Наличие отправленного файла в учетной записи хранения Azure можно проверить с помощью [портала Azure] или обозревателя серверов Visual Studio.

    ![][50]

## <a name="next-steps"></a>Дополнительная информация

В этом руководство показано, как использовать возможности передачи файлов Центра Интернета вещей, чтобы упростить передачу файлов из устройств. Изучение функций и сценариев Центра Интернета вещей можно продолжить в следующих руководствах:

* [Создание Центра Интернета вещей с помощью PowerShell][lnk-create-hub]
* [Знакомство с пакетом SDK для устройств Центра Интернета вещей Azure для C][lnk-c-sdk]
* [IoT Hub SDKs][lnk-sdks] (Пакеты SDK для Центра Интернета вещей)

Для дальнейшего изучения возможностей Центра Интернета вещей см. следующие статьи:

* [Развертывание ИИ на пограничных устройствах с использованием Azure IoT Edge][lnk-iotedge]

<!-- Images. -->

[50]: ./media/iot-hub-csharp-csharp-file-upload/run-apps1.png
[1]: ./media/iot-hub-csharp-csharp-file-upload/image-properties.png
[2]: ./media/iot-hub-csharp-csharp-file-upload/file-upload-project-csharp1.png

<!-- Links -->

[портала Azure]: https://portal.azure.com/

[Центре разработчика для Центра Интернета вещей Azure]: http://azure.microsoft.com/develop/iot

[Обработка временного сбоя]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[пакет SDK NuGet для служб Azure IoT]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
