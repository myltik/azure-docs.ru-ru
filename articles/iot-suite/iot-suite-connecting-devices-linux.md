---
title: "Подключение устройства с помощью C в Linux | Документация Майкрософт"
description: "Описывает, как подключить устройство к предварительно настроенному решению для удаленного мониторинга из набора Azure IoT Suite с помощью приложения на C в среде Linux."
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 0c7c8039-0bbf-4bb5-9e79-ed8cff433629
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 6fdcdc323cff07d7debd46ab10b17ba7e9d8781a
ms.openlocfilehash: d1a6ffe59c5cb4fd0575ba02aa9768548461b869


---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-linux"></a>Подключение устройства к предварительно настроенному решению для удаленного мониторинга (Linux)
[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="build-and-run-a-sample-c-client-linux"></a>Сборка и запуск примера клиента Linux на C
Ниже описывается, как создать клиентское приложение на языке C, созданное и запущенное на устройстве Ubuntu Linux, которое взаимодействует с предварительно настроенным решением для удаленного мониторинга. Для выполнения этих действий необходимо устройство под управлением Ubuntu версии 15.04 или 15.10. Прежде чем продолжить, установите необходимые пакеты на устройство Ubuntu, используя следующую команду.

```
sudo apt-get install cmake gcc g++
```

## <a name="install-the-client-libraries-on-your-device"></a>Установка клиентских библиотек на устройство
Клиентские библиотеки центра IoT Azure доступны в виде пакета, который можно установить на устройство Ubuntu с помощью команды **apt-get** . Выполните следующие действия, чтобы установить пакет, содержащий файлы клиентских библиотек центра IoT Azure и заголовков, на компьютер Ubuntu.

1. Добавьте репозиторий AzureIoT на компьютер.
   
    ```
    sudo add-apt-repository ppa:aziotsdklinux/ppa-azureiot
    sudo apt-get update
    ```
2. Установите пакет azure-iot-sdk-c-dev.
   
    ```
    sudo apt-get install -y azure-iot-sdk-c-dev
    ```

## <a name="add-code-to-specify-the-behavior-of-the-device"></a>Добавление кода для настройки поведения устройства
На компьютере Ubuntu создайте папку **remote\_monitoring**. В папке **remote\_monitoring** создайте четыре файла: **main.c**, **remote\_monitoring.c**, **remote\_monitoring.h** и **CMakeLists.txt**.

Клиентские библиотеки сериализатора Центра Интернета вещей используют модель для указания формата сообщений, отправляемых устройством в Центр Интернета вещей, и формата команд, которые оно получает из Центра Интернета вещей.

1. Откройте в текстовом редакторе файл **remote\_monitoring.c**. Добавьте следующие операторы `#include` :
   
    ```
    #include "iothubtransportamqp.h"
    #include "schemalib.h"
    #include "iothub_client.h"
    #include "serializer.h"
    #include "schemaserializer.h"
    #include "azure_c_shared_utility/threadapi.h"
    #include "azure_c_shared_utility/platform.h"
    ```
2. После операторов `#include` добавьте указанные ниже объявления переменных. Замените значения заполнителей [Device Id] и [Device Key] значениями для устройства, отображенными на панели мониторинга решения для удаленного мониторинга. Замените [IoTHub Name] именем узла центра IoT, которое отображается на панели мониторинга. Например, если имя узла Центра Интернета вещей — **contoso.azure-devices.net**, замените [имя_Центра_Интернета_вещей] на **contoso**.
   
    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "IoTHub Name]";
    static const char* hubSuffix = "azure-devices.net";
    ```
3. Добавьте указанный ниже код для определения модели, позволяющей устройству взаимодействовать с центром IoT. Эта модель указывает, что устройство передает температуру, температуру окружающей среды, влажность и идентификатор устройства в виде телеметрических данных. Кроме того, устройство отправляет в центр IoT свои метаданные, включая список поддерживаемых устройством команд. Это устройство отвечает на команды **SetTemperature** и **SetHumidity**.
   
    ```
    // Define the Model
    BEGIN_NAMESPACE(Contoso);
   
    DECLARE_STRUCT(SystemProperties,
      ascii_char_ptr, DeviceID,
      _Bool, Enabled
    );
   
    DECLARE_STRUCT(DeviceProperties,
      ascii_char_ptr, DeviceID,
      _Bool, HubEnabledState
    );
   
    DECLARE_MODEL(Thermostat,
   
      /* Event data (temperature, external temperature and humidity) */
      WITH_DATA(int, Temperature),
      WITH_DATA(int, ExternalTemperature),
      WITH_DATA(int, Humidity),
      WITH_DATA(ascii_char_ptr, DeviceId),
   
      /* Device Info - This is command metadata + some extra fields */
      WITH_DATA(ascii_char_ptr, ObjectType),
      WITH_DATA(_Bool, IsSimulatedDevice),
      WITH_DATA(ascii_char_ptr, Version),
      WITH_DATA(DeviceProperties, DeviceProperties),
      WITH_DATA(ascii_char_ptr_no_quotes, Commands),
   
      /* Commands implemented by the device */
      WITH_ACTION(SetTemperature, int, temperature),
      WITH_ACTION(SetHumidity, int, humidity)
    );
   
    END_NAMESPACE(Contoso);
    ```

### <a name="add-code-to-implement-the-behavior-of-the-device"></a>Добавление кода для реализации поведения устройства
Добавьте функции, которые будут выполняться, когда устройство получит команду из центра, и код для отправки имитированной телеметрии в центр.

1. Добавьте следующие функции, которые будут выполняться при получении устройством определенных в модели команд **SetTemperature** и **SetHumidity**.
   
    ```
    EXECUTE_COMMAND_RESULT SetTemperature(Thermostat* thermostat, int temperature)
    {
      (void)printf("Received temperature %d\r\n", temperature);
      thermostat->Temperature = temperature;
      return EXECUTE_COMMAND_SUCCESS;
    }
   
    EXECUTE_COMMAND_RESULT SetHumidity(Thermostat* thermostat, int humidity)
    {
      (void)printf("Received humidity %d\r\n", humidity);
      thermostat->Humidity = humidity;
      return EXECUTE_COMMAND_SUCCESS;
    }
    ```
2. Добавьте следующую функцию, которая отправляет сообщение в центр IoT:
   
    ```
    static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
    {
      IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
      if (messageHandle == NULL)
      {
        printf("unable to create a new IoTHubMessage\r\n");
      }
      else
      {
        if (IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, NULL, NULL) != IOTHUB_CLIENT_OK)
        {
          printf("failed to hand over the message to IoTHubClient");
        }
        else
        {
          printf("IoTHubClient accepted the message for delivery\r\n");
        }
   
        IoTHubMessage_Destroy(messageHandle);
      }
    free((void*)buffer);
    }
    ```
3. Добавьте следующую функцию, которая подключает библиотеку сериализации в пакете SDK:
   
    ```
    static IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
    {
      IOTHUBMESSAGE_DISPOSITION_RESULT result;
      const unsigned char* buffer;
      size_t size;
      if (IoTHubMessage_GetByteArray(message, &buffer, &size) != IOTHUB_MESSAGE_OK)
      {
        printf("unable to IoTHubMessage_GetByteArray\r\n");
        result = EXECUTE_COMMAND_ERROR;
      }
      else
      {
        /*buffer is not zero terminated*/
        char* temp = malloc(size + 1);
        if (temp == NULL)
        {
          printf("failed to malloc\r\n");
          result = EXECUTE_COMMAND_ERROR;
        }
        else
        {
          memcpy(temp, buffer, size);
          temp[size] = '\0';
          EXECUTE_COMMAND_RESULT executeCommandResult = EXECUTE_COMMAND(userContextCallback, temp);
          result =
            (executeCommandResult == EXECUTE_COMMAND_ERROR) ? IOTHUBMESSAGE_ABANDONED :
            (executeCommandResult == EXECUTE_COMMAND_SUCCESS) ? IOTHUBMESSAGE_ACCEPTED :
            IOTHUBMESSAGE_REJECTED;
          free(temp);
        }
      }
      return result;
    }
    ```
4. Добавьте указанную ниже функцию, которая позволяет подключаться к центру IoT, отправлять и получать сообщения и отключаться от центра. Обратите внимание, как устройство отправляет метаданные о себе, а также о поддерживаемых командах в Центр Интернета вещей при подключении. С помощью этих метаданных решение может обновить состояние устройства до **Запущено** на панели мониторинга:
   
    ```
    void remote_monitoring_run(void)
    {
      if (platform_init() != 0)
      {
        printf("Failed to initialize the platform.\r\n");
      }
      else
      {
        if (serializer_init(NULL) != SERIALIZER_OK)
        {
          printf("Failed on serializer_init\r\n");
        }
        else
        {
          IOTHUB_CLIENT_CONFIG config;
          IOTHUB_CLIENT_HANDLE iotHubClientHandle;
   
          config.deviceId = deviceId;
          config.deviceKey = deviceKey;
          config.deviceSasToken = NULL;
          config.iotHubName = hubName;
          config.iotHubSuffix = hubSuffix;
          config.protocol = AMQP_Protocol;
          config.protocolGatewayHostName = NULL;
          iotHubClientHandle = IoTHubClient_Create(&config);
          if (iotHubClientHandle == NULL)
          {
            (void)printf("Failed on IoTHubClient_CreateFromConnectionString\r\n");
          }
          else
          {
            Thermostat* thermostat = CREATE_MODEL_INSTANCE(Contoso, Thermostat);
            if (thermostat == NULL)
            {
              (void)printf("Failed on CREATE_MODEL_INSTANCE\r\n");
            }
            else
            {
              STRING_HANDLE commandsMetadata;
   
              if (IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, thermostat) != IOTHUB_CLIENT_OK)
              {
                printf("unable to IoTHubClient_SetMessageCallback\r\n");
              }
              else
              {
   
                /* send the device info upon startup so that the cloud app knows
                   what commands are available and the fact that the device is up */
                thermostat->ObjectType = "DeviceInfo";
                thermostat->IsSimulatedDevice = false;
                thermostat->Version = "1.0";
                thermostat->DeviceProperties.HubEnabledState = true;
                thermostat->DeviceProperties.DeviceID = (char*)deviceId;
   
                commandsMetadata = STRING_new();
                if (commandsMetadata == NULL)
                {
                  (void)printf("Failed on creating string for commands metadata\r\n");
                }
                else
                {
                  /* Serialize the commands metadata as a JSON string before sending */
                  if (SchemaSerializer_SerializeCommandMetadata(GET_MODEL_HANDLE(Contoso, Thermostat), commandsMetadata) != SCHEMA_SERIALIZER_OK)
                  {
                    (void)printf("Failed serializing commands metadata\r\n");
                  }
                  else
                  {
                    unsigned char* buffer;
                    size_t bufferSize;
                    thermostat->Commands = (char*)STRING_c_str(commandsMetadata);
   
                    /* Here is the actual send of the Device Info */
                    if (SERIALIZE(&buffer, &bufferSize, thermostat->ObjectType, thermostat->Version, thermostat->IsSimulatedDevice, thermostat->DeviceProperties, thermostat->Commands) != CODEFIRST_OK)
                    {
                      (void)printf("Failed serializing\r\n");
                    }
                    else
                    {
                      sendMessage(iotHubClientHandle, buffer, bufferSize);
                    }
   
                  }
   
                  STRING_delete(commandsMetadata);
                }
   
                thermostat->Temperature = 50;
                thermostat->ExternalTemperature = 55;
                thermostat->Humidity = 50;
                thermostat->DeviceId = (char*)deviceId;
   
                while (1)
                {
                  unsigned char*buffer;
                  size_t bufferSize;
   
                  (void)printf("Sending sensor value Temperature = %d, Humidity = %d\r\n", thermostat->Temperature, thermostat->Humidity);
   
                  if (SERIALIZE(&buffer, &bufferSize, thermostat->DeviceId, thermostat->Temperature, thermostat->Humidity, thermostat->ExternalTemperature) != CODEFIRST_OK)
                  {
                    (void)printf("Failed sending sensor value\r\n");
                  }
                  else
                  {
                    sendMessage(iotHubClientHandle, buffer, bufferSize);
                  }
   
                  ThreadAPI_Sleep(1000);
                }
              }
   
              DESTROY_MODEL_INSTANCE(thermostat);
            }
            IoTHubClient_Destroy(iotHubClientHandle);
          }
          serializer_deinit();
        }
        platform_deinit();
      }
    }
    ```
   
    Для справки ниже приведен пример сообщения **DeviceInfo**, которое отправляется в Центр Интернета вещей при запуске устройства.
   
    ```
    {
      "ObjectType":"DeviceInfo",
      "Version":"1.0",
      "IsSimulatedDevice":false,
      "DeviceProperties":
      {
        "DeviceID":"mydevice01", "HubEnabledState":true
      }, 
      "Commands":
      [
        {"Name":"SetHumidity", "Parameters":[{"Name":"humidity","Type":"double"}]},
        { "Name":"SetTemperature", "Parameters":[{"Name":"temperature","Type":"double"}]}
      ]
    }
    ```
   
    Для справки ниже приведен пример сообщения **Telemetry** , которое отправляется в центр IoT.
   
    ```
    {"DeviceId":"mydevice01", "Temperature":50, "Humidity":50, "ExternalTemperature":55}
    ```
   
    Для справки ниже приведен пример **команды** , получаемой из центра IoT.
   
    ```
    {
      "Name":"SetHumidity",
      "MessageId":"2f3d3c75-3b77-4832-80ed-a5bb3e233391",
      "CreatedTime":"2016-03-11T15:09:44.2231295Z",
      "Parameters":{"humidity":23}
    }
    ```

### <a name="add-code-to-invoke-the-remotemonitoringrun-function"></a>Добавление кода для вызова функции remote_monitoring_run
Откройте в текстовом редакторе файл **remote_monitoring.h**. Добавьте следующий код:

```
void remote_monitoring_run(void);
```

Откройте в текстовом редакторе файл **main.c** . Добавьте следующий код:

```
#include "remote_monitoring.h"

