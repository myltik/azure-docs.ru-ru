---
title: Передача данных изображений в облако с помощью службы хранилища Azure | Документация Майкрософт
description: Использование хранилища больших двоичных объектов Azure с веб-приложением для хранения данных приложения
services: storage
documentationcenter: ''
author: tamram
manager: jeconnoc
ms.service: storage
ms.workload: web
ms.devlang: csharp
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: tamram
ms.custom: mvc
ms.openlocfilehash: 29accb3394e9a2f6939a657172c1a5c2e411706a
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/11/2018
---
# <a name="upload-image-data-in-the-cloud-with-azure-storage"></a>Передача данных изображений в облако с помощью службы хранилища Azure

Это руководство представляет первую часть цикла. В этом руководстве показано, как развернуть веб-приложение, использующее клиентскую библиотеку службы хранилища Azure, для отправки изображений в учетную запись хранения. По окончании у вас будет веб-приложение, которое хранит и показывает изображения из службы хранилища Azure.

![Представление контейнера изображений](media/storage-upload-process-images/figure2.png)

В первой части цикла вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * Создайте учетную запись хранения.
> * Создание контейнера и настройка разрешений.
> * Получение ключа доступа.
> * Настройка параметров приложения
> * Развертывание веб-приложения в Azure.
> * Взаимодействие с веб-приложением.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этим руководством вам понадобится Azure CLI 2.0.4 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Создание группы ресурсов 

Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#az_group_create). Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими.
 
В следующем примере создается группа ресурсов `myResourceGroup`.
 
```azurecli-interactive 
az group create --name myResourceGroup --location westcentralus 
``` 

## <a name="create-a-storage-account"></a>Создайте учетную запись хранения.
 
