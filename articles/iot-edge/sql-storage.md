---
title: Модуль Azure IoT Edge с кодом SQL | Документация Майкрософт
description: Храните данные в граничной системе с модулями Microsoft SQL, используя решение "Функции Azure" для форматирования данных.
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: ebertrams
ms.date: 02/21/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9c90cc28956e4dd7730cc7ba09a173f505f056fc
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34632406"
---
# <a name="store-data-at-the-edge-with-sql-server-databases"></a>Хранение данных в граничной системе с помощью баз данных SQL Server

Используйте устройства Azure IoT Edge для хранения данных, созданных в граничной системе. Устройства с временными подключениями к Интернету могут хранить собственные базы данных и отравлять отчет об изменениях в облако только при наличии подключения. Устройства, запрограммированные на отправку только критически важных данных в облако, могут сохранять оставшиеся данные для регулярной пакетной передачи. Другим службам Azure можно предоставить общий доступ к структурированным данным, размещенным в облаке, например для создания модели машинного обучения. 

В этой статье содержатся сведения для развертывания базы данных SQL Server на устройство IoT Edge. Функции Azure, запущенные на устройстве IoT Edge, структурируют входящие данные, а затем отправляют их в базу данных. Шаги, описанные в этой статье, также можно применить к другим базам данных, которые работают в контейнерах, например MySQL или PostgreSQL. 

## <a name="prerequisites"></a>предварительным требованиям 

Перед выполнением инструкций, приведенных в этой статье, выполните действия в следующих руководствах:
* Развертывание Azure IoT Edge на имитированном устройстве в ОС [Windows](tutorial-simulate-device-windows.md) или [Linux](tutorial-simulate-device-linux.md)
* [Развертывание Функции Azure в виде модуля IoT Edge — предварительный просмотр](tutorial-deploy-function.md)

Знакомство со статьями ниже не обязательно для работы с этим руководством, но вы можете использовать их для получения дополнительных сведений:
* [Run the SQL Server 2017 container image with Docker](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker) (Запуск образа контейнера SQL Server 2017 с Docker)
* [Использование Visual Studio Code для разработки и развертывания решения "Функции Azure" в Azure IoT Edge](how-to-vscode-develop-azure-function.md)