int main(void)
{
    remote_monitoring_run();

    return 0;
}
```

## <a name="use-cmake-to-build-the-client-application"></a>Создание клиентского приложения с помощью CMake
Ниже приведены указания по использованию *CMake* для создания клиентского приложения.

1. Откройте в текстовом редакторе файл **CMakeLists.txt** из папки **remote_monitoring**.
2. Добавьте следующие операторы, чтобы определить способ сборки клиентского приложения.
   
    ```
    cmake_minimum_required(VERSION 2.8.11)
   
    set(AZUREIOT_INC_FOLDER ".." "/usr/include/azureiot" "/usr/include/azureiot/inc")
   
    include_directories(${AZUREIOT_INC_FOLDER})
   
    set(sample_application_c_files
        ./remote_monitoring.c
        ./main.c
    )
   
    set(sample_application_h_files
        ./remote_monitoring.h
    )
   
    add_executable(sample_app ${sample_application_c_files} ${sample_application_h_files})
   
    target_link_libraries(sample_app
        serializer
        iothub_client
        iothub_client_amqp_transport
        aziotsharedutil
        uamqp
        pthread
        curl
        ssl
        crypto
        m
    )
    ```
3. В папке **remote_monitoring** создайте папку для хранения файлов *make*, которые создает CMake, а затем выполните команды **cmake** и **make**, как показано ниже.
   
    ```
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```
4. Запустите клиентское приложение и отправьте данные телеметрии в центр IoT.
   
    ```
    ./sample_app
    ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]




<!--HONumber=Nov16_HO3-->


