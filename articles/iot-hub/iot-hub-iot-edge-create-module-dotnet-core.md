---
title: "Создание модуля Azure IoT Edge с помощью C# | Документация Майкрософт"
description: "В этом руководстве показано, как с помощью последних пакетов NuGet для Azure IoT Edge, Visual Studio Code и C# написать модуль преобразователя данных BLE."
services: iot-hub
author: jeffreyCline
manager: timlt
keywords: "azure, iot, руководство, модуль, nuget, vscode, csharp, edge"
ms.service: iot-hub
ms.devlang: csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2017
ms.author: jcline
ms.openlocfilehash: 7175ffc8de2c043593d61143b402484d33e4a8cc
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/03/2017
---
# <a name="create-an-azure-iot-edge-module-with-cx23"></a>Создание модуля Azure IoT Edge с помощью C&#x23;

В этом руководстве показано, как создать модуль для `Azure IoT Edge` с помощью `Visual Studio Code` и `C#`.

В этом руководстве приводятся пошаговые инструкции по настройке среды и написанию модуля преобразователя данных [BLE](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) с помощью последних пакетов `Azure IoT Edge NuGet`. 

>[!NOTE]
В этом руководстве используется пакет `.NET Core SDK`, который поддерживает кроссплатформенную совместимость. Приведенные ниже инструкции написаны на основе операционной системы `Windows 10`. Некоторые команды в этом руководстве могут отличаться в зависимости от используемой `development environment`. 

## <a name="prerequisites"></a>Предварительные требования

В этом разделе настраивается среда для разработки модуля `Azure IoT Edge`. Данные настройки действительны для следующих операционных систем: **64-разрядная версия Windows** и **64-разрядная версия Linux (Ubuntu/Debian 8)**.

Требуется следующее ПО:

- [клиент Git](https://git-scm.com/downloads);
- [Базовый пакет SDK для .NET](https://www.microsoft.com/net/core#windowscmd)
- [Visual Studio Code](https://code.visualstudio.com/)

Для данного примера не требуется клонирование репозитория, хотя все рассматриваемые в этом руководстве примеры кода находятся в следующем репозитории:

- `git clone https://github.com/Azure-Samples/iot-edge-samples.git`.
- `cd iot-edge-samples/dotnetcore/simulated_ble`

## <a name="getting-started"></a>Приступая к работе

1. Установите `.NET Core SDK`.
2. Установите `Visual Studio Code`и `C# extension` из Marketplace для Visual Studio Code.

Просмотрите это [краткое видеоруководство](https://channel9.msdn.com/Blogs/dotnet/Get-started-VSCode-Csharp-NET-Core-Windows) о том, как приступить к работе с `Visual Studio Code` и `.NET Core SDK`.

## <a name="creating-the-azure-iot-edge-converter-module"></a>Создание модуля преобразователя Azure IoT Edge

1. Инициализируйте новый проект C# библиотеки классов `.NET Core`:
    - Откройте командную строку (`Windows + R` -> `cmd` -> `enter`).
    - Перейдите к папке, в которой хотите создать проект `C#`.
    - Введите **dotnet new classlib -o IoTEdgeConverterModule -f netstandard1.3**. 
    - Эта команда создает в каталоге проектов пустой класс с именем `Class1.cs`.
2. Перейдите к папке, в которой мы только что создали проект библиотеки классов. Для этого введите команду **cd IoTEdgeConverterModule**.
3. Откройте проект в `Visual Studio Code`, введя команду **code .**.
4. После открытия проекта в `Visual Studio Code` щелкните **IoTEdgeConverterModule.csproj**, чтобы открыть файл, как показано на следующем изображении:

    ![Окно редактирования Visual Studio Code](media/iot-hub-iot-edge-create-module/vscode-edit-csproj.png)

5. Вставьте большой двоичный объект `XML` из приведенного ниже фрагмента кода между закрывающими тегами `PropertyGroup` и `Project` (шестая строка на предыдущем изображении) и сохраните файл, нажав сочетание клавиш `Ctrl` + `S`.

   ```xml
     <ItemGroup>
       <PackageReference Include="Microsoft.Azure.Devices.Gateway.Module.NetStandard" Version="1.0.5" />
       <PackageReference Include="System.Threading.Thread" Version="4.3.0" />
       <PackageReference Include="Newtonsoft.Json" Version="10.0.2" />
     </ItemGroup> 
   ```

6. После сохранения файла `.csproj` в `Visual Studio Code` должен отобразиться запрос в виде диалогового окна `unresolved dependencies`, как показано на следующем изображении: 

    ![Диалоговое окно восстановления зависимостей Visual Studio Code](media/iot-hub-iot-edge-create-module/vscode-restore.png)

    a) Нажмите кнопку `Restore`, чтобы восстановить все ссылки в файле `.csproj` проекта, включая добавленные нами `PackageReferences`. 

    b) `Visual Studio Code` автоматически создает файл `project.assets.json` в папке `obj` проекта. Этот файл содержит сведения о зависимостях проекта, которые позволяют ускорить последующие операции восстановления.
 
    >[!NOTE]
    `.NET Core Tools` теперь основаны на MSBuild. Это означает, что вместо файла `project.json` создается файл `.csproj` проекта.

    - Если `Visual Studio Code` не отображает запрос, то это не проблема, так как эти действия можно выполнить вручную. Откройте окно интегрированного терминала `Visual Studio Code`, нажав сочетание клавиш `Ctrl` + `backtick`. Либо воспользуйтесь меню `View` -> `Integrated Terminal`.
    - В окне `Integrated Terminal` введите команду **dotnet restore**.
    
7. Переименуйте файл `Class1.cs` в `BleConverterModule.cs`. 

    a) Чтобы переименовать файл, сначала щелкните файл, а затем нажмите клавишу `F2`.
    
    b) Введите новое имя **BleConverterModule**, как показано на следующем изображении:

    ![Переименование класса в Visual Studio Code](media/iot-hub-iot-edge-create-module/vscode-rename.png)

