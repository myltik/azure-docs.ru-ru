---
title: "Пакет SDK для .NET. Операции файловой системы в Azure Data Lake Store | Документация Майкрософт"
description: "Использование пакета SDK для .NET для Azure Data Lake Store для выполнения операций файловой системы в Data Lake Store, таких как создание папок и т. д."
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/28/2017
ms.author: nitinme
ms.openlocfilehash: f525bd81ebbc27a4730bdced5c8778b6f2ac69b0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="filesystem-operations-on-azure-data-lake-store-using-net-sdk"></a>Операции файловой системы в Azure Data Lake Store с использованием SDK для .NET
> [!div class="op_single_selector"]
> * [ПАКЕТ SDK .NET](data-lake-store-data-operations-net-sdk.md)
> * [Пакет SDK для Java](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
>

В этой статье содержатся сведения о выполнении операций файловой системы в Data Lake Store с использованием SDK для .NET. К операциям файловой системы относятся операции создания папок в учетной записи Data Lake Store, передачи файлов, скачивания файлов и т. д.

Дополнительные сведения о том, как выполнять операции управления учетными записями в Data Lake Store с помощью Python, см. в статье [Начало работы с Azure Data Lake Store с помощью пакета SDK .NET](data-lake-store-get-started-net-sdk.md).

## <a name="prerequisites"></a>Предварительные требования
* **Visual Studio 2013, 2015 или 2017**. В инструкциях ниже используется Visual Studio 2017.

* **Подписка Azure**. Ознакомьтесь с [бесплатной пробной версией Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Учетная запись хранилища озера данных Azure**. Инструкции по созданию учетной записи см. в статье [Начало работы с Azure Data Lake Store с помощью портала Azure](data-lake-store-get-started-portal.md).

## <a name="create-a-net-application"></a>Создание приложения .NET
1. Откройте Visual Studio и создайте консольное приложение.
2. В меню **Файл** выберите команду **Создать**, а затем — **Проект**.
3. В окне **Новый проект**введите или выберите следующие значения.

   | Свойство | Значение |
   | --- | --- |
   | Категория |Templates/Visual C#/Windows |
   | Шаблон |Консольное приложение |
   | Имя |CreateADLApplication |
4. Нажмите кнопку **ОК** , чтобы создать проект.
5. Добавьте пакеты NuGet в проект.

   1. В обозревателе решений щелкните правой кнопкой мыши имя проекта и выберите пункт **Управление пакетами NuGet**.
   2. На вкладке **Диспетчер пакетов NuGet** в поле **Источник пакета** выберите **nuget.org** и установите флажок **Включить предварительные выпуски**.
   3. Найдите и установите следующие пакеты NuGet:

      * `Microsoft.Azure.Management.DataLake.Store`. В этом руководстве используется предварительная версия 2.1.3.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication`. В этом руководстве используется версия 2.2.12.

        ![Добавление источника NuGet](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "Создание учетной записи Azure Data Lake")
   4. Закройте **диспетчер пакетов NuGet**.
6. Откройте файл **Program.cs**, удалите существующий код и включите следующие инструкции, чтобы добавить ссылки на пространства имен.

        using System;
        using System.IO;
        using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates
        using System.Threading;

        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.Store.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Rest.Azure.Authentication;

7. Объявите переменные, как показано ниже, и укажите значения вместо заполнителей. Кроме того, убедитесь, что локальный путь и имя файла, которые вы указываете, имеются на компьютере.

        namespace SdkSample
        {
            class Program
            {
                private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;

                private static string _adlsAccountName;
                private static string _resourceGroupName;
                private static string _location;

                private static async void Main(string[] args)
                {
                    _adlsAccountName = "<DATA-LAKE-STORE-NAME>"; // TODO: Replace this value with the name of your existing Data Lake Store account.
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; // TODO: Replace this value with the name of the resource group containing your Data Lake Store account.
                    _location = "East US 2";

                    string localFolderPath = @"C:\local_path\"; // TODO: Make sure this exists and can be overwritten.
                    string localFilePath = Path.Combine(localFolderPath, "file.txt"); // TODO: Make sure this exists and can be overwritten.
                    string remoteFolderPath = "/data_lake_path/";
                    string remoteFilePath = Path.Combine(remoteFolderPath, "file.txt");
                }
            }
        }

В остальных разделах статьи рассматривается использование доступных методов .NET при выполнении таких операций, как проверка подлинности, отправка файла и т. д.

## <a name="authentication"></a>Аутентификация

* Дополнительные сведения о проверке подлинности пользователей в приложении см. в статье [End-user authentication with Data Lake Store using .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md) (Аутентификация пользователей в Data Lake Store с использованием пакета SDK для .NET).
* Дополнительные сведения о проверке подлинности между службами в приложении см. в статье [Service-to-service authentication with Data Lake Store using .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md) (Аутентификация между службами в Data Lake Store с использованием пакета SDK для .NET).


## <a name="create-client-objects"></a>Создание клиентских объектов
С помощью следующего фрагмента можно создать учетную запись Data Lake Store и клиентские объекты файловой системы, которые используются для передачи запросов к службе.

    // Create client objects
    _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(creds);

## <a name="create-a-directory"></a>Создайте каталог
Добавьте в класс следующий метод. Во фрагменте кода представлен метод `CreateDirectory()`, с помощью которого можно создать каталог в рамках учетной записи Data Lake Store.

    // Create a directory
    public static void CreateDirectory(string path)
    {
            _adlsFileSystemClient.FileSystem.Mkdirs(_adlsAccountName, path);
    }

Добавьте следующий фрагмент кода в метод `Main()` для вызова метода `CreateDirectory()`.

    CreateDirectory(remoteFolderPath);
    Console.WriteLine("Created a directory in the Data Lake Store account. Press any key to continue ...");
    Console.ReadLine();

## <a name="upload-a-file"></a>Отправить файл.
Добавьте в класс следующий метод. Во фрагменте кода представлен метод `UploadFile()`, с помощью которого можно отправить файлы в учетную запись Data Lake Store. Пакет SDK поддерживает рекурсивную отправку и загрузку между расположением локального файла и расположением файла Data Lake Store.

    // Upload a file
    public static void UploadFile(string srcFilePath, string destFilePath, bool force = true)
    {
        _adlsFileSystemClient.FileSystem.UploadFile(_adlsAccountName, srcFilePath, destFilePath, overwrite:force);
    }

Добавьте следующий фрагмент кода в метод `Main()` для вызова метода `UploadFile()`.

    UploadFile(localFilePath, remoteFilePath, true);
    Console.WriteLine("Uploaded file in the directory. Press any key to continue...");
    Console.ReadLine();
    

## <a name="get-file-or-directory-info"></a>Получение сведений о файле или каталоге
В следующем фрагменте представлен метод `GetItemInfo()` , с помощью которого можно получить сведения о файле или каталоге в Data Lake Store.

    public static FileStatusProperties GetItemInfo(string path)
    {
        return _adlsFileSystemClient.FileSystem.GetFileStatus(_adlsAccountName, path).FileStatus;
    }

Добавьте следующий фрагмент кода в метод `Main()` для вызова метода `GetItemInfo()`.

    
    var fileProperties = GetItemInfo(remoteFilePath);
    Console.WriteLine("The owner of the file at the path is:", fileProperties.Owner);
    Console.WriteLine("Press any key to continue...");
    Console.ReadLine();

## <a name="list-file-or-directories"></a>Получение списка файлов или каталогов
В следующем фрагменте представлен метод `ListItems()` , с помощью которого можно получить список файлов и каталогов в рамках учетной записи Data Lake Store.

    // List files and directories
    public static List<FileStatusProperties> ListItems(string directoryPath)
    {
        return _adlsFileSystemClient.FileSystem.ListFileStatus(_adlsAccountName, directoryPath).FileStatuses.FileStatus.ToList();
    }

Добавьте следующий фрагмент кода в метод `Main()` для вызова метода `ListItems()`.

    var itemList = ListItems(remoteFolderPath);
    var fileMenuItems = itemList.Select(a => String.Format("{0,15} {1}", a.Type, a.PathSuffix));
    Console.WriteLine(String.Join("\r\n", fileMenuItems));
    Console.WriteLine("Files and directories listed. Press any key to continue ...");
    Console.ReadLine();

## <a name="concatenate-files"></a>Сцепление файлов
В следующем фрагменте представлен метод `ConcatenateFiles()` , используемый для сцепления файлов.

    // Concatenate files
    public static void ConcatenateFiles(string[] srcFilePaths, string destFilePath)
    {
        _adlsFileSystemClient.FileSystem.Concat(_adlsAccountName, destFilePath, srcFilePaths);
    }

Добавьте следующий фрагмент кода в метод `Main()` для вызова метода `ConcatenateFiles()`. В этом фрагменте кода предполагается, что вы отправили другой файл в учетную запись Data Lake Store и что путь к файлу указан в строке *anotherRemoteFilePath*.

    string[] stringOfFiles = new String[] {remoteFilePath, anotherRemoteFilePath};
    string destFilePath = Path.Combine(remoteFolderPath, "Concatfile.txt");
    ConcatenateFiles(stringOfFiles, destFilePath);
    Console.WriteLine("Files concatinated. Press any key to continue ...");
    Console.ReadLine();

## <a name="append-to-a-file"></a>Добавление данных в файл
В следующем фрагменте представлен метод `AppendToFile()` , с помощью которого можно добавить данные в файл, уже хранящийся в учетной записи Data Lake Store.

    // Append to file
    public static void AppendToFile(string path, string content)
    {
        using (var stream = new MemoryStream(Encoding.UTF8.GetBytes(content)))
        {
            _adlsFileSystemClient.FileSystem.AppendAsync(_adlsAccountName, path, stream);
        }
    }

Добавьте следующий фрагмент кода в метод `Main()` для вызова метода `AppendToFile()`.

    AppendToFile(remoteFilePath, "123");
    Console.WriteLine("Content appended. Press any key to continue ...");
    Console.ReadLine();

## <a name="download-a-file"></a>Скачивание файла
В следующем фрагменте представлен метод `DownloadFile()` , с помощью которого можно скачать файл из учетной записи Data Lake Store.

    // Download file
    public static void DownloadFile(string srcFilePath, string destFilePath)
    {
        _adlsFileSystemClient.FileSystem.DownloadFile(_adlsAccountName, srcFilePath, destFilePath, overwrite:true);
    }

Добавьте следующий фрагмент кода в метод `Main()` для вызова метода `DownloadFile()`.

    DownloadFile(destFilePath, localFilePath);
    Console.WriteLine("File downloaded. Press any key to continue ...");
    Console.ReadLine();

## <a name="see-also"></a>См. также
* [Начало работы с Azure Data Lake Store с помощью пакета SDK .NET](data-lake-store-get-started-net-sdk.md)
* [Data Lake Store .NET Reference (Справочник по пакету SDK .NET для Data Lake Store)](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>Дальнейшие действия
* [Защита данных в хранилище озера данных](data-lake-store-secure-data.md)
