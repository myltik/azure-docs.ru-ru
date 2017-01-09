---
title: "Разработка приложений с использованием пакета Java SDK для Data Lake Store | Документация Майкрософт"
description: "Разработка приложений с использованием пакета Java SDK для хранилища озера данных."
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
ms.date: 12/23/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: c157da7bf53e2d0762624e8e71e56e956db04a24
ms.openlocfilehash: a80da95328a6f3c47edf6e9be9e786437a8c316e


---
# <a name="get-started-with-azure-data-lake-store-using-java"></a>Начало работы с хранилищем озера данных Azure с помощью Java
> [!div class="op_single_selector"]
> * [Портал](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Пакет SDK для .NET](data-lake-store-get-started-net-sdk.md)
> * [Пакет SDK для Java](data-lake-store-get-started-java-sdk.md)
> * [ИНТЕРФЕЙС REST API](data-lake-store-get-started-rest-api.md)
> * [Интерфейс командной строки Azure](data-lake-store-get-started-cli.md)
> * [Node.js](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
> 

Узнайте, как с помощью пакета Java SDK для Azure Data Lake Store выполнять базовые операции, такие как создание папок, отправка и скачивание файлов данных и т. д. Дополнительные сведения об Azure Data Lake Store см. в [этой статье](data-lake-store-overview.md).

Документацию по API пакета Java SDK для Azure Data Lake Store можно найти [здесь](https://azure.github.io/azure-data-lake-store-java/javadoc/).

## <a name="prerequisites"></a>Предварительные требования
* Комплект разработчика Java (JDK 7 или более поздней версии с использованием Java версии 1.7 или более поздней).
* Учетная запись Azure Data Lake Store. Следуйте инструкциям в разделе [Приступая к работе с хранилищем озера данных Azure на портале Azure](data-lake-store-get-started-portal.md).
* [Maven](https://maven.apache.org/install.html). В этом руководстве это средство используется для создания зависимостей проекта. Хотя зависимости можно создать и без использования таких систем, как Maven или Gradle, они существенно упрощают управление ими.
* Интегрированная среда разработки, например [IntelliJ IDEA](https://www.jetbrains.com/idea/download/), [Eclipse](https://www.eclipse.org/downloads/) или аналогичная (необязательно).

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Как выполнить аутентификацию с помощью Azure Active Directory?
В этом руководстве мы используем секрет клиента приложения Azure AD, чтобы получить маркер Azure Active Directory (для проверки подлинности с взаимодействием между службами). Он используется для создания клиентского объекта Data Lake Store, позволяющего выполнять операции с файлами и каталогами. Для проверки подлинности в Azure Data Lake Store с помощью секрета клиента требуется выполнить следующие основные этапы.

1. Создать веб-приложение Azure AD.
2. Получить идентификатор клиента, секрет клиента и конечную точку маркера для веб-приложения Azure AD.
3. Настроить для веб-приложения Azure AD доступ к файлу или папке Data Lake Store, к которым вы хотите получать доступ из создаваемого приложения Java.

Инструкции по выполнению этих шагов см. в разделе о [создании приложения Active Directory](data-lake-store-authenticate-using-active-directory.md#create-an-active-directory-application).

Помимо получения маркера Azure Active Directory предоставляет и другие возможности. Вы можете выбрать один из нескольких методов проверки подлинности в соответствии с вашим сценарием, например для приложения, выполняемого в браузере, приложения, распространяемого в виде классического приложения, или серверного приложения, выполняемого в локальной среде или в виртуальной машине Azure. Доступны также различные типы учетных данных, например пароли, сертификаты, метод двухфакторной проверки подлинности и т. д. Кроме того, Azure Active Directory позволяет синхронизировать локальных пользователей Active Directory с облаком. Дополнительные сведения см. в статье [Сценарии аутентификации в Azure Active Directory](../active-directory/active-directory-authentication-scenarios.md). 

## <a name="create-a-java-application"></a>Создание приложения Java
На [сайте GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/) доступен пример кода, который используется для создания файлов в хранилище, объединения и скачивания файлов, а также для удаления файлов из хранилища. В этом разделе статьи рассматриваются основные части этого кода.

1. Создайте проект Maven с использованием [архетипа mvn](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) с помощью командной строки или интегрированной среды разработки (IDE). Инструкции по созданию проекта Java с использованием IntelliJ см. [здесь](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). Инструкции по созданию проекта с использованием Eclipse см. [здесь](http://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm). 
2. Добавьте приведенные ниже зависимости в файл Maven **pom.xml**. Добавьте следующий фрагмент текста между тегами **\</version>** и **\</project>**.
   
        <dependencies>
          <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-data-lake-store-sdk</artifactId>
            <version>2.1.1</version>
          </dependency>
          <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-nop</artifactId>
            <version>1.7.21</version>
          </dependency>
        </dependencies>
   
    Первая зависимость предназначена для использования пакета SDK для Data Lake Store (`azure-datalake-store`) из репозитория Maven. Вторая зависимость (`slf4j-nop`) нужна, чтобы указать, какие платформы ведения журналов будут использоваться для этого приложения. Пакет SDK для Data Lake Store использует библиотеку [SLF4J](http://www.slf4j.org/), которая позволяет выбрать любую из ряда популярных платформ ведения журналов, таких как Log4j, платформа для Java, Logback и др., или отключить ведение журнала. В этом примере мы отключим ведение журнала, так как используем привязку **slf4j-nop**. Сведения о других вариантах ведения журнала в приложении см. [здесь](http://www.slf4j.org/manual.html#projectDep).

### <a name="add-the-application-code"></a>Добавление кода приложения
Код состоит из трех основных частей.

1. Получение маркера Azure Active Directory.
2. Использование маркера для создания клиента Data Lake Store.
3. Использование клиента Data Lake Store для выполнения операций.

#### <a name="step-1-obtain-an-azure-active-directory-token"></a>Шаг 1. Получение маркера Azure Active Directory
Пакет SDK для Data Lake Store предоставляет удобные методы получения маркеров безопасности, необходимых для подключения к учетной записи Data Lake Store. Тем не менее пакет SDK не требует использования только этих методов. Можно использовать любые другие средства получения маркера, например [пакет SDK для Azure Active Directory](https://github.com/AzureAD/azure-activedirectory-library-for-java) или пользовательский код.

Чтобы получить маркер для веб-приложения Active Directory, созданного ранее, с помощью пакета SDK для Data Lake Store, укажите статические методы в классе `AzureADAuthenticator`. Замените часть **FILL-IN-HERE** фактическими значениями, соответствующими веб-приложению Azure Active Directory.

    private static String clientId = "FILL-IN-HERE";
    private static String authTokenEndpoint = "FILL-IN-HERE";
    private static String clientKey = "FILL-IN-HERE";

    AzureADToken token = AzureADAuthenticator.getTokenUsingClientCreds(authTokenEndpoint, clientId, clientKey);

#### <a name="step-2-create-an-azure-data-lake-store-client-adlstoreclient-object"></a>Шаг 2. Создание клиентского объекта Azure Data Lake Store (ADLStoreClient)
Создавая объект [ADLStoreClient](https://azure.github.io/azure-data-lake-store-java/javadoc/), укажите имя учетной записи Data Lake Store и маркер Azure Active Directory, созданный на предыдущем шаге. Обратите внимание, что для учетной записи Data Lake Store необходимо указать полное доменное имя. Например, замените часть **FILL-IN-HERE** примерно таким значением: **mydatalakestore.azuredatalakestore.net**.

    private static String accountFQDN = "FILL-IN-HERE";  // full account FQDN, not just the account name
    ADLStoreClient client = ADLStoreClient.createClient(accountFQDN, token);

### <a name="step-3-use-the-adlstoreclient-to-perform-file-and-directory-operations"></a>Шаг 3. Использование ADLStoreClient для выполнения операций с файлами и каталогами
Приведенный ниже код содержит фрагменты для выполнения некоторых распространенных операций. Другие операции можно найти в полной [документации по API пакета Java SDK для Data Lake Store](https://azure.github.io/azure-data-lake-store-java/javadoc/) в разделе об объекте **ADLStoreClient**.

Обратите внимание, что чтение файлов и запись в них осуществляется с помощью стандартных потоков Java. Это означает, что вы можете разместить все потоки Java поверх потоков Data Lake Store, чтобы использовать стандартные возможности Java (например, потоки печати для получения отформатированных выходных данных либо потоки сжатия или шифрования для получения дополнительных возможностей поверх потоков и т. д.).

    // set file permission
    client.setPermission(filename, "744");

    // append to file
    stream = client.getAppendStream(filename);
    stream.write(getSampleContent());
    stream.close();

    // Read File
    InputStream in = client.getReadStream(filename);
    byte[] b = new byte[64000];
    while (in.read(b) != -1) {
        System.out.write(b);
    }
    in.close();

    // concatenate the two files into one
    List<String> fileList = Arrays.asList("/a/b/c.txt", "/a/b/d.txt");
    client.concatenateFiles("/a/b/f.txt", fileList);

    //rename the file
    client.rename("/a/b/f.txt", "/a/b/g.txt");

    // list directory contents
    List<DirectoryEntry> list = client.enumerateDirectory("/a/b", 2000);
    System.out.println("Directory listing for directory /a/b:");
    for (DirectoryEntry entry : list) {
        printDirectoryInfo(entry);
    }

    // delete directory along with all the subdirectories and files in it
    client.deleteRecursive("/a");

#### <a name="step-4-build-and-run-the-application"></a>Шаг 4. Сборка и запуск приложения
1. Для запуска из среды IDE найдите и нажмите кнопку **запуска**. Для запуска из Maven выполните команду [exec:exec](http://www.mojohaus.org/exec-maven-plugin/exec-mojo.html).
2. Чтобы получить автономный JAR-файл, который можно запустить из командной строки, создайте такой файл со всеми зависимостями с помощью [подключаемого модуля сборки Maven](http://maven.apache.org/plugins/maven-assembly-plugin/usage.html). Код для выполнения этих действий содержится в файле pom.xml в [примере исходного кода на сайте GitHub](https://github.com/Azure-Samples/data-lake-store-java-upload-download-get-started/blob/master/pom.xml).

## <a name="next-steps"></a>Дальнейшие действия
* [Защита данных в хранилище озера данных](data-lake-store-secure-data.md)
* [Использование аналитики озера данных Azure с хранилищем озера данных](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Использование Azure HDInsight с хранилищем озера данных](data-lake-store-hdinsight-hadoop-use-portal.md)




<!--HONumber=Nov16_HO4-->


