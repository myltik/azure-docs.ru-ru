<properties
 pageTitle="Импорт и экспорт удостоверений устройств центра IoT | Microsoft Azure"
 description="Основные понятия и фрагменты кода .NET для массового управления удостоверениями устройств центра IoT"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="02/03/2016"
 ms.author="dobett"/>

# Массовое управление удостоверениями устройств центра IoT

В каждом центре IoT есть реестр удостоверений устройств, который можно использовать для создания в службе уникальных ресурсов устройства (например очередь с сообщениями, отправленными из облака в устройство) и который обеспечивает доступ к конечным точкам, обращенным к устройствам. В этой статье описывается массовый импорт удостоверений устройств в реестр удостоверений устройств и массовый экспорт удостоверений из реестра.

Операции импорта и экспорта выполняются в контексте *заданий*, позволяющих выполнять операции массового обслуживания в центре IoT.

Класс **RegistryManager** содержит методы **ExportDevicesAsync** и **ImportDevicesAsync**, которые используют платформу **заданий**. С помощью этих методов можно экспортировать, импортировать и синхронизировать весь реестр устройств центра IoT.

## Что такое задания?

Операции реестра удостоверений устройств используют систему **задания**, когда операция

*  характеризуется длительным временем выполнения по сравнению со стандартными операциями среды выполнения или
*  возвращает пользователю большой объем данных.

В этих случаях вместо одного вызова API-интерфейса, ожидающего или блокирующего результат операции, операция асинхронно создает **задание** для этого центра IoT и сразу же возвращает объект **JobProperties**.

В следующем фрагменте кода C# показано, как создать задание экспорта.

```
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await registryManager.ExportDevicesAsync(containerSasUri, false);
```

После этого можно использовать класс **RegistryManager** для запросов состояния **задания** с помощью возвращенных метаданных **JobProperties**.

В следующем фрагменте кода C# показано, как каждые пять секунд выполнять опрос, чтобы увидеть, завершено ли выполнение задания.

```
// Wait until job is finished
while(true)
{
  exportJob = await registryManager.GetJobAsync(exportJob.JobId);
  if (exportJob.Status == JobStatus.Completed || 
      exportJob.Status == JobStatus.Failed ||
      exportJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## Экспорт устройств

Используйте метод **ExportDevicesAsync** для экспорта всего реестра устройств центра IoT в контейнер BLOB-объектов [хранилища Azure](https://azure.microsoft.com/documentation/services/storage/) с помощью [подписанного URL-адреса](https://msdn.microsoft.com/library/ee395415.aspx).

Этот метод позволяет создавать надежные резервные копии данных устройства в контейнере BLOB-объектов, которым вы управляете.

Для использования метода **ExportDevicesAsync** требуется два параметра.

*  *Строковый*, содержащий URI контейнера BLOB-объектов. Этот URI должен содержать маркер SAS, который предоставляет доступ на запись в контейнер. Задание создает в этом контейнере блочный BLOB-объект для хранения сериализованных данных экспорта устройств. Маркер SAS должен включать следующие разрешения.
    
    ```
    SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Delete
    ```

*  *Логический*, указывающий, следует ли исключить из данных экспорта ключи проверки подлинности. Если задано значение **false**, ключи проверки подлинности включены в выходные данные экспорта. В противном случае экспортируются ключи со значением **NULL**.

В следующем фрагменте кода C# показано, как запустить задание экспорта, а затем выполнить опрос о завершении.

```
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await registryManager.ExportDevicesAsync(containerSasUri, false);