8. Замените существующий код в файле `BleConverterModule.cs`, скопировав и вставив следующий фрагмент кода в файл `BleConverterModule.cs`.

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Globalization;
   using System.Linq;
   using System.Text;
   using System.Threading;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Gateway;
   using Newtonsoft.Json;
   
   namespace IoTEdgeConverterModule
   {
       public class BleConverterModule : IGatewayModule, IGatewayModuleStart
       {
           private Broker broker;
           private string configuration;
           private int messageCount;

           public void Create(Broker broker, byte[] configuration)
           {
               this.broker = broker;
               this.configuration = System.Text.Encoding.UTF8.GetString(configuration);
           }
   
           public void Start()
           {
           }

           public void Destroy()
           {
           }

           public void Receive(Message received_message)
           {
               string recMsg = Encoding.UTF8.GetString(received_message.Content, 0, received_message.Content.Length);
               BleData receivedData = JsonConvert.DeserializeObject<BleData>(recMsg);

               float temperature = float.Parse(receivedData.Temperature, CultureInfo.InvariantCulture.NumberFormat); 
               Dictionary<string, string> receivedProperties = received_message.Properties;
            
               Dictionary<string, string> properties = new Dictionary<string, string>();
               properties.Add("source", receivedProperties["source"]);
               properties.Add("macAddress", receivedProperties["macAddress"]);
               properties.Add("temperatureAlert", temperature > 30 ? "true" : "false");
   
               String content = String.Format("{0} \"deviceId\": \"Intel NUC Gateway\", \"messageId\": {1}, \"temperature\": {2} {3}", "{", ++this.messageCount, temperature, "}");
               Message messageToPublish = new Message(content, properties);
   
               this.broker.Publish(messageToPublish);
           }
       }
   }
   ```

9. Сохраните файл, нажав сочетание клавиш `Ctrl` + `S`.

10. Создайте файл с именем `Untitled-1`, нажав сочетание клавиш `Ctrl` + `N`, как показано на следующем изображении:

    ![Новый файл Visual Studio Code](media/iot-hub-iot-edge-create-module/vscode-new-file.png)

11. Чтобы десериализировать объект `JSON`, который мы получаем с имитации устройства `BLE`, скопируйте следующий код в окно редактора кода файла `Untitled-1`. 

   ```csharp
   using System;
   using Newtonsoft.Json;

   namespace IoTEdgeConverterModule
   {
       public class BleData
       {
           [JsonProperty(PropertyName = "temperature")]
           public string Temperature { get; set; }
       }
   }
   ```

12. Сохраните файл как `BleData.cs`, нажав сочетание клавиш `Ctrl` + `Shift` + `S`.
    - В диалоговом окне "Сохранить как" в раскрывающемся меню `Save as Type` выберите `C# (*.cs;*.csx)`, как показано на следующем изображении:

    ![Диалоговое окно "Сохранить как" Visual Studio Code](media/iot-hub-iot-edge-create-module/vscode-save-as.png)

13. Создайте файл с именем `Untitled-1`, нажав сочетание клавиш `Ctrl` + `N`.

