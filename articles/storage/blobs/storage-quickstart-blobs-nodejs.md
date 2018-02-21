---
title: "Краткое руководство по Azure. Передача объектов в хранилище BLOB-объектов Azure и обратно с помощью Node.js | Документация Майкрософт"
description: "Краткие сведения о передаче объектов в хранилище BLOB-объектов Azure и обратно с помощью Node.js"
services: storage
author: tamram
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 02/06/2018
ms.author: tamram
ms.openlocfilehash: 07845d0e1917c00dbd6098ef2bfbd9dcbbf2f97b
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/09/2018
---
# <a name="transfer-objects-tofrom-azure-blob-storage-using-nodejs"></a>Передача объектов в хранилище BLOB-объектов Azure и обратно с помощью Node.js

Из этого краткого руководства вы узнаете, как использовать Node.js для передачи, скачивания и перечисления блочных BLOB-объектов в контейнере в хранилище BLOB-объектов Azure.

## <a name="prerequisites"></a>предварительным требованиям

Для работы с этим кратким руководством сделайте следующее:

* Установите [Node.js](https://nodejs.org/en/)

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>Загрузка примера приложения

[Пример приложения](https://github.com/Azure-Samples/storage-blobs-node-quickstart.git), используемый в этом кратком руководстве, является простым консольным приложением. 

Используйте команду [git](https://git-scm.com/), чтобы скачать копию приложения в среду разработки.

```bash
git clone https://github.com/Azure-Samples/storage-blobs-node-quickstart.git
```

Эта команда клонирует репозиторий в локальную папку git. Чтобы открыть приложение, найдите папку "storage-blobs-node-quickstart", откройте ее и дважды щелкните index.js.

## <a name="configure-your-storage-connection-string"></a>Настройка строки подключения хранилища

В приложении необходимо указать строку подключения для учетной записи хранения. Откройте файл `index.js` и найдите переменную `connectionString`. Полностью замените значение строки подключения на значение, сохраненное с портала Azure. Строка подключения хранилища должна выглядеть так:

```javascript
// Create a blob client for interacting with the blob service from connection string
// How to create a storage connection string - http://msdn.microsoft.com/library/azure/ee758697.aspx
var connectionString = '<Your connection string here>';
var blobService = storage.createBlobService(connectionString);
```

## <a name="install-required-packages"></a>Установка необходимых пакетов

В каталоге приложения запустите `npm install`, чтобы установить все необходимые пакеты, перечисленные в файле `package.json`.

```javascript
npm install
```

## <a name="run-the-sample"></a>Запуск примера

В этом примере создается тестовый файл в папке "My Documents", затем он отправляется в хранилище BLOB-объектов, после чего выводится список BLOB-объектов в контейнере, а затем файл загружается с новым именем, чтобы можно было сравнить старый и новый файлы.

Для запуска примера введите `node index.js`. Ниже приведены выходные данные из системы Windows.  Такие же выходные данные с соответствующими путями к файлам можно получить при использовании Linux.

```
Azure Storage Node.js Client Library Blobs Quick Start

1. Creating a container with public access: quickstartcontainer-79a3eea0-bec9-11e7-9a36-614cd00ca63d

2. Creating a file in ~/Documents folder to test the upload and download

   Local File: C:\Users\admin\Documents\HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d.txt

3. Uploading BlockBlob: quickstartblob-HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d.txt

   Uploaded Blob URL: https://mystorageaccount.blob.core.windows.net/quickstartcontainer-79a3eea0-bec9-11e7-9a36-614cd00ca63d/quickstartblob-HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d.txt

4. Listing blobs in container

   - quickstartblob-HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d.txt (type: BlockBlob)


5. Downloading blob

   Downloaded File: C:\Users\admin\Documents\HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d_DOWNLOADED.txt

Sample finished running. When you hit <ENTER> key, the temporary files will be deleted and the sample application will exit.
```

Прежде чем продолжить, проверьте наличие двух файлов в папке "Мои документы". Вы можете открыть их и убедиться, что они идентичны.

Для просмотра файлов в хранилище BLOB-объектов можно также воспользоваться таким средством, как [обозреватель службы хранилища Azure](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Обозреватель службы хранилища Azure — это бесплатное кроссплатформенное средство для доступа к данным учетной записи хранения.

После проверки файлов нажмите любую клавишу для завершения демонстрации и удаления тестовых файлов. Теперь, когда известна функциональность примера, откройте файл index.js, чтобы взглянуть на код. 

## <a name="understand-the-sample-code"></a>Разбор примера кода

Разберем пример кода, чтобы понять, как он работает.

### <a name="get-references-to-the-storage-objects"></a>Получение ссылок на объекты хранилища

Сначала необходимо создать ссылки на **BlobService**, используемые для доступа к хранилищу BLOB-объектов и управлению им. Эти объекты зависят друг от друга — каждый объект используется следующим в списке объектом.

* Создайте экземпляр объекта [BlobService](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService__ctor), указывающий на службу BLOB-объектов в учетной записи хранения.

* Создайте контейнер, а затем задайте для него разрешения, чтобы предоставить общий доступ к большим двоичным объектам по URL-адресу. Название контейнера начинается с **quickstartcontainer-**.

В этом примере используется [createContainerCreateIfNotExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createContainerIfNotExists), так как при каждом запуске примера требуется создавать новый контейнер. В рабочей среде, где в рамках приложения используется один и тот же контейнер, рекомендуется вызвать "CreateIfNotExists" только один раз. Вы можете также создать контейнер заранее, чтобы не создавать его в коде.

```javascript
// Create a container for organizing blobs within the storage account.
console.log('1. Creating a Container with Public Access:', blockBlobContainerName, '\n');
blobService.createContainerIfNotExists(blockBlobContainerName, { 'publicAccessLevel': 'blob' }, function (error) {
    if (error) return callback(error);
```

### <a name="upload-blobs-to-the-container"></a>Отправка BLOB-объектов в контейнер

Хранилище BLOB-объектов поддерживает блочные, добавочные и страничные BLOB-объекты. Чаще всего используются блочные BLOB-объекты. В этом кратком руководстве они используются, так как идеально подходят для хранения текста и двоичных данных.

Чтобы передать файл в большой двоичный объект, используйте метод [createBlockBlobFromLocalFile](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_createBlockBlobFromLocalFile). Эта операция создает BLOB-объект, если он еще не существует, или заменяет его, если он существует.

В примере кода создается локальный файл, который будет использоваться для отправки и скачивания, отправляемый файл сохраняется как **localPath** и большому двоичному объекту задается имя в **localFileToUpload**. В приведенном ниже примере файл отправляется в контейнер, имя которого начинается с **quickstartcontainer-**.

```javascript
console.log('2. Creating a file in ~/Documents folder to test the upload and download\n');
console.log('   Local File:', LOCAL_FILE_PATH, '\n');
fs.writeFileSync(LOCAL_FILE_PATH, 'Greetings from Microsoft!');

console.log('3. Uploading BlockBlob:', BLOCK_BLOB_NAME, '\n');
blobService.createBlockBlobFromLocalFile(CONTAINER_NAME, BLOCK_BLOB_NAME, LOCAL_FILE_PATH, function (error) {
handleError(error);
console.log('   Uploaded Blob URL:', blobService.getUrl(CONTAINER_NAME, BLOCK_BLOB_NAME), '\n');
```

Существует несколько методов отправки, которые можно использовать с хранилищем BLOB-объектов. Например, если у вас есть поток в памяти, можно применить метод [createBlockBlobFromStream](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_createBlockBlobFromStream) вместо [createBlockBlobFromLocalFile](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_createBlockBlobFromLocalFile).

### <a name="list-the-blobs-in-a-container"></a>Перечисление BLOB-объектов в контейнере

Затем приложение получает список файлов в контейнере с помощью метода [listBlobsSegmented](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_listBlobsSegmented). Следующий код извлекает список BLOB-объектов, затем переходит по ним, отображая найденные URI. Можно скопировать URI из окна командной строки и вставить его в адресную строку браузера для просмотра файла.

При наличии в контейнере до 5000 больших двоичных объектов все их имена извлекаются в одном вызове метода [listBlobsSegmented](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_listBlobsSegmented). При наличии в контейнере более 5 000 BLOB-объектов служба получает список наборами по 5 000 объектов вплоть до извлечения всех имен BLOB-объектов. Таким образом, при первом вызове API возвращаются первые 5 000 имен BLOB-объектов и маркер продолжения. Во второй раз вы указываете маркер, служба получает следующий набор имен BLOB-объектов, и так продолжается до тех пор, пока значение маркера продолжения не будет равно NULL, означающему, что были получены все имена BLOB-объектов.

```javascript
console.log('4. Listing blobs in container\n');
blobService.listBlobsSegmented(CONTAINER_NAME, null, function (error, data) {
    handleError(error);

    for (var i = 0; i < data.entries.length; i++) {
    console.log(util.format('   - %s (type: %s)'), data.entries[i].name, data.entries[i].blobType);
    }
    console.log('\n');
```

### <a name="download-blobs"></a>Скачивание больших двоичных объектов

Чтобы скачать большие двоичные объекты на локальный диск, воспользуйтесь методом [getBlobToLocalFile](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_getBlobToLocalFile).

Следующий код скачивает BLOB-объект, отправленный в предыдущем разделе, добавляя к имени BLOB-объекта суффикс "_DOWNLOADED", чтобы вы увидели оба файла на локальном диске. 

```javascript
console.log('5. Downloading blob\n');
blobService.getBlobToLocalFile(CONTAINER_NAME, BLOCK_BLOB_NAME, DOWNLOADED_FILE_PATH, function (error) {
handleError(error);
console.log('   Downloaded File:', DOWNLOADED_FILE_PATH, '\n');
```

### <a name="clean-up-resources"></a>Очистка ресурсов

Если вам больше не нужны большие двоичные объекты, отправленные при работе с этим руководством, удалите весь контейнер с помощью метода [deleteBlobIfExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_deleteBlobIfExists) и [deleteContainerIfExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_deleteContainerIfExists). Кроме того, удалите созданные файлы, если они больше не требуются. Это осуществляется, когда вы нажимаете клавишу ВВОД для выхода из приложения.

```javascript
console.log('6. Deleting block Blob\n');
    blobService.deleteBlobIfExists(CONTAINER_NAME, BLOCK_BLOB_NAME, function (error) {
        handleError(error);

    console.log('7. Deleting container\n');
    blobService.deleteContainerIfExists(CONTAINER_NAME, function (error) {
        handleError(error);
            
        fs.unlinkSync(LOCAL_FILE_PATH);
        fs.unlinkSync(DOWNLOADED_FILE_PATH);
```

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы узнали, как передавать файлы между локальным диском и хранилищем BLOB-объектов Azure с помощью Node.js. Дополнительные сведения о работе с хранилищем BLOB-объектов см. в соответствующем практическом руководстве.

> [!div class="nextstepaction"]
> [Практическое руководство по операциям в хранилище BLOB-объектов](storage-nodejs-how-to-use-blob-storage.md)

Справочник по Node.js для хранилища Azure см. в пакете [azure-storage](https://docs.microsoft.com/javascript/api/azure-storage/?view=azure-node-latest).