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
ms.date: 11/02/2017
ms.author: nitinme
ms.openlocfilehash: a5d446986f810993d65c7e73eb95eeb2283c39a3
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/18/2017
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
На [сайте GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted) доступен пример кода, который используется для создания файлов в хранилище, объединения и скачивания файлов, а также для удаления файлов из хранилища. В этом разделе статьи рассматриваются основные части этого кода.

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

      * `Microsoft.Azure.DataLake.Store` — в этом руководстве используется версия 1.0.0.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` — в этом руководстве используется версия 2.3.1.
    
    Закройте **диспетчер пакетов NuGet**.

6. Откройте файл **Program.cs**, удалите существующий код и включите следующие инструкции, чтобы добавить ссылки на пространства имен.

        using System;
        using System.IO;using System.Threading;
        using System.Linq;
        using System.Text;
        using System.Collections.Generic;
        using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates
                
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.DataLake.Store;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

7. Объявите переменные, как показано ниже, и укажите значения вместо заполнителей. Кроме того, убедитесь, что локальный путь и имя файла, которые вы указываете, имеются на компьютере.

        namespace SdkSample
        {
            class Program
            {
                private static string _adlsAccountName = "<DATA-LAKE-STORE-NAME>"; //Replace this value with the name of your existing Data Lake Store account.        
            }
        }

В остальных разделах статьи рассматривается использование доступных методов .NET при выполнении таких операций, как проверка подлинности, отправка файла и т. д.

## <a name="authentication"></a>Аутентификация

* Дополнительные сведения о проверке подлинности пользователей в приложении см. в статье [End-user authentication with Data Lake Store using .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md) (Аутентификация пользователей в Data Lake Store с использованием пакета SDK для .NET).
* Дополнительные сведения о проверке подлинности между службами в приложении см. в статье [Service-to-service authentication with Data Lake Store using .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md) (Аутентификация между службами в Data Lake Store с использованием пакета SDK для .NET).


## <a name="create-client-object"></a>Создание клиентского объекта
Следующий фрагмент кода создает клиентский объект файловой системы Data Lake Store, который используется для передачи запросов к службе.

    // Create client objects
    AdlsClient client = AdlsClient.CreateClient(_adlsAccountName, adlCreds);

## <a name="create-a-file-and-directory"></a>Создание файла и каталога
Добавьте в приложение указанный ниже фрагмент кода. Этот фрагмент кода добавляет файл, а также любой родительский каталог, которые еще не добавлены.

    // Create a file - automatically creates any parent directories that don't exist
    
    string fileName = "/Test/testFilename1.txt";
    using (var streamWriter = new StreamWriter(client.CreateFile(fileName, IfExists.Overwrite)))
    {
        streamWriter.WriteLine("This is test data to write");
        streamWriter.WriteLine("This is line 2");
    }

## <a name="append-to-a-file"></a>Добавление данных в файл
Следующий фрагмент кода добавляет данные в существующий файл в учетной записи Data Lake Store.

    // Append to existing file
    using (var streamWriter = new StreamWriter(client.GetAppendStream(fileName)))
    {
        streamWriter.WriteLine("This is the added line");
    }

## <a name="read-a-file"></a>Чтение файла
Следующий фрагмент кода считывает содержимое файла в Data Lake Store.

    //Read file contents
    using (var readStream = new StreamReader(client.GetReadStream(fileName)))
    {
        string line;
        while ((line = readStream.ReadLine()) != null)
        {
            Console.WriteLine(line);
        }
    }

## <a name="get-file-properties"></a>Получение свойств файла
Следующий фрагмент кода возвращает свойства, связанные с файлом или каталогом.

    // Get file properties
    var directoryEntry = client.GetDirectoryEntry(fileName);
    PrintDirectoryEntry(directoryEntry);

Определение метода `PrintDirectoryEntry` доступно в примере [на сайте GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted). 

## <a name="rename-a-file"></a>Переименование файла
Следующий фрагмент кода переименовывает существующий файл в учетной записи Data Lake Store.

    // Rename a file
    string destFilePath = "/Test/testRenameDest3.txt";
    client.Rename(fileName, destFilePath, true);

## <a name="enumerate-a-directory"></a>Перечисление каталогов
Следующий фрагмент кода перечисляет каталоги в учетной записи Data Lake Store.

    // Enumerate directory
    foreach (var entry in client.EnumerateDirectory("/Test"))
    {
        PrintDirectoryEntry(entry);
    }

Определение метода `PrintDirectoryEntry` доступно в примере [на сайте GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted).

## <a name="delete-directories-recursively"></a>Рекурсивное удаление каталогов
Следующий фрагмент кода удаляет каталог и все его подкаталоги рекурсивно.

    // Delete a directory and all it's subdirectories and files
    client.DeleteRecursive("/Test");

## <a name="samples"></a>Примеры
Ниже приведено несколько примеров использования пакета SDK для файловой системы Data Lake Store.
* [Базовый пример на сайте GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted)
* [Расширенный пример на сайте GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-samples)

## <a name="see-also"></a>См. также
* [Начало работы с Azure Data Lake Store с помощью пакета SDK для .NET](data-lake-store-get-started-net-sdk.md)
* [Data Lake Store .NET Reference (Справочник по пакету SDK .NET для Data Lake Store)](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>Дальнейшие действия
* [Защита данных в хранилище озера данных](data-lake-store-secure-data.md)
