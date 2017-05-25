---
title: "Начало работы с Azure Data Lake Analytics с помощью Azure CLI 2.0 | Документация Майкрософт"
description: "Узнайте, как использовать интерфейс командной строки Azure версии 2.0 для создания учетной записи Data Lake Analytics, создания задания Data Lake Analytics с помощью U-SQL и отправки этого задания. "
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/06/2017
ms.author: jgao
ms.translationtype: Human Translation
ms.sourcegitcommit: 95b8c100246815f72570d898b4a5555e6196a1a0
ms.openlocfilehash: 25278e621155e7c90b5bb870d08793d285f4edff
ms.contentlocale: ru-ru
ms.lasthandoff: 05/18/2017


---
# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-cli-20"></a>Руководство. Начало работы с Azure Data Lake Analytics с помощью Azure CLI 2.0
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Узнайте, как с помощью Azure CLI 2.0 создавать учетные записи Azure Data Lake Analytics, определять задания Data Lake Analytics в [U-SQL](data-lake-analytics-u-sql-get-started.md) и отправлять задания в учетные записи Data Lake Analytics. Дополнительные сведения о Data Lake Analytics см. в [обзоре Azure Data Lake Analytics](data-lake-analytics-overview.md).

В этом руководстве вам предстоит разработать задание, которое считывает файл с разделителями-табуляциями (TSV) и преобразует его в файл с разделителями-запятыми (CSV). Для навигации по руководству с помощью других поддерживаемых средств используйте раскрывающийся список в верхней части этого раздела.

## <a name="prerequisites"></a>Предварительные требования
Перед началом работы с этим руководством необходимо иметь следующее:

