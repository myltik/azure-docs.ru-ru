---
title: "Управление Azure Data Lake Analytics с помощью пакета SDK Azure для Java | Документация Майкрософт"
description: "Разработка приложений с использованием пакета SDK для Java для аналитики озера данных Azure"
services: data-lake-analytics
documentationcenter: 
author: matt1883
manager: jhubbard
editor: cgronlun
ms.assetid: 07830b36-2fe3-4809-a846-129cf67b6a9e
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/18/2017
ms.author: saveenr
ms.openlocfilehash: 8a0c1c7aab89f3bb62d0eb9f42e8ac65309d617e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="manage--azure-data-lake-analytics-using-java-sdk"></a>Управление Azure Data Lake Analytics с помощью пакета SDK для Java

В этом руководстве вы разработаете консольное приложение Java, которое выполняет общие действия в Azure Data Lake.

## <a name="prerequisites"></a>предварительным требованиям
* **Пакет средств разработки для Java (JDK) 8** (с использованием Java версии 1.8).
* **IntelliJ** или другая подходящая среда разработки Java. В инструкциях в этом документе используется IntelliJ.
* Создайте приложение Azure Active Directory (AAD) и получите его **идентификатор клиента**, **код клиента** и **ключ**. Дополнительные сведения о приложениях AAD и указания о том, как получить идентификатор клиента, см. в статье [Создание приложения Active Directory и субъекта-службы с помощью портала](../azure-resource-manager/resource-group-create-service-principal-portal.md). Когда будут созданы приложение и ключ, URI ответа и ключ также будут доступны на портале.

## <a name="authenticating-using-azure-active-directory"></a>Проверка подлинности с помощью Azure Active Directory

Ниже приведен фрагмент кода для **неинтерактивной** аутентификации, в ходе которой приложение указывает собственные учетные данные.

## <a name="create-a-java-application"></a>Создание приложения Java
1. Откройте IntelliJ и создайте проект Java с помощью шаблона **приложения командной строки**.
2. Щелкните правой кнопкой мыши проект в левой части экрана и выберите пункт **Add Framework Support**(Добавить поддержку платформы). Выберите **Maven** и нажмите кнопку **ОК**.
3. Откройте только что созданный файл **pom.xml** и добавьте следующий фрагмент текста между тегами **\<version>** и **\<project>**:

```
<repositories>
    <repository>
        <id>adx-snapshots</id>
        <name>Azure ADX Snapshots</name>
        <url>http://adxsnapshots.azurewebsites.net/</url>
        <layout>default</layout>
        <snapshots>
            <enabled>true</enabled>
        </snapshots>
    </repository>
    <repository>
        <id>oss-snapshots</id>
        <name>Open Source Snapshots</name>
        <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
        <layout>default</layout>
        <snapshots>
            <enabled>true</enabled>
            <updatePolicy>always</updatePolicy>
        </snapshots>
    </repository>
</repositories>
<dependencies>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-client-authentication</artifactId>
        <version>1.0.0-20160513.000802-24</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-client-runtime</artifactId>
        <version>1.0.0-20160513.000812-28</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.rest</groupId>
        <artifactId>client-runtime</artifactId>
        <version>1.0.0-20160513.000825-29</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-mgmt-datalake-store</artifactId>
        <version>1.0.0-SNAPSHOT</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-mgmt-datalake-analytics</artifactId>
        <version>1.0.0-SNAPSHOT</version>
    </dependency>
</dependencies>
```

Последовательно выберите пункты **Файл > Параметры > Сборка > Выполнение > Развертывание**. Выберите **Средства сборки > Maven > Импорт**. Затем установите флажок **Import Maven projects automatically**(Импортировать проекты Maven автоматически).

Откройте файл `Main.java` и замените существующий блок кода следующим фрагментом кода.

