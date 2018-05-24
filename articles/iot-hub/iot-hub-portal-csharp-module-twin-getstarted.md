---
title: Приступая к работе с удостоверением и двойником модуля Центра Интернета вещей Azure (портал и .NET) | Документация Майкрософт
description: Сведения о создании удостоверения модуля и обновлении двойника модуля с помощью портала и .NET.
services: iot-hub
documentationcenter: .net
author: chrissie926
manager: timlt
editor: ''
ms.assetid: f40604ff-8fd6-4969-9e99-8574fbcf036c
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/26/2018
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 30a53620a640b0122286a6ac69c0f98cd0bbde40
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34361694"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-the-portal-and-net-device"></a>Начало работы с удостоверением модуля Центра Интернета вещей и двойником модуля с использованием портала и устройства .NET

> [!NOTE]
> [Удостоверение и двойник модуля](iot-hub-devguide-module-twins.md) аналогичны удостоверению и двойнику устройства Центра Интернета вещей Azure, однако они предоставляют более высокую степень детализации. В то время как удостоверение и двойник устройства Центра Интернета вещей Azure позволяют серверному приложению настраивать устройство и отображать состояние устройства, удостоверение и двойник модуля предоставляют эти возможности для отдельных компонентов устройства. Благодаря этому можно изолировать конфигурацию и условия для каждого компонента на совместимых устройствах с несколькими компонентами, например устройствах на основе операционной системы или устройствах со встроенным ПО.

Из этого руководства вы узнаете: 
1. как создать удостоверение модуля на портале; 
2. как использовать SDK для устройства .NET, чтобы обновить двойник модуля из устройства.

> [!NOTE]
> Статья о пакетах SDK для Центра Интернета вещей Azure содержит сведения о различных пакетах SDK, которые можно использовать для создания приложений, которые будут работать на устройствах и в серверной части решения.

Для работы с этим учебником требуется:

