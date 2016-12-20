---
title: "Подключение устройства с помощью C в Windows | Документация Майкрософт"
description: "Описывает, как подключить устройство к предварительно настроенному решению для удаленного мониторинга из набора Azure IoT Suite с помощью приложения на C в среде Windows."
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 34e39a58-2434-482c-b3fa-29438a0c05e8
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: af83b55210d1e31c548b9b178272f7e75be7b98e


---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-windows"></a>Подключение устройства к предварительно настроенному решению для удаленного мониторинга (Windows)
[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="create-a-c-sample-solution-on-windows"></a>Создание примера решения на C в Windows
Описанные ниже действия показывают, как с помощью Visual Studio создать клиентское приложение, написанное на языке C, которое взаимодействует с предварительно настроенным решением для удаленного мониторинга.

Создайте начальный проект в Visual Studio 2015 и добавьте клиентские пакеты NuGet для устройства центра IoT:

1. В Visual Studio 2015 создайте консольное приложение C, используя шаблон **консольного приложения Win32** в Visual C++. Присвойте проекту имя **RMDevice**.
2. На странице **Параметры приложения** в **мастере приложений Win32** необходимо установить флажок **Консольное приложение** и снять флажки **Предкомпилированный заголовок** и **Security Development Lifecycle (SDL)** (Жизненный цикл разработки безопасного ПО (SDL)).
3. В **обозревателе решений**удалите файлы stdafx.h, targetver.h и stdafx.cpp.
4. В **обозревателе решений**переименуйте файл RMDevice.cpp в RMDevice.c.
5. В **обозревателе решений** щелкните проект **RMDevice** правой кнопкой мыши и выберите пункт **Управление пакетами NuGet**. Щелкните **Обзор**, а затем найдите и установите в проект следующие пакеты NuGet:
   
   * Microsoft.Azure.IoTHub.Serializer
   * Microsoft.Azure.IoTHub.IoTHubClient
   * Microsoft.Azure.IoTHub.HttpTransport
6. В **обозревателе решений** щелкните правой кнопкой мыши проект **RMDevice**, а затем щелкните **Свойства**, чтобы открыть диалоговое окно **Страницы свойств** проекта. Дополнительные сведения см. в статье [Работа со свойствами проектов][lnk-c-project-properties]. 
7. Выберите папку **Компоновщик**, затем щелкните страницу свойств **Входные данные**.
8. В свойство **Дополнительные зависимости** добавьте **crypt32.lib**. Нажмите кнопку **ОК**, а затем еще раз **ОК**, чтобы сохранить значения свойств проекта.

## <a name="specify-the-behavior-of-the-iot-hub-device"></a>Определение поведения устройства центра IoT
Клиентские библиотеки Центра Интернета вещей используют модель для указания формата сообщений, отправляемых устройством в Центр Интернета вещей, и формата команд, которые оно получает из Центра Интернета вещей.

1. В Visual Studio откройте файл RMDevice.c. Замените имеющиеся инструкции `#include` в этом окне следующим кодом.
   
    ```
    #include "iothubtransporthttp.h"
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
    static const char* hubName = "[IoTHub Name]";
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

## <a name="implement-the-behavior-of-the-device"></a>Реализация поведения устройства
Теперь добавьте код, который будет реализовывать определенное в модели поведение.

1. Добавьте следующие функции, которые будут выполняться при получении устройством команд **SetTemperature** и **SetHumidity** из Центра Интернета вещей.
   
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
        config.iotHubName = hubName;
        config.iotHubSuffix = hubSuffix;
        config.protocol = HTTP_Protocol;
        config.deviceSasToken = NULL;
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
                  if (SERIALIZE(&buffer, &bufferSize, thermostat->ObjectType, thermostat->Version, thermostat->IsSimulatedDevice, thermostat->DeviceProperties, thermostat->Commands) != IOT_AGENT_OK)
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
   
                if (SERIALIZE(&buffer, &bufferSize, thermostat->DeviceId, thermostat->Temperature, thermostat->Humidity, thermostat->ExternalTemperature) != IOT_AGENT_OK)
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
5. Замените функцию **main** следующим кодом для вызова функции **remote_monitoring_run**:
   
    ```
    int main()
    {
      remote_monitoring_run();
      return 0;
    }
    ```
6. Щелкните **Построить**, а затем **Построить решение**, чтобы выполнить сборку приложения устройства.
7. В **обозревателе решений** щелкните правой кнопкой мыши проект **RMDevice**, нажмите кнопку **Отладка**, а затем щелкните **Запустить новый экземпляр**, чтобы запустить пример. В консоли отображаются сообщения об отправке приложением примеров данных телеметрии в центр IoT.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-c-project-properties]: https://msdn.microsoft.com/library/669zx6zc.aspx



<!--HONumber=Nov16_HO3-->