```
package com.company;

import com.microsoft.azure.CloudException;
import com.microsoft.azure.credentials.ApplicationTokenCredentials;
import com.microsoft.azure.management.datalake.store.*;
import com.microsoft.azure.management.datalake.store.models.*;
import com.microsoft.azure.management.datalake.analytics.*;
import com.microsoft.azure.management.datalake.analytics.models.*;
import com.microsoft.rest.credentials.ServiceClientCredentials;
import java.io.*;
import java.nio.charset.Charset;
import java.nio.file.Files;
import java.nio.file.Paths;
import java.util.ArrayList;
import java.util.UUID;
import java.util.List;

public class Main {
    private static String _adlsAccountName;
    private static String _adlaAccountName;
    private static String _resourceGroupName;
    private static String _location;

    private static String _tenantId;
    private static String _subId;
    private static String _clientId;
    private static String _clientSecret;

    private static DataLakeStoreAccountManagementClient _adlsClient;
    private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;
    private static DataLakeAnalyticsAccountManagementClient _adlaClient;
    private static DataLakeAnalyticsJobManagementClient _adlaJobClient;
    private static DataLakeAnalyticsCatalogManagementClient _adlaCatalogClient;

    public static void main(String[] args) throws Exception {

        _adlsAccountName = "<DATA-LAKE-STORE-NAME>";
        _adlaAccountName = "<DATA-LAKE-ANALYTICS-NAME>";
        _resourceGroupName = "<RESOURCE-GROUP-NAME>";
        _location = "East US 2";

        _tenantId = "<TENANT-ID>";
        _subId =  "<SUBSCRIPTION-ID>";
        _clientId = "<CLIENT-ID>";

        _clientSecret = "<CLIENT-SECRET>"; 
        
        String localFolderPath = "C:\\local_path\\"; 
        
        // ----------------------------------------
        // Authenticate
        // ----------------------------------------
        ApplicationTokenCredentials creds = new ApplicationTokenCredentials(_clientId, _tenantId, _clientSecret, null);
        SetupClients(creds);

        // ----------------------------------------
        // List Data Lake Store and Analytics accounts that this app can access
        // ----------------------------------------
        System.out.println(String.format("All ADL Store accounts that this app can access in subscription %s:", _subId));
        List<DataLakeStoreAccount> adlsListResult = _adlsClient.getAccountOperations().list().getBody();
        for (DataLakeStoreAccount acct : adlsListResult) {
            System.out.println(acct.getName());
        }
        
        System.out.println(String.format("All ADL Analytics accounts that this app can access in subscription %s:", _subId));
        List<DataLakeAnalyticsAccount> adlaListResult = _adlaClient.getAccountOperations().list().getBody();
        for (DataLakeAnalyticsAccount acct : adlaListResult) {
            System.out.println(acct.getName());
        }
        WaitForNewline("Accounts displayed.", "Creating files.");

        // ----------------------------------------
        // Create a file in Data Lake Store: input1.csv
        // ----------------------------------------

        byte[] bytesContents = "123,abc".getBytes();
        _adlsFileSystemClient.getFileSystemOperations().create(_adlsAccountName, "/input1.csv", bytesContents, true);

        WaitForNewline("File created.", "Submitting a job.");

        // ----------------------------------------
        // Submit a job to Data Lake Analytics
        // ----------------------------------------

string script = "@input =  EXTRACT Data string FROM \"/input1.csv\" USING Extractors.Csv(); OUTPUT @input TO @\"/output1.csv\" USING Outputters.Csv();", "testJob";
        UUID jobId = SubmitJobByScript(script);
        WaitForNewline("Job submitted.", "Getting job status.");

        // ----------------------------------------
        // Wait for job completion and output job status
        // ----------------------------------------
        System.out.println(String.format("Job status: %s", GetJobStatus(jobId)));
        System.out.println("Waiting for job completion.");
        WaitForJob(jobId);
        System.out.println(String.format("Job status: %s", GetJobStatus(jobId)));
        WaitForNewline("Job completed.", "Downloading job output.");

        // ----------------------------------------
        // Download job output from Data Lake Store
        // ----------------------------------------
        DownloadFile("/output1.csv", localFolderPath + "output1.csv");
        WaitForNewline("Job output downloaded.", "Deleting file.");

    }
}
```

Укажите значения для параметров, вызываемых во фрагменте кода:
* `localFolderPath`
* `_adlaAccountName`
* `_adlsAccountName`
* `_resourceGroupName`

Замените заполнители для:
* `CLIENT-ID`,
* `CLIENT-SECRET`,
* `TENANT-ID`
* `SUBSCRIPTION-ID`

## <a name="helper-functions"></a>Вспомогательные функции

### <a name="setup-clients"></a>Настройка клиентов

```
public static void SetupClients(ServiceClientCredentials creds)
{
    _adlsClient = new DataLakeStoreAccountManagementClientImpl(creds);
    _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClientImpl(creds);
    _adlaClient = new DataLakeAnalyticsAccountManagementClientImpl(creds);
    _adlaJobClient = new DataLakeAnalyticsJobManagementClientImpl(creds);
    _adlaCatalogClient = new DataLakeAnalyticsCatalogManagementClientImpl(creds);
    _adlsClient.setSubscriptionId(_subId);
    _adlaClient.setSubscriptionId(_subId);
}
```


### <a name="wait-for-input"></a>Ожидание входных данных

```
public static void WaitForNewline(String reason, String nextAction)
{
    if (nextAction == null)
        nextAction = "";

    System.out.println(reason + "\r\nPress ENTER to continue...");
    try{System.in.read();}
    catch(Exception e){}

    if (!nextAction.isEmpty())
    {
        System.out.println(nextAction);
    }
}
```

### <a name="create-accounts"></a>Создание учетных записей