// Wait until job is finished
while(true)
{
    exportJob = await registryManager.GetJobAsync(exportJob.JobId);
    if (exportJob.Status == JobStatus.Completed || 
        exportJob.Status == JobStatus.Failed ||
        exportJob.Status == JobStatus.Cancelled)
    {
    // Job has finished executing
    break;
    }

    await Task.Delay(TimeSpan.FromSeconds(5));
}
```

Задание сохраняет выходные данные в указанном контейнере BLOB-объектов в виде блочного BLOB-объекта с именем **devices.txt**. Выходные данные состоят из сериализованных данных устройств JSON с одним устройством в строке.

Ниже приведен пример выходных данных.

```
{"id":"Device1","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device2","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device3","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device4","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device5","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
```

Если вам нужен доступ к этим данным в коде, их можно легко десериализовать с помощью класса **ExportImportDevice**. В следующем фрагменте кода C# показано, как считать сведения об устройстве, которые ранее были экспортированы в блочный BLOB-объект.

```
var exportedDevices = new List<ExportImportDevice>();

using (var streamReader = new StreamReader(await blob.OpenReadAsync(AccessCondition.GenerateIfExistsCondition(), RequestOptions, null), Encoding.UTF8))
{
  while (streamReader.Peek() != -1)
  {
    string line = await streamReader.ReadLineAsync();
    var device = JsonConvert.DeserializeObject<ExportImportDevice>(line);
    exportedDevices.Add(device);
  }
}
```

> [AZURE.NOTE]  Для получения списка устройств можно использовать метод **GetDevicesAsync** класса **RegistryManager**. Однако такой подход имеет жесткое ограничение в 1000 возвращаемых объектов устройств. Метод **GetDevicesAsync** предназначен для использования в сценариях разработки для упрощения отладки. Он не рекомендуется для производственных рабочих нагрузок.

## Импорт устройств

Метод **ImportDevicesAsync** в классе **RegistryManager** позволяет выполнять операции массового импорта и синхронизации в реестре устройств центра IoT. Как и метод **ExportDevicesAsync**, метод **ImportDevicesAsync** использует платформу заданий.

Использовать метод **ImportDevicesAsync** следует с осторожностью, так как наряду с подготовкой новых устройств в реестре удостоверений устройств он также может обновлять и удалять существующие устройства.

> [AZURE.WARNING]  Операцию импорта отменить нельзя. Прежде чем вносить массовые изменения в реестр удостоверений устройств, всегда необходимо создавать резервную копию существующих данных в другом контейнере BLOB-объектов с помощью метода **ExportDevicesAsync**.

Для использования метода **ImportDevicesAsync** требуется два параметра.

*  *Строковой*, содержащий URI контейнера BLOB-объектов [хранилища Azure](https://azure.microsoft.com/documentation/services/storage/), в качестве *входных данных* для задания. Этот URI должен содержать маркер SAS, который предоставляет доступ на чтение контейнера. Этот контейнер должен включать BLOB-объект с именем **devices.txt**, содержащий сериализованные данные устройств для импорта в реестр удостоверений устройств. Импортируемые данные должны включать в себя сведения об устройствах в том же формате JSON, который создает задание **ExportImportDevice**. Маркер SAS должен включать следующие разрешения.

    ```
    SharedAccessBlobPermissions.Read
    ```

*  *Строковой*, содержащий URI контейнера BLOB-объектов [хранилища Azure](https://azure.microsoft.com/documentation/services/storage/), в качестве *выходных данных* из задания. Задание создает в этом контейнере блочный BLOB-объект для хранения сведений об ошибках из завершенного **задания** импорта. Маркер SAS должен включать следующие разрешения.
    
    ```
    SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Delete
    ```

> [AZURE.NOTE]  Два параметра могут указывать на один и тот же контейнер BLOB-объектов. Отдельные параметры предоставляют больший уровень контроля над выходными данными, так как для выходного контейнера требуются дополнительные разрешения.

В следующем фрагменте кода C# показано, как инициировать задание импорта.

```
JobProperties importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);
```

## Поведение при импорте

Метод **ImportDevicesAsync** можно использовать для выполнения следующих массовых операций в реестре удостоверений устройств.

-   Массовая регистрация новых устройств
-   Массовое удаление существующих устройств
-   Массовое изменение состояния (включение или отключение устройств)
-   Массовое назначение новых ключей проверки подлинности устройств
-   Массовое автоматическое повторное создание ключей проверки подлинности устройств

Один вызов метода **ImportDevicesAsync** позволяет выполнять любое сочетание приведенных выше операций. Например, можно регистрировать новые устройства и одновременно удалять или обновлять существующие. Если этот метод использовать вместе с методом **ExportDevicesAsync**, можно полностью перенести все устройства из одного центра IoT в другой.

Управление процессом импорта на уровне каждого устройства осуществляется с помощью необязательного свойства **importMode** в сериализованных данных импорта для каждого устройства. Свойство **importMode** имеет следующие параметры:

| importMode | Описание |
| -------- | ----------- |
| **createOrUpdate** | Если устройство с указанным **идентификатором** не существует, оно регистрируется заново. <br/>Если устройство уже существует, имеющиеся данные заменяются предоставленными входными данными вне зависимости от значения **ETag**. |
| **создание** | Если устройство с указанным **идентификатором** не существует, оно регистрируется заново. <br/>Если устройство уже существует, в файл журнала записывается ошибка. |
| **обновить** | Если устройство с указанным **идентификатором** уже существует, имеющиеся данные заменяются предоставленными входными данными вне зависимости от значения **ETag**. <br/>Если устройство не существует, в файл журнала записывается ошибка. |
| **updateIfMatchETag** | Если устройство с указанным **идентификатором** уже существует, имеющиеся данные заменяются предоставленными входными данными только при совпадении **ETag**. <br/>Если устройство не существует, в файл журнала записывается ошибка. <br/>Если имеется несовпадение **ETag**, в файл журнала записывается ошибка. |
| **createOrUpdateIfMatchETag** | Если устройство с указанным **идентификатором** не существует, оно регистрируется заново. <br/>Если устройство уже существует, имеющиеся данные заменяются предоставленными входными данными только при совпадении **ETag**. <br/>Если имеется несовпадение **ETag**, в файл журнала записывается ошибка. |
| **delete** | Если устройство с указанным **идентификатором** уже существует, оно удаляется вне зависимости от значения **ETag**. <br/>Если устройство не существует, в файл журнала записывается ошибка. |
| **deleteIfMatchETag** | Если устройство с указанным **идентификатором** уже существует, оно удаляется только при совпадении **ETag**. Если устройство не существует, в файл журнала записывается ошибка. <br/>Если имеется несовпадение ETag, в файл журнала записывается ошибка. |

> [AZURE.NOTE] Если данные сериализации явно не определяют флаг **importMode** для устройства, то по умолчанию используется **createOrUpdate** во время операции импорта.

## Пример импорта устройств — массовая подготовка устройств 

В следующем примере кода C# показано, как создать несколько удостоверений устройств, которые включают ключи проверки подлинности, записывают сведения об этом устройстве в блочный BLOB-объект хранилища Azure и затем импортируют устройства в реестр удостоверений устройств.

```
// Provision 1,000 more devices
var serializedDevices = new List<string>();