14. Скопируйте следующий фрагмент кода и вставьте его в файл `Untitled-1`. Этот класс является модулем `Azure IoT Edge`, который мы используем для вывода данных, получаемых от модуля `BleConverterModule`.

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Linq;
   using System.Text;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Gateway;
   using Newtonsoft.Json;
   
   namespace PrinterModule
   {
       public class DotNetPrinterModule : IGatewayModule
       {
           private string configuration;
           public void Create(Broker broker, byte[] configuration)
           {
               this.configuration = System.Text.Encoding.UTF8.GetString(configuration);
           }
   
           public void Destroy()
           {
           }
   
           public void Receive(Message received_message)
           {
               string recMsg = System.Text.Encoding.UTF8.GetString(received_message.Content, 0, received_message.Content.Length);
               Dictionary<string, string> receivedProperties = received_message.Properties;
               
               BleConverterData receivedData = JsonConvert.DeserializeObject<BleConverterData>(recMsg);
   
               Console.WriteLine();
               Console.WriteLine("Module           : [DotNetPrinterModule]");
               Console.WriteLine("received_message : {0}", recMsg);
   
               if(received_message.Properties["source"] == "bleTelemetry")
               {
                   Console.WriteLine("Source           : {0}", receivedProperties["source"]);
                   Console.WriteLine("MAC address      : {0}", receivedProperties["macAddress"]);
                   Console.WriteLine("Temperature Alert: {0}", receivedProperties["temperatureAlert"]);
                   Console.WriteLine("Deserialized Obj : [BleConverterData]");
                   Console.WriteLine("  DeviceId       : {0}", receivedData.DeviceId);
                   Console.WriteLine("  MessageId      : {0}", receivedData.MessageId);
                   Console.WriteLine("  Temperature    : {0}", receivedData.Temperature);
               }
   
               Console.WriteLine();
           }
       }
   }
   ```

15. Сохраните файл как `DotNetPrinterModule.cs`, нажав сочетание клавиш `Ctrl` + `Shift` + `S`.
    - В диалоговом окне "Сохранить как" в раскрывающемся меню `Save as Type` выберите `C# (*.cs;*.csx)`.

16. Создайте файл с именем `Untitled-1`, нажав сочетание клавиш `Ctrl` + `N`.

17. Чтобы десериализировать объект `JSON`, который мы получаем от модуля `BleConverterModule`, скопируйте следующий фрагмент кода и вставьте его в файл `Untitled-1`. 

   ```csharp
   using System;
   using Newtonsoft.Json;

   namespace PrinterModule
   {
       public class BleConverterData
       {
           [JsonProperty(PropertyName = "deviceId")]
           public string DeviceId { get; set; }
   
           [JsonProperty(PropertyName = "messageId")]
           public string MessageId { get; set; }
   
           [JsonProperty(PropertyName = "temperature")]
           public string Temperature { get; set; }
       }
   }
   ```

18. Сохраните файл как `BleConverterData.cs`, нажав сочетание клавиш `Ctrl` + `Shift` + `S`.
    - В диалоговом окне "Сохранить как" в раскрывающемся меню `Save as Type` выберите `C# (*.cs;*.csx)`.

19. Создайте файл с именем `Untitled-1`, нажав сочетание клавиш `Ctrl` + `N`.

20. Скопируйте следующий фрагмент кода и вставьте его в файл `Untitled-1`.

   ```json
   {
       "loaders": [
           {
               "type": "dotnetcore",
               "name": "dotnetcore",
               "configuration": {
                   "binding.path": "dotnetcore.dll",
                   "binding.coreclrpath": "C:\\Program Files\\dotnet\\shared\\Microsoft.NETCore.App\\1.1.1\\coreclr.dll",
                   "binding.trustedplatformassemblieslocation": "C:\\Program Files\\dotnet\\shared\\Microsoft.NETCore.App\\1.1.1\\"
               }
           }
       ],
          "modules": [
           {
               "name": "simulated_device",
               "loader": {
                   "name": "native",
                   "entrypoint": {
                       "module.path": "simulated_device.dll"
                   }
               },
               "args": {
                   "macAddress": "01:02:03:03:02:01",
                   "messagePeriod": 500
               }
           },
           {
               "name": "ble_converter_module",
               "loader": {
                   "name": "dotnetcore",
                   "entrypoint": {
                       "assembly.name": "IoTEdgeConverterModule",
                       "entry.type": "IoTEdgeConverterModule.BleConverterModule"
                   }
               },
               "args": ""
           },
           {
               "name": "printer_module",
               "loader": {
                   "name": "dotnetcore",
                   "entrypoint": {
                       "assembly.name": "IoTEdgeConverterModule",
                       "entry.type": "PrinterModule.DotNetPrinterModule"
                   }
               },
               "args": ""
           }
       ],
       "links": [
           {
               "source": "simulated_device", "sink": "ble_converter_module"
           },
           {
               "source": "ble_converter_module", "sink": "printer_module"
           }
   ]
   }
   ```