* **Подписка Azure**. Ознакомьтесь с [бесплатной пробной версией Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure CLI 2.0**. См. статью [Установка и настройка интерфейса командной строки Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="log-in-to-azure"></a>Вход в Azure

Войдите в свою подписку Azure, выполнив эту команду:

```azurecli
az login
```

Вам будет предложено перейти по URL-адресу и ввести код для аутентификации.  Следуйте инструкциям, чтобы ввести учетные данные.

Войдя в Azure, вы увидите список своих подписок.

Чтобы выбрать нужную подписку, выполните следующую команду:

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-data-lake-analytics-account"></a>Создание учетной записи аналитики озера данных
Для выполнения любых заданий требуется учетная запись Data Lake Analytics. Для создания учетной записи Data Lake Analytics необходимо указать следующие данные.

* **Группа ресурсов Azure**. Учетная запись Data Lake Analytics должна быть создана в группе ресурсов Azure. [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) позволяет работать с группами ресурсов в приложении. Вы можете развертывать, обновлять или удалять все ресурсы для приложения в рамках одной скоординированной операции.  
  
    Чтобы отобразить существующие группы ресурсов в своей подписке выполните следующую команду:
  
    ```azurecli
    az group list 
    ```

    Чтобы создать новую группу ресурсов:
  
    ```azurecli
    az group create --name "<Resource Group Name>" --location "<Azure Location>"
    ```

* **Имя учетной записи Data Lake Analytics**. Каждой учетной записи Data Lake Analytics присвоено имя.
* **Расположение.** Используйте один из центров обработки данных Azure, который поддерживает Data Lake Analytics.
* **Учетная запись Data Lake Store по умолчанию** — каждая учетная запись Data Lake Analytics содержит учетную запись Data Lake Store по умолчанию.
  
    Чтобы получить список существующих учетных записей Data Lake Store, выполните эту команду:

    ```azurecli
    az dls account list
    ```
  
    Чтобы создать новую учетную запись Data Lake Store, выполните эту команду:
  
    ```azurecli
    az dls account create --account "<Data Lake Store Account Name>" --resource-group "<Resource Group Name>"
    ```

Используйте следующий синтаксис, чтобы создать учетную запись Data Lake Analytics:

```azurecli
az dla account create --account "<Data Lake Analytics Account Name>" --resource-group "<Resource Group Name>" --location "<Azure location>" --default-data-lake-store "<Default Data Lake Store Account Name>"
```

Создав учетную запись, вы можете получить список учетных записей и просмотреть сведения о них. Для этого выполните следующие команды:

```azurecli
az dla account list
az dla account show --account "<Data Lake Analytics Account Name>"            
```

## <a name="upload-data-to-data-lake-store"></a>Передача данных в хранилище озера данных
В этом руководстве обрабатываются некоторые журналы поиска.  Журнал поиска может храниться в хранилище озера данных или в хранилище больших двоичных объектов Azure. 

На портале Azure реализован пользовательский интерфейс для копирования файлов с образцами данных, включая файл журнала поиска, в учетную запись Data Lake Store по умолчанию. О передаче данных в учетную запись Data Lake Store по умолчанию см. в разделе [Подготовка исходных данных](data-lake-analytics-get-started-portal.md#prepare-source-data).

Чтобы передать файлы, используя интерфейс командной строки версии 2.0, выполните следующие команды:

```azurecli
az dls fs upload --account "<Data Lake Store Account Name>" --source-path "<Source File Path>" --destination-path "<Destination File Path>"
az dls fs list --account "<Data Lake Store Account Name>" --path "<Path>"
```

Из аналитики озера данных также доступно хранилище больших двоичных объектов Azure.  Чтобы передать данные в хранилище BLOB-объектов Azure, см. статью [Использование интерфейса командной строки (CLI) Azure со службой хранилища Azure](../storage/storage-azure-cli.md).

## <a name="submit-data-lake-analytics-jobs"></a>Отправка заданий аналитики озера данных
Задания аналитики озера данных пишутся на языке U-SQL. Дополнительные сведения о языке U-SQL см. в статье о [начале работы с языком U-SQL](data-lake-analytics-u-sql-get-started.md) и в [справочнике по языку U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348).

**Создание скрипта задания аналитики озера данных**

Создайте текстовый файл со следующим скриптом U-SQL, а затем сохраните текстовый файл на своей рабочей станции.
  
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

Не меняйте эти два пути, если только исходный файл не был скопирован в другое место.  Аналитика озера данных создаст выходную папку, если ее не существует.

Проще использовать относительные пути для файлов, которые хранятся в учетных записях Data Lake Store по умолчанию. Также можно использовать абсолютные пути.  Например:

    adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv

Необходимо использовать абсолютные пути для доступа к файлам в связанных учетных записях хранения.  Для файлов, хранящихся в связанной учетной записи хранения Azure, используется следующий синтаксис:

    wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv
  
  > [!NOTE]
  > Контейнер больших двоичных объектов Azure с разрешениями на доступ к общедоступным большим двоичным объектам или общедоступным контейнерам в настоящее время не поддерживается.      
  > 
  > 

**Отправка заданий**

Чтобы отправить задание, используйте этот синтаксис:

```azurecli
az dla job submit --account "<Data Lake Analytics Account Name>" --job-name "<Job Name>" --script "<Script Path and Name>"
```

Например:

```azurecli
az dla job submit --account "myadlaaccount" --job-name "myadlajob" --script @"C:\DLA\myscript.txt"
```

**Отображение списка заданий и сведений о задании**

```azurecli
az dla job list --account "<Data Lake Analytics Account Name>"
az dla job show --account "<Data Lake Analytics Account Name>" --job-identity "<Job Id>"
```

**Отмена заданий**

```azurecli
az dla job cancel --account "<Data Lake Analytics Account Name>" --job-identity "<Job Id>"
```

##<a name="retrieve-job-results"></a>Получение результатов задания

По окончании задания используйте следующие команды, чтобы вывести и скачать выходные файлы:

```azurecli
az dls fs list --account "<Data Lake Store Account Name>" --source-path "/Output" --destination-path "<Destintion>"
az dls fs preview --account "<Data Lake Store Account Name>" --path "/Output/SearchLog-from-Data-Lake.csv" 
az dls fs preview --account "<Data Lake Store Account Name>" --path "/Output/SearchLog-from-Data-Lake.csv" --length 128 --offset 0
az dls fs downlod --account "<Data Lake Store Account Name>" --source-path "/Output/SearchLog-from-Data-Lake.csv" --destintion-path "<Destination Path and File Name>"
```

Например:

```azurecli
az dls fs downlod --account "myadlsaccount" --source-path "/Output/SearchLog-from-Data-Lake.csv" --destintion-path "C:\DLA\myfile.csv"
```

## <a name="next-steps"></a>Дальнейшие действия

* Для просмотра учебника с помощью других средств используйте вкладки-селекторы в верхней части страницы.
* Документацию по интерфейсу командной строки 2.0 для Data Lake Analytics см. [здесь](https://docs.microsoft.com/cli/azure/dla).
* Документацию по интерфейсу командной строки 2.0 для Data Lake Store см. [здесь](https://docs.microsoft.com/cli/azure/dls).
* Более сложный запрос можно посмотреть в статье [Анализ журналов веб-сайта с помощью аналитики озера данных Azure](data-lake-analytics-analyze-weblogs.md).
* Чтобы приступить к разработке приложений U-SQL, ознакомьтесь со статьей [Разработка скриптов U-SQL с помощью средств озера данных для Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* Для знакомства с U-SQL см. статью о [начале работы с языком U-SQL для Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
* Задачи управления описываются в руководстве по [управлению Azure Data Lake Analytics с помощью портала Azure](data-lake-analytics-manage-use-portal.md).
* Общие сведения об Azure Data Lake Analytics см. в [этой статье](data-lake-analytics-overview.md).