В примере изображения отправляются в контейнер больших двоичных объектов в учетной записи хранения Azure. Учетная запись хранения предоставляет уникальное пространство имен для хранения ваших объектов данных в службе хранилища Azure и доступа к ним. Создайте учетную запись хранения в созданной вами группе ресурсов с помощью команды [az storage account create](/cli/azure/storage/account#az_storage_account_create). 

> [!IMPORTANT] 
> Во второй части этого руководства используются подписки на события для хранилища BLOB-объектов. Сейчас подписки на события поддерживаются только для учетных записей хранения больших двоичных объектов в таких регионах, как "Западно-центральная часть США" и "Западная часть США 2". Из-за этого ограничения необходимо создать учетную запись хранения больших двоичных объектов, используемую в примере приложения для хранения изображений и эскизов.   

В следующей команде замените `<blob_storage_account>` глобально уникальным именем учетной записи хранения больших двоичных объектов везде, где встречается этот заполнитель.  

```azurecli-interactive 
az storage account create --name <blob_storage_account> \
--location westcentralus --resource-group myResourceGroup \
--sku Standard_LRS --kind blobstorage --access-tier hot 
``` 
 
## <a name="create-blob-storage-containers"></a>Создание контейнеров хранилища BLOB-объектов
 
Приложение использует два типа контейнеров в учетной записи хранилища больших двоичных объектов. Контейнеры похожи на папки и используются для хранения больших двоичных объектов. Контейнер _изображений_ — это контейнер, в который отправляются изображения с высоким разрешением. В одной из последующих статей этой серии приложение-функция Azure будет отправлять эскизы изображений измененного размера в контейнер _эскизов_. 

Получите ключи своей учетной записи хранения с помощью команды [az storage account keys list](/cli/azure/storage/account/keys#az_storage_account_keys_list). Затем используйте этот ключ для создания двух контейнеров с помощью команды [az storage container create](/cli/azure/storage/container#az_storage_container_create).  
 
В данном случае `<blob_storage_account>` — это имя созданной учетной записи хранения больших двоичных объектов. Для общего доступа к контейнерам _изображений_ задано значение `off`, а для контейнеров _эскизов_ — `container`. Параметр общего доступа `container` делает эскизы видимыми для пользователей, посещающих веб-страницу.
 
```azurecli-interactive 
$blobStorageAccount="<blob_storage_account>"

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
-n $blobStorageAccount --query [0].value --output tsv) 

az storage container create -n images --account-name $blobStorageAccount \
--account-key $blobStorageAccountKey --public-access off 

az storage container create -n thumbnails --account-name $blobStorageAccount \
--account-key $blobStorageAccountKey --public-access container

echo "Make a note of your blob storage account key..." 
echo $blobStorageAccountKey 
``` 

Запишите имя и ключ учетной записи хранения больших двоичных объектов. В этом примере приложения эти параметры используются для подключения к учетной записи хранения и отправки изображений. 

## <a name="create-an-app-service-plan"></a>Создание плана службы приложений 

[План службы приложений](../../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) указывает расположение, размер и функции фермы веб-серверов, в которой размещается приложение. 

Создайте план службы приложений, выполнив команду [az appservice plan create](/cli/azure/appservice/plan#az_appservice_plan_create). 

В следующем примере создается план службы приложений с именем `myAppServicePlan` и ценовой категорией **Бесплатный**. 

```azurecli-interactive 
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE 
``` 

## <a name="create-a-web-app"></a>Создание веб-приложения 

Веб-приложение предоставляет пространство для размещения примера кода приложения, развернутого из примера репозитория GitHub. Создайте [веб-приложение](../../app-service/app-service-web-overview.md) в рамках плана `myAppServicePlan` службы приложений с помощью команды [az webapp create](/cli/azure/webapp#az_webapp_create).  
 
В следующей команде замените `<web_app>` уникальным именем (допустимые символы: `a-z`, `0-9` и `-`). Если `<web_app>` не является уникальным, отобразится следующее сообщение об ошибке: _Веб-сайт с указанным именем `<web_app>` уже существует_. URL-адрес приложения по умолчанию: `https://<web_app>.azurewebsites.net`.  

```azurecli-interactive 
az webapp create --name <web_app> --resource-group myResourceGroup --plan myAppServicePlan 
``` 

## <a name="deploy-the-sample-app-from-the-github-repository"></a>Развертывание примера приложения из репозитория GitHub 

Служба приложений поддерживает несколько способов развертывания содержимого для веб-приложения. В этом руководстве развертывается веб-приложение из [общедоступного репозитория примеров GitHub](https://github.com/Azure-Samples/storage-blob-upload-from-webapp). Настройте для веб-приложения развертывание GitHub с помощью команды [az webapp deployment source config](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config). Замените `<web_app>` именем веб-приложения, созданного на предыдущем шаге.

В примере проекта содержится приложение [ASP.NET MVC](https://www.asp.net/mvc), которое принимает изображения, сохраняет их в учетную запись хранения и показывает изображения из контейнера эскизов. Веб-приложение использует пространства имен [Microsoft.WindowsAzure.Storage](/dotnet/api/microsoft.windowsazure.storage?view=azure-dotnet), [Microsoft.WindowsAzure.Storage.Blob](/dotnet/api/microsoft.windowsazure.storage.blob?view=azure-dotnet) и [Microsoft.WindowsAzure.Storage.Auth](/dotnet/api/microsoft.windowsazure.storage.auth?view=azure-dotnet) из клиентской библиотеки службы хранилища Azure для взаимодействия со службой хранилища Azure. 

```azurecli-interactive 
az webapp deployment source config --name <web_app> \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
``` 

## <a name="configure-web-app-settings"></a>Настройка параметров веб-приложения 

В примере приложения [клиентская библиотека хранилища Azure](/dotnet/api/overview/azure/storage?view=azure-dotnet) используется для запроса маркеров доступа, которые используются для отправки изображений. Учетные данные записи хранения, используемые пакетом SDK хранилища, задаются в параметрах веб-приложения. Добавить параметры в развернутое приложение можно с помощью команды [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set). 

В следующей команде `<blob_storage_account>` является именем учетной записи хранения больших двоичных объектов, а `<blob_storage_key>` — связанным ключом. Замените `<web_app>` именем веб-приложения, созданного на предыдущем шаге.     

```azurecli-interactive 
az webapp config appsettings set --name <web_app> --resource-group myResourceGroup \
--settings AzureStorageConfig__AccountName=<blob_storage_account> \
AzureStorageConfig__ImageContainer=images  \
AzureStorageConfig__ThumbnailContainer=thumbnails \
AzureStorageConfig__AccountKey=<blob_storage_key>  
``` 

После развертывания и настройки веб-приложения можно проверить возможность отправки изображений в приложении.   

## <a name="upload-an-image"></a>Передача образа 

Чтобы проверить веб-приложение, перейдите по URL-адресу опубликованного приложения. URL-адрес приложения по умолчанию: `https://<web_app>.azurewebsites.net`. Выберите область **Upload photos** (Отправка фотографии), чтобы выбрать файл для отправки, или перетащите файлы в нее. При успешной отправке изображение исчезнет.

![Приложение ImageResizer](media/storage-upload-process-images/figure1.png)

В примере кода задача `UploadFiletoStorage` в файле `Storagehelper.cs` используется для отправки изображений в контейнер `images` в учетной записи хранения с помощью метода [UploadFromStreamAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstreamasync?view=azure-dotnet). В следующем примере кода содержится задача `UploadFiletoStorage`. 

```csharp
public static async Task<bool> UploadFileToStorage(Stream fileStream, string fileName, AzureStorageConfig _storageConfig)
{
    // Create storagecredentials object by reading the values from the configuration (appsettings.json)
    StorageCredentials storageCredentials = new StorageCredentials(_storageConfig.AccountName, _storageConfig.AccountKey);

    // Create cloudstorage account by passing the storagecredentials
    CloudStorageAccount storageAccount = new CloudStorageAccount(storageCredentials, true);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get reference to the blob container by passing the name by reading the value from the configuration (appsettings.json)
    CloudBlobContainer container = blobClient.GetContainerReference(_storageConfig.ImageContainer);

    // Get the reference to the block blob from the container
    CloudBlockBlob blockBlob = container.GetBlockBlobReference(fileName);

    // Upload the file
    await blockBlob.UploadFromStreamAsync(fileStream);

    return await Task.FromResult(true);
}
```

В предыдущей задаче используются следующие классы и методы:

|Класс  |Метод  |
|---------|---------|
|[StorageCredentials](/dotnet/api/microsoft.windowsazure.storage.auth.storagecredentials?view=azure-dotnet)     |         |
|[CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount?view=azure-dotnet)    |  [CreateCloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.createcloudblobclient?view=azure-dotnet#Microsoft_WindowsAzure_Storage_CloudStorageAccount_CreateCloudBlobClient)       |
|[CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient?view=azure-dotnet)     |[GetContainerReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient.getcontainerreference?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobClient_GetContainerReference_System_String_)         |
|[CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer?view=azure-dotnet)    | [GetBlockBlobReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.getblockblobreference?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobContainer_GetBlockBlobReference_System_String_)        |
|[CloudBlockBlob](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob?view=azure-dotnet)     | [UploadFromStreamAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstreamasync?view=azure-dotnet)        |

## <a name="verify-the-image-is-shown-in-the-storage-account"></a>Проверка отображения изображения в учетной записи хранения

Войдите на [портале Azure](https://portal.azure.com). В меню слева выберите **Учетные записи хранения**, а затем имя своей учетной записи хранения. В разделе **Обзор** выберите контейнер с **изображениями**.

Убедитесь, что изображение отображается в контейнере.

![Представление контейнера изображений](media/storage-upload-process-images/figure13.png)

## <a name="test-thumbnail-viewing"></a>Тестирование просмотра эскизов

Для проверки просмотра эскизов отправьте изображение в контейнер эскизов, чтобы убедиться, что приложение может считывать контейнер эскизов.

Войдите на [портале Azure](https://portal.azure.com). В меню слева выберите **Учетные записи хранения**, а затем имя своей учетной записи хранения. В разделе **Служба BLOB-объектов** щелкните **Контейнеры**, а затем выберите контейнер **эскизов**. Щелкните **Отправить**, чтобы открыть область **Передать BLOB-объект**.

С помощью средства выбора файлов выберите файл и щелкните **Отправить**.

Вернитесь к своему приложению и убедитесь, что изображение, отправленное в контейнер **эскизов**, отображается.

![Представление контейнера изображений](media/storage-upload-process-images/figure2.png)

В контейнере **эскизов** на портале Azure выберите отправленное изображение и щелкните **Удалить**, чтобы удалить его. Во второй части серии руководств будет выполняться автоматизация создания эскизов изображений, поэтому тестирование изображений не требуется.

CDN можно включить для кэширования содержимого из учетной записи хранения Azure. Хотя в этом руководстве не описано, как включить CDN с учетной записью хранения Azure, можно ознакомиться со статьей [Интеграция учетной записи хранения Azure с Azure CDN](../../cdn/cdn-create-a-storage-account-with-cdn.md).

## <a name="next-steps"></a>Дополнительная информация

В первой части серии руководств описана настройка взаимодействия веб-приложения с хранилищами. Вы научились выполнять такие задачи, как:

> [!div class="checklist"]
> * Создайте учетную запись хранения.
> * Создание контейнера и настройка разрешений.
> * Получение ключа доступа.
> * Настройка параметров приложения
> * Развертывание веб-приложения в Azure.
> * Взаимодействие с веб-приложением.

Перейдите ко второй части руководства, чтобы узнать об использовании службы "Сетка событий" для активации функции Azure, изменяющей размер изображения.

> [!div class="nextstepaction"]
> [Automate resizing uploaded images using Event Grid](../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) (Автоматическое изменение размера отправленных изображений с помощью службы "Сетка событий")