for (var i = 0; i < 1000; i++)
{
// Create a new ExportImportDevice
  var deviceToAdd = new ExportImportDevice()
  {
    Id = Guid.NewGuid().ToString(),
    Status = DeviceStatus.Enabled,
    Authentication = new AuthenticationMechanism()
    {
      SymmetricKey = new SymmetricKey()
      {
        PrimaryKey = CryptoKeyGenerator.GenerateKey(32),
        SecondaryKey = CryptoKeyGenerator.GenerateKey(32)
      }
    },
    ImportMode = ImportMode.Create
  };

  // Add device to existing list
  serializedDevices.Add(JsonConvert.SerializeObject(deviceToAdd));
}

// Write this list to the Azure storage blob
var sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice => sb.AppendLine(serializedDevice));
await blob.DeleteIfExistsAsync();

using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Call import using the same storage blob to add new devices!
// This normally takes 1 minute per 100 devices the normal way
JobProperties importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## Пример импорта устройств — массовое удаление

В следующем примере кода показано, как удалить устройства, добавленные с помощью предыдущего примера кода.

```
// Step 1: Update each device's ImportMode to be Delete
sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice =>
{
  // Deserialize back to an ExportImportDevice
  var device = JsonConvert.DeserializeObject<ExportImportDevice>(serializedDevice);

  // Update property
  device.ImportMode = ImportMode.Delete;

  // Re-serialize
  sb.AppendLine(JsonConvert.SerializeObject(device));
});

// Step 2: Write the new import data back to the block blob
await blob.DeleteIfExistsAsync();
using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Step 3: Call import using the same storage blob to delete all devices!
importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}

```

## Получение URI SAS контейнера


В следующем примере кода показано, как создать [URI SAS](../storage/storage-dotnet-shared-access-signature-part-2.md) с разрешениями на чтение, запись и удаление контейнера BLOB-объектов.

```
static string GetContainerSasUri(CloudBlobContainer container)
{
  // Set the expiry time and permissions for the container.
  // In this case no start time is specified, so the
  // shared access signature becomes valid immediately.
  var sasConstraints = new SharedAccessBlobPolicy();
  sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
  sasConstraints.Permissions = 
    SharedAccessBlobPermissions.Write | 
    SharedAccessBlobPermissions.Read | 
    SharedAccessBlobPermissions.Delete;

  // Generate the shared access signature on the container,
  // setting the constraints directly on the signature.
  string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

  // Return the URI string for the container,
  // including the SAS token.
  return container.Uri + sasContainerToken;
}

```

## Дальнейшие действия

В этой статье вы узнали, как выполнять массовые операции с реестром удостоверений устройств в центре IoT. Дальнейшее описание функций и сценариев центра IoT см. в следующих статьях:

- [Созданию центра IoT программным образом](iot-hub-rm-template.md)
- [Метрики использования центра IoT](iot-hub-metrics.md)
- [Мониторинг операций центра IoT](iot-hub-operations-montoring.md)

<!---HONumber=AcomDC_0204_2016-->