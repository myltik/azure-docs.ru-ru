---
title: "Пакет SDK для Java. Операции файловой системы в Azure Data Lake Store | Документация Майкрософт"
description: "Использование пакета SDK для Java для Azure Data Lake Store для выполнения операций файловой системы в Data Lake Store, таких как создание папок и т. д."
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: d10e09db-5232-4e84-bb50-52efc2c21887
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/29/2017
ms.author: nitinme
ms.openlocfilehash: e8c7b788061b3eb18b3e6c282339a03d93ab8b1c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="filesystem-operations-on-data-lake-store-using-java-sdk"></a>Операции файловой системы в Data Lake Store с помощью пакета SDK для Java
> [!div class="op_single_selector"]
> * [ПАКЕТ SDK .NET](data-lake-store-data-operations-net-sdk.md)
> * [Пакет SDK для Java](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

Узнайте, как с помощью пакета Java SDK для Azure Data Lake Store выполнять базовые операции, такие как создание папок, отправка и скачивание файлов данных и т. д. Дополнительные сведения об Azure Data Lake Store см. в [этой статье](data-lake-store-overview.md).

Документацию по API пакета Java SDK для Azure Data Lake Store можно найти [здесь](https://azure.github.io/azure-data-lake-store-java/javadoc/).

## <a name="prerequisites"></a>Предварительные требования
* Комплект разработчика Java (JDK 7 или более поздней версии с использованием Java версии 1.7 или более поздней).
* Учетная запись Azure Data Lake Store. Следуйте инструкциям в разделе [Начало работы с Azure Data Lake Store с помощью портала Azure](data-lake-store-get-started-portal.md).
* [Maven](https://maven.apache.org/install.html). В этом руководстве это средство используется для создания зависимостей проекта. Хотя зависимости можно создать и без использования таких систем, как Maven или Gradle, они существенно упрощают управление ими.
* Интегрированная среда разработки, например [IntelliJ IDEA](https://www.jetbrains.com/idea/download/), [Eclipse](https://www.eclipse.org/downloads/) или аналогичная (необязательно).

## <a name="create-a-java-application"></a>Создание приложения Java
На [сайте GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/) доступен пример кода, который используется для создания файлов в хранилище, объединения и скачивания файлов, а также для удаления файлов из хранилища. В этом разделе статьи рассматриваются основные части этого кода.

1. Создайте проект Maven с использованием [архетипа mvn](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) с помощью командной строки или интегрированной среды разработки (IDE). Инструкции по созданию проекта Java с использованием IntelliJ см. [здесь](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). Инструкции по созданию проекта с использованием Eclipse см. [здесь](http://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm). 

2. Добавьте приведенные ниже зависимости в файл Maven **pom.xml**. Добавьте следующий фрагмент перед тегом **\</project >**:
   
        <dependencies>
          <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-data-lake-store-sdk</artifactId>
            <version>2.1.5</version>
          </dependency>
          <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-nop</artifactId>
            <version>1.7.21</version>
          </dependency>
        </dependencies>
   
    Первая зависимость предназначена для использования пакета SDK для Data Lake Store (`azure-data-lake-store-sdk`) из репозитория Maven. Вторая зависимость нужна, чтобы указать, какие платформы ведения журналов (`slf4j-nop`) будут использоваться для этого приложения. Пакет SDK для Data Lake Store использует библиотеку [SLF4J](http://www.slf4j.org/), которая позволяет выбрать любую из ряда популярных платформ ведения журналов, таких как Log4j, платформа для Java, Logback и др., или отключить ведение журнала. В этом примере мы отключим ведение журнала, так как используем привязку **slf4j-nop**. Сведения о других вариантах ведения журнала в приложении см. [здесь](http://www.slf4j.org/manual.html#projectDep).

3. Добавьте следующие инструкции импорта в приложение.

        import com.microsoft.azure.datalake.store.ADLException;
        import com.microsoft.azure.datalake.store.ADLStoreClient;
        import com.microsoft.azure.datalake.store.DirectoryEntry;
        import com.microsoft.azure.datalake.store.IfExists;
        import com.microsoft.azure.datalake.store.oauth2.AccessTokenProvider;
        import com.microsoft.azure.datalake.store.oauth2.ClientCredsTokenProvider;

        import java.io.*;
        import java.util.Arrays;
        import java.util.List;

## <a name="authentication"></a>Аутентификация

* Дополнительные сведения о проверке подлинности пользователей в приложении см. в статье [Аутентификация пользователей в Data Lake Store с помощью Java](data-lake-store-end-user-authenticate-java-sdk.md).
* Дополнительные сведения о проверке подлинности между службами в приложении см. в статье [Service-to-service authentication with Data Lake Store using Java](data-lake-store-service-to-service-authenticate-java.md) (Аутентификация между службами в Data Lake Store с использованием Java).

## <a name="create-an-azure-data-lake-store-client"></a>Создание клиента Azure Data Lake Store
Создавая объект [ADLStoreClient](https://azure.github.io/azure-data-lake-store-java/javadoc/), укажите имя учетной записи Data Lake Store и поставщик маркера, который был создан при проверке подлинности в Data Lake Store (см. раздел [Проверка подлинности](#authentication)). Для учетной записи Data Lake Store необходимо указать полное доменное имя. Например, замените часть **FILL-IN-HERE** примерно таким значением: **mydatalakestore.azuredatalakestore.net**.

    private static String accountFQDN = "FILL-IN-HERE";  // full account FQDN, not just the account name
    ADLStoreClient client = ADLStoreClient.createClient(accountFQDN, provider);

Фрагменты кода в следующих разделах содержат примеры некоторых распространенных операций с файловой системой. Другие операции можно найти в полной [документации по API пакета Java SDK для Data Lake Store](https://azure.github.io/azure-data-lake-store-java/javadoc/) в разделе об объекте **ADLStoreClient**.

## <a name="create-a-directory"></a>Создайте каталог

Следующий фрагмент создает структуру каталогов в корне указанной учетной записи Data Lake Store.

    // create directory
    client.createDirectory("/a/b/w");
    System.out.println("Directory created.");

## <a name="create-a-file"></a>Создание файла

Следующий фрагмент кода создает файл (c.txt) в структуре каталогов и записывает в него некоторые данные.

    // create file and write some content
    String filename = "/a/b/c.txt";
    OutputStream stream = client.createFile(filename, IfExists.OVERWRITE  );
    PrintStream out = new PrintStream(stream);
    for (int i = 1; i <= 10; i++) {
        out.println("This is line #" + i);
        out.format("This is the same line (%d), but using formatted output. %n", i);
    }
    out.close();
    System.out.println("File created.");

Можно также создать файл (d.txt) с помощью массивов байтов.

    // create file using byte arrays
    stream = client.createFile("/a/b/d.txt", IfExists.OVERWRITE);
    byte[] buf = getSampleContent();
    stream.write(buf);
    stream.close();
    System.out.println("File created using byte array.");

Определение функции `getSampleContent`, используемой в предыдущем фрагменте кода, доступно как часть примера [на GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/). 

## <a name="append-to-a-file"></a>Добавление данных в файл

Следующий фрагмент кода добавляет содержимое в имеющийся файл.

    // append to file
    stream = client.getAppendStream(filename);
    stream.write(getSampleContent());
    stream.close();
    System.out.println("File appended.");

Определение функции `getSampleContent`, используемой в предыдущем фрагменте кода, доступно как часть примера [на GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/).

## <a name="read-a-file"></a>Чтение файла

Следующий фрагмент кода считывает содержимое из файла в учетной записи Data Lake Store.

    // Read File
    InputStream in = client.getReadStream(filename);
    BufferedReader reader = new BufferedReader(new InputStreamReader(in));
    String line;
    while ( (line = reader.readLine()) != null) {
        System.out.println(line);
    }
    reader.close();
    System.out.println();
    System.out.println("File contents read.");

## <a name="concatenate-files"></a>Сцепление файлов

Следующий фрагмент кода сцепляет два файла в учетной записи Data Lake Store. При успешном сцеплении объединенный файл заменяет два имеющихся файла.

    // concatenate the two files into one
    List<String> fileList = Arrays.asList("/a/b/c.txt", "/a/b/d.txt");
    client.concatenateFiles("/a/b/f.txt", fileList);
    System.out.println("Two files concatenated into a new file.");

## <a name="rename-a-file"></a>Переименование файла

Следующий фрагмент кода переименовывает файл в учетной записи Data Lake Store.

    //rename the file
    client.rename("/a/b/f.txt", "/a/b/g.txt");
    System.out.println("New file renamed.");

## <a name="get-metadata-for-a-file"></a>Получение метаданных для файла

Следующий фрагмент кода извлекает метаданные для файла из учетной записи Data Lake Store.

    // get file metadata
    DirectoryEntry ent = client.getDirectoryEntry(filename);
    printDirectoryInfo(ent);
    System.out.println("File metadata retrieved.");

## <a name="set-permissions-on-a-file"></a>Установка разрешений для файла

Следующий фрагмент кода задает разрешения для файла, созданного в предыдущем разделе.

    // set file permission
    client.setPermission(filename, "744");
    System.out.println("File permission set.");

## <a name="list-directory-contents"></a>Вывод содержимого каталогов

Следующий фрагмент кода рекурсивно отображает содержимое каталога.

    // list directory contents
    List<DirectoryEntry> list = client.enumerateDirectory("/a/b", 2000);
    System.out.println("Directory listing for directory /a/b:");
    for (DirectoryEntry entry : list) {
        printDirectoryInfo(entry);
    }
    System.out.println("Directory contents listed.");

Определение функции `printDirectoryInfo`, используемой в предыдущем фрагменте кода, доступно как часть примера [на GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/).

## <a name="delete-files-and-folders"></a>Удаление файлов и папок

Следующий фрагмент кода рекурсивно удаляет указанные файлы и папки из учетной записи хранения Data Lake Store.

    // delete directory along with all the subdirectories and files in it
    client.deleteRecursive("/a");
    System.out.println("All files and folders deleted recursively");
    promptEnterKey();

## <a name="build-and-run-the-application"></a>Создание и запуск приложения
1. Для запуска из среды IDE найдите и нажмите кнопку **запуска**. Для запуска из Maven выполните команду [exec:exec](http://www.mojohaus.org/exec-maven-plugin/exec-mojo.html).
2. Чтобы получить автономный JAR-файл, который можно запустить из командной строки, создайте такой файл со всеми зависимостями с помощью [подключаемого модуля сборки Maven](http://maven.apache.org/plugins/maven-assembly-plugin/usage.html). [Файл pom.xml в примере исходного кода на сайте GitHub](https://github.com/Azure-Samples/data-lake-store-java-upload-download-get-started/blob/master/pom.xml).

## <a name="next-steps"></a>Дальнейшие действия
* [Документация по пакету SDK для Java](https://azure.github.io/azure-data-lake-store-java/javadoc/)
* [Защита данных в хранилище озера данных](data-lake-store-secure-data.md)


