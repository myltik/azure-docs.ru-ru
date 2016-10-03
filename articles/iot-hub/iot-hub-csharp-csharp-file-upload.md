<properties
	pageTitle="Передача файлов из устройств в центре IoT | Microsoft Azure"
	description="Следуйте инструкциям этого учебника, чтобы узнать, как передавать файлы из устройств в центре IoT Azure с помощью C#."
	services="iot-hub"
	documentationCenter=".net"
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="06/21/2016"
     ms.author="elioda"/>

# Учебник: как передать файлы из устройств в облако с помощью центра IoT

## Введение

Центр IoT Azure — полностью управляемая служба, которая обеспечивает надежный и защищенный двунаправленный обмен данными между миллионами устройств IoT и серверной частью приложения. В предыдущих учебниках ([Приступая к работе с центром IoT] и [Отправка сообщений из облака на устройства с помощью центра IoT]) описаны базовые функции центра IoT для отправки сообщений из устройства в облако и из облака на устройство, а в учебнике [Обработка сообщений с устройства в облако] рассмотрен надежный способ хранения сообщений, отправляемых из устройства в облако, в хранилище BLOB-объектов Azure. Тем не менее в некоторых случаях не просто сопоставить данные, отправляемые устройством, с относительно небольшими сообщениями, отправляемыми из устройства в облако, которые принимает центр IoT. В качестве примеров можно привести большие файлы, содержащие изображения, видео, данные вибрации для с высокой частотой выборки или файлы, содержащие предварительно обработанные данные в какой-либо форме. Эти файлы обычно обрабатываются в виде пакета в облаке с помощью таких инструментов, как [фабрика данных Azure] или стек [Hadoop]. Когда передача файлов из устройства предпочтительнее отправки событий, вы все равно можете использовать функции безопасности и надежности центра IoT.

В этом учебнике используется код учебника [Отправка сообщений из облака на устройства с помощью центра IoT], чтобы показать, как использовать возможности передачи файлов центра IoT. Вы узнаете, как безопасно предоставить устройству универсальный код ресурса (URI) большого двоичного объекта Azure для передачи файла и как использовать уведомления о передаче файлов центра IoT для активации обработки файла в серверной части приложения.

По завершении этого учебника вы запустите два консольных приложения для Windows.

* **SimulatedDevice** — измененная версия приложения, созданного в учебнике [Отправка сообщений из облака на устройства с помощью центра IoT], которое передает файл в хранилище с помощью универсального кода ресурса (URI) SAS, предоставленного вашим центром IoT.
* **ReadFileUploadNotification** — приложение, которое получает уведомления о передаче файлов от центра IoT.

> [AZURE.NOTE] Существуют пакеты SDK для устройств Azure IoT, обеспечивающие поддержку многих платформ устройств и языков (включая C, Java и JavaScript) в центре IoT. Пошаговые инструкции по подключению устройства к коду, приведенному в этом учебнике, и к центру IoT Azure в целом доступны в [Центре разработчика для IoT в Azure].

Для работы с этим учебником требуется следующее:

+ Microsoft Visual Studio 2015;

+ Активная учетная запись Azure. (Если ее нет, можно создать бесплатную пробную версию учетной записи всего за несколько минут. Дополнительные сведения см. на странице [Бесплатная пробная версия Azure][lnk-free-trial].)

## Связывание учетной записи хранения Azure с центром IoT

Так как виртуальное устройство отправляет файл в большой двоичный объект в службе хранилища Azure, необходимо связать учетную запись [хранилища Azure] с центром IoT. После этого центр IoT создаст универсальный код ресурса (URI) SAS, который устройство сможет использовать для безопасной отправки файла в контейнер больших двоичных объектов. Служба центра IoT и пакеты SDK для устройств координируют процесс создания URI SAS, делая его доступным для устройства, которое отправляет файл.

Чтобы связать учетную запись хранилища Azure с центром IoT, следуйте инструкциям в статье [Управление центрами IoT через портал Azure].

## Передача файла из виртуального устройства

В этом разделе вы измените приложение виртуального устройства, созданное в разделе [Отправка сообщений из облака на устройства с помощью центра IoT], для получения сообщений из облака на устройство от центра IoT.

1. В Visual Studio щелкните правой кнопкой мыши проект **SimulatedDevice**, а затем выберите **Добавить** > **Существующий элемент**. Перейдите к файлу образа и добавьте его в проект. В этом руководстве используется образ `image.jpg`.

2. Щелкните его правой кнопкой мыши и выберите пункт **Свойства**. Убедитесь, что параметр **Копировать в выходной каталог** имеет значение **Всегда копировать**.

    ![][1]

3. Добавьте в начало файла **Program.cs** следующие инструкции:

        using System.IO;

