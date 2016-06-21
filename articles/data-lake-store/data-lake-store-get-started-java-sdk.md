<properties
   pageTitle="Разработка приложений с использованием пакета Java SDK для хранилища озера данных | Azure"
   description="Разработка приложений с использованием пакета Java SDK для хранилища озера данных."
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/12/2016"
   ms.author="nitinme"/>

# Начало работы с хранилищем озера данных Azure с помощью Java

> [AZURE.SELECTOR]
- [Портал](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [Пакет SDK для .NET](data-lake-store-get-started-net-sdk.md)
- [Пакет SDK для Java](data-lake-store-get-started-java-sdk.md)
- [ИНТЕРФЕЙС REST API](data-lake-store-get-started-rest-api.md)
- [Интерфейс командной строки Azure](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)

Узнайте, как с помощью пакета Java SDK для хранилища озера данных Azure создать учетную запись озера данных Azure и выполнить такие базовые операции, как создание папок, передача и загрузка файлов данных, удаление учетной записи и т. д. Дополнительные сведения об озере данных см. в разделе [Хранилище озера данных Azure](data-lake-store-overview.md).

## Пакет SDK для Java хранилища озера данных Azure

Ниже приведены ссылки на загрузку пакета SDK для Java хранилища озера данных и справочные материалы по пакету SDK для Java. В этом руководстве не требуется загружать пакет SDK или выполнять инструкции из справочной документации. Эти ссылки приводятся только для ознакомления.

* Исходный код пакета SDK для Java для хранилища озера данных доступен на портале [GitHub](https://github.com/Azure/azure-sdk-for-java).
* Справочник по пакету SDK для Java для хранилища озера данных доступен по адресу [https://azure.github.io/azure-sdk-for-java/](https://azure.github.io/azure-sdk-for-java/).

## Предварительные требования

* Пакет средств разработки для Java (JDK) 8 (с использованием Java версии 1.8).
* IntelliJ или другая подходящая среда разработки Java. Это необязательный пункт, но мы рекомендуем его выполнить. В инструкциях ниже используется IntelliJ.
* **Подписка Azure.**. См. [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Включите свою подписку Azure** для общедоступной предварительной версии хранилища озера данных. См. [инструкции](data-lake-store-get-started-portal.md#signup).
* **Создайте приложение Azure Active Directory**. Есть два метода проверки подлинности с помощью Azure Active Directory — **интерактивный** и **неинтерактивный**. Для этих способов предусмотрены разные предварительные требования.
	* **Для интерактивной проверки подлинности** в Azure Active Directory необходимо создать **собственное клиентское приложение**. После создания приложения необходимо получить следующие связанные с ним значения.
		- Получите **идентификатор клиента** и **универсальный код ресурса (URI) перенаправления** для приложения.
		- Настройка делегированных разрешений

	* **Для неинтерактивной проверки подлинности** (используется в статье) в Azure Active Directory необходимо создать **веб-приложение**. После создания приложения необходимо получить следующие связанные с ним значения.
		- Получите **идентификатор клиента**, **секрет клиента** и **универсальный код ресурса (URI) перенаправления** для приложения.
		- Настройка делегированных разрешений
		- Назначьте роль приложению Azure Active Directory. Роль может быть задана на уровне области действия, согласно которой необходимо предоставить разрешение приложению Azure Active Directory. Например, можно назначить приложение на уровне подписки или на уровне группы ресурсов. 

	Инструкции по извлечению этих значений, настройке разрешений и назначению ролей см. в статье [Создание приложения Active Directory и субъекта-службы с помощью портала](../resource-group-create-service-principal-portal.md).

## Как выполнить аутентификацию с помощью Azure Active Directory?

Ниже приведен фрагмент кода для **неинтерактивной** проверки подлинности, в ходе которой приложение указывает собственные учетные данные.

Для работы с этим руководством вам потребуется предоставить приложению разрешение на создание ресурсов в Azure. Для целей этого руководства **настоятельно рекомендуем** предоставить приложению разрешения участника приложения в новой неиспользованной пустой группе ресурсов в подписке Azure.

## Создание приложения Java

1. Откройте IntelliJ и создайте новый проект Java с помощью шаблона **приложения командной строки**. Завершите работу мастера, чтобы создать проект.

2. В левой части экрана щелкните проект правой кнопкой мыши и выберите пункт **Add Framework Support** (Добавить поддержку платформы). Выберите **Maven** и нажмите кнопку **ОК**.

3. Откройте только что созданный файл **pom.xml** и добавьте следующий фрагмент текста между тегами **</version>** и **</project>**:

    >[AZURE.NOTE] Этот шаг выполняется временно, пока пакет SDK для хранилища озера данных Azure не станет доступным в Maven. Как только пакет SDK появится в Maven, эта статья будет обновлена. Все будущие обновления этого пакета SDK будут доступны в Maven.

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
    	</dependencies>


4. Последовательно выберите элементы **File** (Файл), **Settings** (Параметры), **Build, Execution, and Deployment** (Сборка, выполнение и развертывание). Последовательно разверните элементы **Build Tools** (Средства сборки), **Maven**, **Importing** (Импорт). Установите флажок **Import Maven projects automatically** (Импортировать проекты Maven автоматически). Нажмите кнопку **Apply** (Применить), а затем — **ОК**.

5. В левой области последовательно выберите элементы **src**, **main**, **java**, **<имя пакета>**, откройте файл **Main.java** и замените существующий блок кода следующим кодом. Кроме того, задайте значения параметров, вызываемых во фрагменте кода, например **localFolderPath**, **\_adlsAccountName**, **\_resourceGroupName**, и замените заполнители **CLIENT-ID**, **CLIENT-SECRET**, **TENANT-ID** и **SUBSCRIPTION-ID** соответствующими значениями.

    Этот код используется при создании учетной записи хранилища озера данных и файлов в хранилище, при объединении и скачивании файлов, а также при удалении учетной записи.

        package com.company;
        
        import com.microsoft.azure.CloudException;
        import com.microsoft.azure.credentials.ApplicationTokenCredentials;
        import com.microsoft.azure.management.datalake.store.*;
        import com.microsoft.azure.management.datalake.store.models.*;
        import com.microsoft.rest.credentials.ServiceClientCredentials;
        import java.io.*;
        import java.nio.charset.Charset;
        import java.util.ArrayList;
        import java.util.List;
        
        public class Main {
            private static String _adlsAccountName;
            private static String _resourceGroupName;
            private static String _location;
        
            private static String _tenantId;
            private static String _subId;
            private static String _clientId;
            private static String _clientSecret;
        
            private static DataLakeStoreAccountManagementClient _adlsClient;
            private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;
        
            public static void main(String[] args) throws Exception {
                _adlsAccountName = "<DATA-LAKE-STORE-NAME>";
                _resourceGroupName = "<RESOURCE-GROUP-NAME>";
                _location = "East US 2";
        
                _tenantId = "<TENANT-ID>";
                _subId =  "<SUBSCRIPTION-ID>";
                _clientId = "<CLIENT-ID>";
        
                _clientSecret = "<CLIENT-SECRET>"; // TODO: For production scenarios, we recommend that you replace this line with a more secure way of acquiring the application client secret, rather than hard-coding it in the source code.
        
                String localFolderPath = "C:\\local_path\"; // TODO: Change this to any unused, new, empty folder on your local machine.
        
                // Authenticate
                ApplicationTokenCredentials creds = new ApplicationTokenCredentials(_clientId, _tenantId, _clientSecret, null);
                SetupClients(creds);
        
                // Create Data Lake Store account
                WaitForNewline("Authenticated.", "Creating NEW account.");
                CreateAccount();
                WaitForNewline("Account created.", "Displaying account(s).");
        
                // List Data Lake Store accounts that this app can access
                System.out.println(String.format("All ADL Store accounts that this app can access in subscription %s:", _subId));
                List<DataLakeStoreAccount> adlsListResult = _adlsClient.getAccountOperations().list().getBody();
                for (DataLakeStoreAccount acct : adlsListResult) {
                    System.out.println(acct.getName());
                }
                WaitForNewline("Account(s) displayed.", "Creating files.");
        
                // Create two files in Data Lake Store: file1.csv and file2.csv
                CreateFile("/file1.csv", "123,abc", true);
                CreateFile("/file2.csv", "456,def", true);
                WaitForNewline("Files created.", "Concatenating files.");
        
                // Concatenate two files in Data Lake Store
                List<String> srcFilePaths = new ArrayList<String>();
                srcFilePaths.add("/file1.csv");
                srcFilePaths.add("/file2.csv");
                ConcatenateFiles(srcFilePaths, "/input.csv");
                WaitForNewline("Files concatenated.", "Downloading file.");
        
                // Download file from Data Lake Store
                DownloadFile("/input.csv", localFolderPath + "input.csv");
                WaitForNewline("File downloaded.", "Deleting file.");
        
                // Delete file from Data Lake Store
                DeleteFile("/input.csv");
                WaitForNewline("File deleted.", "Deleting account.");
        
                // Delete account
                DeleteAccount();
                WaitForNewline("Account deleted.", "DONE.");
            }
        
            //Set up clients
            public static void SetupClients(ServiceClientCredentials creds)
            {
                _adlsClient = new DataLakeStoreAccountManagementClientImpl(creds);
                _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClientImpl(creds);
        
                _adlsClient.setSubscriptionId(_subId);
            }
        
            // Helper function to show status and wait for user input
            public static void WaitForNewline(String reason, String nextAction)
            {
                if (nextAction == null)
                    nextAction = "";
                if (!nextAction.isEmpty())
                {
                    System.out.println(reason + "\r\nPress ENTER to continue...");
                    try{System.in.read();}
                    catch(Exception e){}
                    System.out.println(nextAction);
                }
                else
                {
                    System.out.println(reason + "\r\nPress ENTER to continue...");
                    try{System.in.read();}
                    catch(Exception e){}
                }
            }
        
            // Create Data Lake Store account
            public static void CreateAccount() throws InterruptedException, CloudException, IOException {
                DataLakeStoreAccount adlsParameters = new DataLakeStoreAccount();
                adlsParameters.setLocation(_location);
        
                _adlsClient.getAccountOperations().create(_resourceGroupName, _adlsAccountName, adlsParameters);
            }
        
            // Create file
            public static void CreateFile(String path) throws IOException, CloudException {
                _adlsFileSystemClient.getFileSystemOperations().create(_adlsAccountName, path);
            }
        
            // Create file with contents
            public static void CreateFile(String path, String contents, boolean force) throws IOException, CloudException {
                byte[] bytesContents = contents.getBytes();
        
                _adlsFileSystemClient.getFileSystemOperations().create(_adlsAccountName, path, bytesContents, force);
            }
        
            // Append to file
            public static void AppendToFile(String path, String contents) throws IOException, CloudException {
                byte[] bytesContents = contents.getBytes();
        
                _adlsFileSystemClient.getFileSystemOperations().append(_adlsAccountName, path, bytesContents);
            }
        
            // Concatenate files
            public static void ConcatenateFiles(List<String> srcFilePaths, String destFilePath) throws IOException, CloudException {
                _adlsFileSystemClient.getFileSystemOperations().concat(_adlsAccountName, destFilePath, srcFilePaths);
            }
        
            // Delete concatenated file
            public static void DeleteFile(String filePath) throws IOException, CloudException {
                _adlsFileSystemClient.getFileSystemOperations().delete(_adlsAccountName, filePath);
            }
        
            // Get file or directory info
            public static FileStatusProperties GetItemInfo(String path) throws IOException, CloudException {
                return _adlsFileSystemClient.getFileSystemOperations().getFileStatus(_adlsAccountName, path).getBody().getFileStatus();
            }
        
            // List files and directories
            public static List<FileStatusProperties> ListItems(String directoryPath) throws IOException, CloudException {
                return _adlsFileSystemClient.getFileSystemOperations().listFileStatus(_adlsAccountName, directoryPath).getBody().getFileStatuses().getFileStatus();
            }
        
            // Download file
            public static void DownloadFile(String srcPath, String destPath) throws IOException, CloudException {
                InputStream stream = _adlsFileSystemClient.getFileSystemOperations().open(_adlsAccountName, srcPath).getBody();
        
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
        
            // Delete account
            public static void DeleteAccount() throws InterruptedException, CloudException, IOException {
                _adlsClient.getAccountOperations().delete(_resourceGroupName, _adlsAccountName);
            }
        }


6. Запустите приложение. Следуйте инструкциям на экране для запуска и завершения приложения.

## Дальнейшие действия

- [Защита данных в хранилище озера данных](data-lake-store-secure-data.md)
- [Использование аналитики озера данных Azure с хранилищем озера данных](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Использование Azure HDInsight с хранилищем озера данных](data-lake-store-hdinsight-hadoop-use-portal.md)

<!---HONumber=AcomDC_0615_2016-->