* Visual Studio 2015 или Visual Studio 2017.
* Активная учетная запись Azure. (Если ее нет, можно создать [бесплатную учетную запись] [lnk-free-trial] всего за несколько минут.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

## <a name="create-a-device-identity-in-the-portal"></a>Создание удостоверения устройства на портале

Теперь у вас есть Центр Интернета вещей. Откройте [портал](https://portal.azure.com) и перейдите к своему Центру Интернета вещей. Щелкните "Устройства IoT", а затем нажмите кнопку "Добавить", чтобы создать удостоверение устройства. Назовите его **MyFirstDevice**. 

![Создание удостоверения устройства][8]

После сохранения вы увидите успешное созданное удостоверение MyFirstDevice в списке удостоверений устройств.

![Идентификатор устройства создан][11]

Теперь щелкните строку. Вы увидите сведения об устройстве.

![Сведения об устройстве][10]

## <a name="create-a-module-identity-in-the-portal"></a>Создание удостоверения модуля на портале

В рамках одного удостоверения устройства можно создать до 20 удостоверений модуля. Нажмите кнопку **Add Module Identity** (Добавить удостоверение модуля) сверху, чтобы создать свое первое удостоверение модуля под названием **myFirstModule**. 

![Сведения об устройстве][9]

Нажмите кнопку "Сохранить" и щелкните только что созданное удостоверение модуля. Отобразятся сведения об удостоверении модуля. Сохраните значение параметра "Строка подключения — первичный ключ". Оно будет использоваться в следующем разделе при настройке модуля на устройстве.

![Сведения об устройстве][12]

<a id="D2C_csharp"></a>
## <a name="update-the-module-twin-using-net-device-sdk"></a>Обновление двойника модуля с помощью пакета SDK для устройства .NET

Вы успешно создали удостоверение модуля в своем Центре Интернета вещей. Давайте попробуем обменяться данными с облаком со своего имитированного устройства. После создания удостоверения модуля двойник модуля будет неявно создан в Центре Интернета вещей. В этом разделе вы создадите консольное приложение .NET на имитированном устройстве, которое обновляет сообщаемые свойства двойника модуля.

1. **Создайте проект Visual Studio**. В Visual Studio добавьте в имеющееся решение проект классического приложения Windows на языке Visual C#, используя шаблон проекта **Консольное приложение (.NET Framework)**. Убедитесь, что указана версия платформы .NET 4.6.1 или более поздняя. В качестве имени проекта укажите **UpdateModuleTwinReportedProperties**.

    ![Создание проекта Visual Studio][13]

2. **Установите пакет SDK V1.16.0-preview-005 .NET для Центра Интернета вещей Azure**. Удостоверение модуля и двойник модуля предоставляются в общедоступной предварительной версии. Они доступны только через пакеты SDK предварительного выпуска для Центра Интернета вещей. В Visual Studio выберите для решения элементы "Сервис > Диспетчер пакетов NuGet > Управление пакетами NuGet". Найдите Microsoft.Azure.Devices.Client. Обязательно установите флажок "Включить предварительные выпуски". Выберите версию V1.16.0-preview-005 и установите ее. Теперь у вас есть доступ ко всем возможностям этого модуля. 

    ![IУстановка пакета SDK версии 1.16.0-preview-005 .NET для Центра Интернета вещей Azure][14]

3. **Получите строку подключения модуля**. Войдите на [портал Azure][lnk-portal]. Перейдите к Центру Интернета вещей и щелкните "Устройства IoT". Найдите устройство myFirstDevice, откройте его, и вы увидите, что модуль myFirstModule успешно создан. Скопируйте строку подключения модуля. Она понадобится на следующем шаге.

    ![Сведения о модуле на портале Azure][15]

4. **Создайте консольное приложение UpdateModuleTwinReportedProperties**. В верхнюю часть файла **Program.cs** добавьте следующие инструкции `using`:

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

    Добавьте следующие поля в класс **Program** . Замените значение заполнителя строкой подключения модуля.

    ```csharp
    private const string ModuleConnectionString = "<Your module connection string>“;
    private static ModuleClient Client = null;
    ```

    Добавьте метод **OnDesiredPropertyChanged** в класс **Program**:

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, object userContext)
        {
            Console.WriteLine("desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
            Console.WriteLine("Sending current time as reported property");
            TwinCollection reportedProperties = new TwinCollection
            {
                ["DateTimeLastDesiredPropertyChangeReceived"] = DateTime.Now
            };

            await Client.UpdateReportedPropertiesAsync(reportedProperties).ConfigureAwait(false);
        }
    ```

    Наконец, добавьте следующие строки в метод **Main** :

    ```csharp
    static void Main(string[] args)
    {
        Microsoft.Azure.Devices.Client.TransportType transport = Microsoft.Azure.Devices.Client.TransportType.Amqp;

        try
        {
            Client = ModuleClient.CreateFromConnectionString(ModuleConnectionString, transport);
            Client.SetConnectionStatusChangesHandler(ConnectionStatusChangeHandler);
            Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();

            Console.WriteLine("Retrieving twin");
            var twinTask = Client.GetTwinAsync();
            twinTask.Wait();
            var twin = twinTask.Result;
            Console.WriteLine(JsonConvert.SerializeObject(twin));

            Console.WriteLine("Sending app start time as reported property");
            TwinCollection reportedProperties = new TwinCollection();
            reportedProperties["DateTimeLastAppLaunch"] = DateTime.Now;

            Client.UpdateReportedPropertiesAsync(reportedProperties);
        }
        catch (AggregateException ex)
        {
            Console.WriteLine("Error in sample: {0}", ex);
        }

        Console.WriteLine("Waiting for Events.  Press enter to exit...");
        Client.CloseAsync().Wait();
    }
    ```

    В этом примере кода показано, как извлечь двойник модуля и обновить сообщаемые свойства с помощью протокола AMQP. В общедоступной предварительной версии для операций двойника модуля поддерживается только протокол AMQP.
    ```

## Run the apps

You are now ready to run the apps. In Visual Studio, in Solution Explorer, right-click your solution, and then click **Set StartUp projects**. Select **Multiple startup projects**, and then select **Start** as the action for the console app. And then press F5 to start both apps running. 

## Next steps

To continue getting started with IoT Hub and to explore other IoT scenarios, see:

* [Get started with IoT Hub module identity and module twin using .NET backup and .NET device][lnk-csharp-csharp-getstarted]
* [Getting started with IoT Edge][lnk-iot-edge]


<!-- Images. -->
[8]:./media\iot-hub-portal-csharp-module-twin-getstarted/create-device-id.JPG
[9]:./media\iot-hub-portal-csharp-module-twin-getstarted/create-module-id.JPG
[10]:./media\iot-hub-portal-csharp-module-twin-getstarted/device-details.JPG
[11]:./media\iot-hub-portal-csharp-module-twin-getstarted/device-id-created.JPG
[12]:./media\iot-hub-portal-csharp-module-twin-getstarted/module-details.JPG
[13]: ./media\iot-hub-csharp-csharp-module-twin-getstarted/update-twins-csharp1.JPG
[14]: ./media\iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png
[15]: ./media\iot-hub-csharp-csharp-module-twin-getstarted/module-detail.JPG
<!-- Links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-csharp-csharp-getstarted]: iot-hub-csharp-csharp-module-twin-getstarted.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