После завершения работы с требуемыми руководствами на компьютере должны быть установлены все необходимые компоненты. 
* Активный Центр Интернета вещей Azure.
* Устройство IoT Edge с минимальным объемом ОЗУ и размером диска 2 ГБ.
* [Visual Studio Code](https://code.visualstudio.com/). 
* [Расширение Azure IoT Edge для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [C# для расширения Visual Studio Code (на платформе OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
* [Docker](https://docs.docker.com/engine/installation/)
* [Пакет SDK для .NET Core 2.0](https://www.microsoft.com/net/core#windowscmd). 
* [Python 2.7](https://www.python.org/downloads/).
* [Сценарий управления IoT Edge](https://pypi.python.org/pypi/azure-iot-edge-runtime-ctl).
* Шаблон AzureIoTEdgeFunction (`dotnet new -i Microsoft.Azure.IoT.Edge.Function`).
* Активный Центр Интернета вещей с устройством IoT Edge (как минимум).

Для работы с этим руководством можно использовать контейнеры Windows и Linux на архитектурах процессора x64. SQL Server не поддерживает процессоры ARM.

## <a name="deploy-a-sql-server-container"></a>Развертывание контейнера SQL Server

В этом разделе вы добавите базу данных MS-SQL на имитированное устройство IoT Edge. Используйте образ контейнера Docker на основе SQL Server 2017, доступный в качестве контейнера [Windows](https://hub.docker.com/r/microsoft/mssql-server-windows-developer/) и контейнера [Linux](https://hub.docker.com/r/microsoft/mssql-server-linux/). 

### <a name="deploy-sql-server-2017"></a>Развертывание SQL Server 2017

По умолчанию с помощью кода в этом разделе создается контейнер с бесплатным выпуском SQL Server 2017 Developer Edition. Если вместо этого выпуска вы хотите использовать выпуски для рабочей среды, ознакомьтесь с разделом о [запуске производственных образов контейнера](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-docker#production), чтобы получить дополнительные сведения. 

На шаге 3 вы добавите параметры создания в контейнер SQL Server. Они важны для установки переменных среды и постоянного хранилища. Настроенные [переменные среды](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-environment-variables) принимают лицензионное соглашение и определяют пароль. [Постоянное хранилище](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-docker#persist) настраивается с помощью [подключений](https://docs.docker.com/storage/volumes/). С помощью подключений создается контейнер SQL Server 2017 с присоединенным контейнером тома *sqlvolume*, что обеспечивает наличие данных даже в случае удаления контейнера. 

1. Откройте файл `deployment.json` в Visual Studio Code. 
2. Замените раздел **modules** в файле кодом ниже: 

   ```json
   "modules": {
          "filterFunction": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "<docker registry address>/filterfunction:latest",
              "createOptions": "{}"
            }
          },
          "tempSensor": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview",
              "createOptions": "{}"
            }
          },
          "sql": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "",
              "createOptions": ""
             }
          }
        }
   ```

3. Замените `<docker registry address>` адресом из статьи о [развертывании Функции Azure в виде модуля IoT Edge](tutorial-deploy-function.md).

   >[!NOTE]
   >Адрес реестра контейнеров совпадает с адресом сервера входа, который был скопирован из реестра. Он должен быть указан в формате `<your container registry name>.azurecr.io`.

4. В зависимости от используемой операционной системы обновите параметры для модуля SQL с помощью кода ниже: 

   * Windows:

      ```json
      "image": "microsoft/mssql-server-windows-developer",
      "createOptions": "{\"Env\": [\"ACCEPT_EULA=Y\",\"MSSQL_SA_PASSWORD=Strong!Passw0rd\"],\"HostConfig\": {\"Mounts\": [{\"Target\": \"C:\\\\mssql\",\"Source\": \"sqlVolume\",\"Type\": \"volume\"}],\"PortBindings\": {\"1433/tcp\": [{\"HostPort\": \"1401\"}]}}}"
      ```

   * Linux:

      ```json
      "image": "microsoft/mssql-server-linux:2017-latest",
      "createOptions": "{\"Env\": [\"ACCEPT_EULA=Y\",\"MSSQL_SA_PASSWORD=Strong!Passw0rd\"],\"HostConfig\": {\"Mounts\": [{\"Target\": \"/var/opt/mssql\",\"Source\": \"sqlVolume\",\"Type\": \"volume\"}],\"PortBindings\": {\"1433/tcp\": [{\"HostPort\": \"1401\"}]}}}"
      ```

5. Сохраните файл. 
6. В палитре команд VS Code выберите **Edge: Create deployment for Edge device** (Edge: создать развертывание для устройства Edge). 
7. Выберите идентификатор устройства IoT Edge.
8. Выберите файл `deployment.json`, который вы обновили. В окне вывода можно увидеть соответствующие выходные данные для развертывания. 
9. Чтобы запустить среду выполнения Edge, выберите в палитре команд **Edge: Start Edge** (Edge: запустить Edge).

>[!TIP]
>Каждый раз при создании контейнера SQL Server в рабочей среде нужно [изменять стандартный пароль системного администратора](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker#change-the-sa-password).

## <a name="create-the-sql-database"></a>Создание базы данных SQL

В этом разделе показано, как настроить базу данных SQL для хранения данных температуры, полученных от датчиков, подключенных к устройству IoT Edge. Если вы используете имитированное устройство, эти данные поступают из контейнера *tempSensor*. 

В программе командной строки подключитесь к базе данных: 

* Контейнер Windows
   ```cmd
   docker exec -it sql cmd
   ```

* Контейнер Linux
   ```bash
   docker exec -it sql bash
   ```

Откройте программу командной строки для SQL: 

* Контейнер Windows
   ```cmd
   sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
   ```

* Контейнер Linux
   ```bash
   /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
   ```

Создайте базу данных: 

* Контейнер Windows
   ```sql
   CREATE DATABASE MeasurementsDB
   ON
   (NAME = MeasurementsDB, FILENAME = 'C:\mssql\measurementsdb.mdf')
   GO
   ```

* Контейнер Linux
   ```sql
   CREATE DATABASE MeasurementsDB
   ON
   (NAME = MeasurementsDB, FILENAME = '/var/opt/mssql/measurementsdb.mdf')
   GO
   ```

Определите таблицу: 

   ```sql
   CREATE TABLE MeasurementsDB.dbo.TemperatureMeasurements (measurementTime DATETIME2, location NVARCHAR(50), temperature FLOAT)
   GO
   ```

Можно настроить файл Docker SQL Server таким образом, чтобы SQL Server автоматически настраивался для развертывания на нескольких устройствах IoT Edge. Дополнительные сведения см. в статье о [демонстрационном проекте контейнера Microsoft SQL Server](https://github.com/twright-msft/mssql-node-docker-demo-app).

## <a name="understand-the-sql-connection"></a>Сведения о подключении SQL

В других руководствах для обмена данными между контейнерами в изолированном состоянии используются маршруты. Тем не менее при работе с базой данных SQL Server требуется более тесное взаимодействие. 

IoT Edge при запуске автоматически создает мостовую сеть (Linux) или сеть NAT (Windows). Эта сеть называется **azure-iot-edge**. Если в дальнейшем понадобится отладка этого подключения, можно просмотреть его свойства в командной строке:

* Windows

   ```cmd
   docker network inspect azure-iot-edge
   ```

* Linux

   ```bash
   sudo docker network inspect azure-iot-edge
   ```

IoT Edge может также разрешить DNS имени контейнера через Docker, поэтому нет необходимости обращаться к базе данных SQL Server по IP-адресу. 

Для примера приводится строка подключения, которую мы используем в разделе ниже: 

   ```csharp
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

Как видно, строка подключения ссылается на контейнер по его имени — **sql**. Если используется другое имя модуля, обновите эту строку подключения. В противном случае перейдите к следующему разделу. 

## <a name="update-your-azure-function"></a>Обновление функции Azure
Для отправки данных в базу данных обновите функцию FilterFunction Azure, созданную в рамках работы с предыдущим руководством. Измените этот файл, чтобы он мог структурировать данные, полученные датчиками, а затем сохранить их в таблицу SQL. 

1. В Visual Studio Code откройте папку FilterFunction. 
2. Замените содержимое файла run.csx кодом ниже, который включает строку подключения SQL из предыдущего раздела: 

   ```csharp
   #r "Microsoft.Azure.Devices.Client"
   #r "Newtonsoft.Json"
   #r "System.Data.SqlClient"

   using System.IO;
   using Microsoft.Azure.Devices.Client;
   using Newtonsoft.Json;
   using Sql = System.Data.SqlClient;
   using System.Threading.Tasks;

   // Filter messages based on the temperature value in the body of the message and the temperature threshold value.
   public static async Task Run(Message messageReceived, IAsyncCollector<Message> output, TraceWriter log)
   {
       const int temperatureThreshold = 25;
       byte[] messageBytes = messageReceived.GetBytes();
       var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

       if (!string.IsNullOrEmpty(messageString))
       {
           // Get the body of the message and deserialize it
           var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

           //Store the data in SQL db
           const string str = "Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;";
           using (Sql.SqlConnection conn = new Sql.SqlConnection(str))
           {
           conn.Open();
           var insertMachineTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'machine', " + messageBody.machine.temperature + ");";
           var insertAmbientTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'ambient', " + messageBody.ambient.temperature + ");"; 
               using (Sql.SqlCommand cmd = new Sql.SqlCommand(insertMachineTemperature + "\n" + insertAmbientTemperature, conn))
               {
               //Execute the command and log the # rows affected.
               var rows = await cmd.ExecuteNonQueryAsync();
               log.Info($"{rows} rows were updated");
               }
           }

           if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
           {
               // Send the message to the output as the temperature value is greater than the threshold
               var filteredMessage = new Message(messageBytes);
               // Copy the properties of the original message into the new Message object
               foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
               {
                   filteredMessage.Properties.Add(prop.Key, prop.Value);
               }
               // Add a new property to the message to indicate it is an alert
               filteredMessage.Properties.Add("MessageType", "Alert");
               // Send the message        
               await output.AddAsync(filteredMessage);
               log.Info("Received and transferred a message with temperature above the threshold");
           }
       }
   }

   //Define the expected schema for the body of incoming messages
   class MessageBody
   {
       public Machine machine {get;set;}
       public Ambient ambient {get; set;}
       public string timeCreated {get; set;}
   }
   class Machine
   {
      public double temperature {get; set;}
      public double pressure {get; set;}         
   }
   class Ambient
   {
      public double temperature {get; set;}
      public int humidity {get; set;}         
   }
   ```

## <a name="update-your-container-image"></a>Обновление образа контейнера

Чтобы применить внесенные изменения, обновите образ контейнера, опубликуйте его и перезапустите IoT Edge.

1. В Visual Studio Code разверните папку **Docker**. 
2. В зависимости от используемой платформы разверните папку **windows-nano** или **linux-x64**. 
3. Щелкните правой кнопкой мыши файл **Dockerfile** и выберите **Build IoT Edge module Docker image** (Создать образ Docker модуля IoT Edge).
4. Перейдите в папку проекта **FilterFunction** и щелкните **Select Folder as EXE_DIR** (Выбрать папку в качестве каталога выполнения).
5. В верхней части окна VS Code введите имя образа в текстовом поле всплывающего окна. Например, `<your container registry address>/filterfunction:latest`. Если развертывание выполняется в локальный реестр, необходимо использовать имя `<localhost:5000/filterfunction:latest>`.
6. В палитре команд VS Code выберите **Edge: Push IoT Edge module Docker image** (Edge: передать образ Docker для модуля IoT Edge). 
7. В текстовом поле всплывающего окна введите то же имя образа. 
8. В палитре команд VS Code выберите **Edge: Restart Edge** (Edge: перезапустить Edge).

## <a name="view-the-local-data"></a>Просмотр локальных данных

После перезапуска контейнеров данные, полученные от датчиков температуры, сохраняются в локальной базе данных SQL Server 2017 на устройстве IoT Edge. 

В программе командной строки подключитесь к базе данных: 

* Контейнер Windows
   ```cmd
   docker exec -it sql cmd
   ```

* Контейнер Linux
   ```bash
   docker exec -it sql bash
   ```

Откройте программу командной строки для SQL: 

* Контейнер Windows
   ```cmd
   sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
   ```

* Контейнер Linux
   ```bash
   /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
   ```

Просмотрите данные: 

   ```sql
   SELECT * FROM MeasurementsDB.dbo.TemperatureMeasurements
   GO
   ```

## <a name="next-steps"></a>Дополнительная информация

* Узнайте, как [настроить образы контейнера SQL Server 2017 в Docker](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-docker).

* Посетите [репозиторий GitHub mssql-docker](https://github.com/Microsoft/mssql-docker), чтобы ознакомиться с ресурсами, отзывами и распространенными проблемами.