4. Добавьте следующий метод в класс **Program**.
         
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

    Метод `UploadToBlobAsync` принимает имя отправляемого файла и источник его передачи, а затем обрабатывает передачу в хранилище. Консольное приложение отображает время, необходимое для передачи файла.

5. Добавьте в метод **Main** непосредственно перед строкой `Console.ReadLine()` следующий метод:

        SendToBlobAsync();

> [AZURE.NOTE] Для упрощения в этом руководстве не реализуются какие-либо политики повтора. В рабочем коде следует реализовать политики повтора (например, экспоненциальную задержку), как указано в статье MSDN [Transient Fault Handling] \(Обработка временного сбоя).

## Получение уведомления о передачи файла

В этом разделе вам предстоит создать консольное приложение для Windows, которое принимает уведомления о передаче файлов из центра IoT.

1. В текущем решении Visual Studio создайте проект Windows на языке Visual C#, используя шаблон проекта **Консольное приложение**. Назовите проект **ReadFileUploadNotification**.

    ![Новый проект в Visual Studio][2]

2. В обозревателе решений щелкните правой кнопкой мыши проект **ReadFileUploadNotification** и выберите пункт **Управление пакетами NuGet**.

    Откроется окно "Управление пакетами NuGet".

2. Выполните поиск `Microsoft.Azure.Devices` и щелкните **Установить**, после чего примите условия использования.

	После этого будут выполнены скачивание, установка [пакета SDK для службы Microsoft Azure IoT] и добавление ссылки на него в проект **ReadFileUploadNotification**.

3. Добавьте в начало файла **Program.cs** следующие инструкции:

        using Microsoft.Azure.Devices;

4. Добавьте следующие поля в класс **Program**. Замените значение заполнителя строкой подключения центра IoT из раздела [Приступая к работе с центром IoT]\:

		static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";
        
5. Добавьте следующий метод в класс **Program**.
   
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

6. Наконец, добавьте следующие строки в метод **Main**:

        Console.WriteLine("Receive file upload notifications\n");
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
        ReceiveFileUploadNotificationAsync().Wait();
        Console.ReadLine();

## Запуск приложений

Теперь все готово к запуску приложений.

1. В Visual Studio щелкните правой кнопкой мыши свое решение и выберите пункт **Назначить запускаемые проекты**. Выберите **Несколько запускаемых проектов**, а затем выберите действие **Запуск** для обоих приложений **ReadFileUploadNotification** и **SimulatedDevice**.

2. Нажмите клавишу **F5**. Должны запуститься оба приложения. Вы должны увидеть, как завершится передача в одном консольном приложении и как второе консольное приложение получит уведомление о передаче. Наличие отправленного файла в своей учетной записи хранилища можно проверить с помощью [портала Azure] или обозревателя серверов Visual Studio.

  ![][50]


## Дальнейшие действия

В этом учебнике было показано, как использовать возможности передачи файлов центра IoT, чтобы упросить передачу файлов из устройств. Изучение функций и сценариев центра IoT можно продолжить в следующих руководствах:

- [Создание центра IoT программно][lnk-create-hub]
- [Знакомство с пакетом SDK для устройств Azure IoT для C][lnk-c-sdk]
- [Пакеты SDK для центра IoT][lnk-sdks]

Для дальнейшего изучения возможностей центра IoT см. следующие статьи:

- [Разработка решения][lnk-design]
- [Обзор управления устройствами центра IoT с помощью примера пользовательского интерфейса][lnk-dmui]
- [Пакет SDK для шлюза IoT (бета-версия): отправка сообщений с устройства в облако через виртуальное устройство с помощью Linux][lnk-gateway]
- [Управление центрами IoT через портал Azure][lnk-portal]

<!-- Images. -->

[50]: ./media/iot-hub-csharp-csharp-file-upload/run-apps1.png
[1]: ./media/iot-hub-csharp-csharp-file-upload/image-properties.png
[2]: ./media/iot-hub-csharp-csharp-file-upload/create-identity-csharp1.png

<!-- Links -->

[портала Azure]: https://portal.azure.com/

[фабрика данных Azure]: https://azure.microsoft.com/documentation/services/data-factory/
[Hadoop]: https://azure.microsoft.com/documentation/services/hdinsight/

[Отправка сообщений из облака на устройства с помощью центра IoT]: iot-hub-csharp-csharp-c2d.md
[Обработка сообщений с устройства в облако]: iot-hub-csharp-csharp-process-d2c.md
[Приступая к работе с центром IoT]: iot-hub-csharp-csharp-getstarted.md
[Центре разработчика для IoT в Azure]: http://www.azure.com/develop/iot

[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[хранилища Azure]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Управление центрами IoT через портал Azure]: iot-hub-manage-through-portal.md#file-upload
[пакета SDK для службы Microsoft Azure IoT]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-sdks-summary.md

[lnk-design]: iot-hub-guidance.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0914_2016-->