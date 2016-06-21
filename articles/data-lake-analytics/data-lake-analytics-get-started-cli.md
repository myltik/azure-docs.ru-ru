<properties 
   pageTitle="Приступая к работе с аналитикой озера данных Azure с помощью интерфейса командной строки Azure | Microsoft Azure" 
   description="Узнайте, как использовать интерфейс командной строки Azure для создания учетной записи хранилища озера данных, создания задания аналитики озера данных с помощью U-SQL и отправки задания." 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# Учебник. Приступая к работе с аналитикой озера данных Azure с помощью интерфейса командной строки Azure (CLI)

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]


Узнайте, как с помощью Azure CLI создавать учетные записи аналитики озера данных Azure, определять задания аналитики озера данных в [U-SQL](data-lake-analytics-u-sql-get-started.md) и отправлять задания в учетные записи аналитики озера данных. Дополнительные сведения об аналитике озера данных см. в статье [Обзор аналитики озера данных Azure](data-lake-analytics-overview.md).

В этом учебнике вам предстоит разработать задание, которое считывает файл с разделителями-табуляциями (TSV) и преобразует его в файл с разделителями-запятыми (CSV). Для навигации по учебнику с помощью других поддерживаемых средств используйте вкладки в верхней части этого раздела.

[AZURE.INCLUDE [basic-process-include](../../includes/data-lake-analytics-basic-process.md)]

##Предварительные требования

Перед началом работы с этим учебником необходимо иметь следующее:

- **Подписка Azure.**. См. [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Интерфейс командной строки Azure**. См. статью [Установка и настройка интерфейса командной строки Azure](../xplat-cli-install.md).
	- Для выполнения этой демонстрации загрузите и установите **предварительный выпуск** [программ CLI Azure](https://github.com/MicrosoftBigData/AzureDataLake/releases).
- **Пройдите проверку подлинности** с помощью следующей команды:

		azure login
	Дополнительную информацию об аутентификации с помощью рабочей или учебной учетной записи см. в разделе [Подключение к подписке Azure через интерфейс командной строки Azure](../xplat-cli-connect.md).
- **Переключитесь в режим диспетчера ресурсов Azure** с помощью следующей команды:

		azure config mode arm
		
## Создание учетной записи аналитики озера данных

Для выполнения любых заданий требуется учетная запись аналитики озера данных. Для создания учетной записи аналитики озера данных необходимо указать следующее.

- **Группа ресурсов Azure**: необходимо создать учетную запись аналитики озера данных в группе «Ресурс Azure». [Диспетчер ресурсов Azure](../resource-group-overview.md) позволяет вам работать с ресурсами в приложении в виде группы. Вы можете развертывать, обновлять или удалять все ресурсы для приложения в рамках одной скоординированной операции.  

	Для перечисления групп ресурсов в своей подписке выполните следующие действия.
    
    	azure group list 
    
	Чтобы создать новую группу ресурсов:

		azure group create -n "<Resource Group Name>" -l "<Azure Location>"

- **Имя учетной записи аналитики озера данных**
- **Расположение**: один из центров обработки данных Azure, который поддерживает аналитику озера данных.
- **Учетная запись озера данных по умолчанию**: каждая учетная запись аналитики озера данных имеет учетную запись озера данных по умолчанию.

	Вывод существующей учетной записи озера данных:
	
		azure datalake store account list

	Создание новой учетной записи озера данных

		azure datalake store account create "<Data Lake Store Account Name>" "<Azure Location>" "<Resource Group Name>"

	> [AZURE.NOTE] Имя учетной записи озера данных должно содержать только буквы нижнего регистра и цифры.



**Создание учетной записи аналитики озера данных**

		azure datalake analytics account create "<Data Lake Analytics Account Name>" "<Azure Location>" "<Resource Group Name>" "<Default Data Lake Account Name>"

		azure datalake analytics account list
		azure datalake analytics account show "<Data Lake Analytics Account Name>"			

![Отображение учетной записи аналитики озера данных](./media/data-lake-analytics-get-started-cli/data-lake-analytics-show-account-cli.png)

> [AZURE.NOTE] Имя учетной записи аналитики озера данных должно содержать только буквы в нижнем регистре и цифры.


## Передача данных в хранилище озера данных

В этом учебнике обрабатываются некоторые журналы поиска. Журнал поиска может храниться в хранилище озера данных или в хранилище больших двоичных объектов Azure.

Портал Azure предоставляет пользовательский интерфейс для копирования файлов с образцами данных, включая файл журнала поиска, в учетную запись озера данных по умолчанию. О передаче данных в учетную запись хранилища озера данных по умолчанию см. в разделе [Подготовка исходных данных](data-lake-analytics-get-started-portal.md#prepare-source-data).

Чтобы передать файлы, используя интерфейс командной строки, выполните следующую команду:

  	azure datalake store filesystem import "<Data Lake Store Account Name>" "<Path>" "<Destination>"
  	azure datalake store filesystem list "<Data Lake Store Account Name>" "<Path>"

Из аналитики озера данных также доступно хранилище больших двоичных объектов Azure. Чтобы передать данные в хранилище больших двоичных объектов Azure, см. раздел [Использование интерфейса командной строки (CLI) Azure со службой хранилища Azure](../storage/storage-azure-cli.md).

## Отправка заданий аналитики озера данных

Задания аналитики озера данных пишутся на языке U-SQL. Подробнее о языке U-SQL см. в статье [Начало работы с языком U-SQL](data-lake-analytics-u-sql-get-started.md) и в [справочнике по языку U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348).

**Создание скрипта задания аналитики озера данных**

- Создайте текстовый файл со следующим скриптом U-SQL, а затем сохраните текстовый файл на своей рабочей станции.

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();
        
        OUTPUT @searchlog   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

	Этот сценарий U-SQL считывает файл исходных данных с помощью **Extractors.Tsv()**, а затем создает CSV-файл с помощью **Outputters.Csv()**.
    
    Не меняйте эти два пути, если только исходный файл не был скопирован в другое место. Аналитика озера данных создаст выходную папку, если ее не существует.
	
	Проще использовать относительные пути для файлов, которые хранятся в учетных записях озера данных по умолчанию. Также можно использовать абсолютные пути. Например:
    
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
        
    Необходимо использовать абсолютные пути для доступа к файлам в связанных учетных записях хранения. Для файлов, хранящихся в связанной учетной записи хранения Azure, используется следующий синтаксис:
    
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

    >[AZURE.NOTE] Контейнер больших двоичных объектов Azure с разрешениями на доступ к общедоступным большим двоичным объектам или общедоступным контейнерам в настоящее время не поддерживается.

	
**Отправка задания**


	azure datalake analytics job create  "<Data Lake Analytics Account Name>" "<Job Name>" "<Script>"
    
    
С помощью следующих команд можно вывести список заданий, получить сведения о задании и отменить задания.

  	azure datalake analytics job cancel "<Data Lake Analytics Account Name>" "<Job Id>"
  	azure datalake analytics job list "<Data Lake Analytics Account Name>"
	azure datalake analytics job show "<Data Lake Analytics Account Name>" "<Job Id>"

По окончании задания используйте следующие командлеты, чтобы включить файл в список, а затем загрузите файл.
	
    azure datalake store filesystem list "<Data Lake Store Account Name>" "/Output"
	azure datalake store filesystem export "<Data Lake Store Account Name>" "/Output/SearchLog-from-Data-Lake.csv" "<Destination>"
	azure datalake store filesystem read "<Data Lake Store Account Name>" "/Output/SearchLog-from-Data-Lake.csv" <Length> <Offset>

## См. также

- Для просмотра учебника с помощью других средств используйте вкладки-селекторы в верхней части страницы.
- Более сложный запрос можно посмотреть в статье [Анализ журналов веб-сайта с помощью аналитики озера данных Azure](data-lake-analytics-analyze-weblogs.md).
- Чтобы приступить к разработке приложений U-SQL, ознакомьтесь со статьей [Разработка скриптов U-SQL с помощью средств озера данных для Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Для знакомства с U-SQL см. статью [Начало работы с языком U-SQL для аналитики озера данных Azure](data-lake-analytics-u-sql-get-started.md).
- Задачи управления описываются в статье [Управление аналитикой озера данных Azure с помощью портала Azure](data-lake-analytics-manage-use-portal.md).
- Общие сведения об аналитике озера данных см. в статье [Обзор аналитики озера данных Azure](data-lake-analytics-overview.md).

<!---HONumber=AcomDC_0615_2016-->