```
public static void CreateAccounts() throws InterruptedException, CloudException, IOException 
{
    // Create ADLS account
    DataLakeStoreAccount adlsParameters = new DataLakeStoreAccount();
    adlsParameters.setLocation(_location);

    _adlsClient.getAccountOperations().create(_resourceGroupName, _adlsAccountName, adlsParameters);

    // Create ADLA account
    DataLakeStoreAccountInfo adlsInfo = new DataLakeStoreAccountInfo();
    adlsInfo.setName(_adlsAccountName);

    DataLakeStoreAccountInfoProperties adlsInfoProperties = new DataLakeStoreAccountInfoProperties();
    adlsInfo.setProperties(adlsInfoProperties);

    List<DataLakeStoreAccountInfo> adlsInfoList = new ArrayList<DataLakeStoreAccountInfo>();
    adlsInfoList.add(adlsInfo);

    DataLakeAnalyticsAccountProperties adlaProperties = new DataLakeAnalyticsAccountProperties();
    adlaProperties.setDataLakeStoreAccounts(adlsInfoList);
    adlaProperties.setDefaultDataLakeStoreAccount(_adlsAccountName);

    DataLakeAnalyticsAccount adlaParameters = new DataLakeAnalyticsAccount();
    adlaParameters.setLocation(_location);
    adlaParameters.setName(_adlaAccountName);
    adlaParameters.setProperties(adlaProperties);

    _adlaClient.getAccountOperations().create(_resourceGroupName, _adlaAccountName, adlaParameters);
}
```

### <a name="create-a-file"></a>Создание файла

```
public static void CreateFile(String path, String contents, boolean force) throws IOException, CloudException 
{
    byte[] bytesContents = contents.getBytes();

    _adlsFileSystemClient.getFileSystemOperations().create(_adlsAccountName, path, bytesContents, force);
}
```

### <a name="delete-a-file"></a>Удаление файла

```
public static void DeleteFile(String filePath) throws IOException, CloudException 
{
    _adlsFileSystemClient.getFileSystemOperations().delete(filePath, _adlsAccountName);
}
```

### <a name="download-a-file"></a>Скачивание файла

```
public static void DownloadFile(String srcPath, String destPath) throws IOException, CloudException 
{
    InputStream stream = _adlsFileSystemClient.getFileSystemOperations().open(srcPath, _adlsAccountName).getBody();

    PrintWriter pWriter = new PrintWriter(destPath, Charset.defaultCharset().name());

    String fileContents = "";
    if (stream != null) {
        Writer writer = new StringWriter();

        char[] buffer = new char[1024];
        try {
            Reader reader = new BufferedReader(
                    new InputStreamReader(stream, "UTF-8"));
            int n;
            while ((n = reader.read(buffer)) != -1) {
                writer.write(buffer, 0, n);
            }
        } finally {
            stream.close();
        }
        fileContents =  writer.toString();
    }

    pWriter.println(fileContents);
    pWriter.close();
}
```

### <a name="submit-a-u-sql-job"></a>Отправка задания U-SQL

```
public static UUID SubmitJobByScript(String script, String jobName) throws IOException, CloudException 
{
    UUID jobId = java.util.UUID.randomUUID();
    USqlJobProperties properties = new USqlJobProperties();
    properties.setScript(script);
    JobInformation parameters = new JobInformation();
    parameters.setName(jobName);
    parameters.setJobId(jobId);
    parameters.setType(JobType.USQL);
    parameters.setProperties(properties);

    JobInformation jobInfo = _adlaJobClient.getJobOperations().create(_adlaAccountName, jobId, parameters).getBody();

    return jobId;
}

// Wait for job completion
public static JobResult WaitForJob(UUID jobId) throws IOException, CloudException 
{
    JobInformation jobInfo = _adlaJobClient.getJobOperations().get(_adlaAccountName, jobId).getBody();
    while (jobInfo.getState() != JobState.ENDED)
    {
        jobInfo = _adlaJobClient.getJobOperations().get(_adlaAccountName,jobId).getBody();
    }
    return jobInfo.getResult();
}
```

### <a name="retrieve-job-status"></a>Получение состояния задания

```
public static String GetJobStatus(UUID jobId) throws IOException, CloudException 
{
    JobInformation jobInfo = _adlaJobClient.getJobOperations().get(_adlaAccountName, jobId).getBody();
    return jobInfo.getState().toValue();
}
```

## <a name="next-steps"></a>Дополнительная информация

* Сведения о языке U-SQL см. в статье [Учебник. Приступая к работе с языком U-SQL для аналитики озера данных Azure](data-lake-analytics-u-sql-get-started.md) и в [справочнике по языку U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348).
* Задачи управления описываются в руководстве по [управлению Azure Data Lake Analytics с помощью портала Azure](data-lake-analytics-manage-use-portal.md).
* Общие сведения об Azure Data Lake Analytics см. в [этой статье](data-lake-analytics-overview.md).