21. Сохраните файл как `gw-config.json`, нажав сочетание клавиш `Ctrl` + `Shift` + `S`.
    - В диалоговом окне "Сохранить как" в раскрывающемся меню `Save as Type` выберите `JSON (*.json;*.bowerrc;*.jshintrc;*.jscsrc;*.eslintrc;*.babelrc;*webmanifest)`.

22. Чтобы файл конфигурации можно было скопировать в выходной каталог, обновите файл `IoTEdgeConverterModule.csproj`, вставив в него следующий большой двоичный объект XML:

   ```xml
     <ItemGroup>
       <None Update="gw-config.json" CopyToOutputDirectory="PreserveNewest" />
     </ItemGroup>
   ```
    
   - Обновленный файл `IoTEdgeConverterModule.csproj` должен выглядеть так:

    ![Обновленный CSPROJ-файл Visual Studio Code](media/iot-hub-iot-edge-create-module/vscode-update-csproj.png)

23. Создайте файл с именем `Untitled-1`, нажав сочетание клавиш `Ctrl` + `N`.

24. Скопируйте следующий фрагмент кода и вставьте его в файл `Untitled-1`.

   ```powershell
   Copy-Item -Path $env:userprofile\.nuget\packages\microsoft.azure.devices.gateway.native.windows.x64\1.1.3\runtimes\win-x64\native\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.runtime.serialization.formatters\4.3.0\lib\netstandard1.4\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.runtime.serialization.primitives\4.3.0\lib\netstandard1.3\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\newtonsoft.json\10.0.2\lib\netstandard1.3\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.componentmodel.typeconverter\4.3.0\lib\netstandard1.5\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.collections.nongeneric\4.3.0\lib\netstandard1.3\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.collections.specialized\4.3.0\lib\netstandard1.3\* -Destination .\bin\Debug\netstandard1.3
   ```

25. Сохраните файл как `binplace.ps1`, нажав сочетание клавиш `Ctrl` + `Shift` + `S`.
    - В диалоговом окне "Сохранить как" в раскрывающемся меню `Save as Type` выберите `PowerShell (*.ps1;*.psm1;*.psd1;*.pssc;*.psrc)`.

26. Выполните сборку проекта, нажав сочетание клавиш `Ctrl` + `Shift` + `B`. При первом выполнении сборки проекта в `Visual Studio Code` отобразится запрос в виде диалогового окна `No build task defined.`, как показано на следующем изображении:

    ![Диалоговое окно задачи сборки Visual Studio Code](media/iot-hub-iot-edge-create-module/vscode-build-task.png)

    a) Нажмите кнопку `Configure Build Task`.

    b) В раскрывающегося меню `Select a Task Runner` диалогового окна выберите `.NET Core`, как показано на следующем изображении: 

    ![Диалоговое окно выбора задачи Visual Studio Code](media/iot-hub-iot-edge-create-module/vscode-build-task-runner.png)

    c) Если щелкнуть элемент `.NET Core`, то в каталоге `.vscode` будет создан файл `tasks.json`, который затем откроется в окне `code editor`. Нет необходимости изменять этот файл. Просто закройте вкладку.

27.  Откройте окно интегрированного терминала `Visual Studio Code`, нажав сочетание клавиш `Ctrl` + `backtick` или с помощью меню `View` -> `Integrated Terminal`, и введите в командной строке `PowerShell` команду **.\binplace.ps1**. Эта команда копирует все зависимости в выходной каталог.

28. Перейдите в выходной каталог проекта в окне `Integrated Terminal`, введя команду **cd .\bin\Debug\netstandard1.3**.

29. Запустите пример проекта, введя команду **.\gw.exe gw-config.json** в окно командной строки `Integrated Terminal`. 
    - Если вы четко следовали инструкциям, приведенным в этом руководстве, то у вас должен запуститься пример проекта `Azure IoT Edge BLE Data Converter Module`, как показано на следующем изображении:
    
        ![Пример имитации устройства, запущенный в Visual Studio Code](media/iot-hub-iot-edge-create-module/vscode-run.png)
    
    - Чтобы завершить работу приложения, нажмите клавишу `<Enter>`.

>[!IMPORTANT]
Не рекомендуется использовать сочетание клавиш `Ctrl` + `C` для завершения работы приложения шлюза `IoT Edge` (то есть **gw.exe**). Это действие может вызвать аварийное завершение процесса.

