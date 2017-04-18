---
title: "Разработка скриптов U-SQL с помощью средств Data Lake для Visual Studio | Документация Майкрософт"
description: "Сведения об установке средств озера данных для Visual Studio, разработке и тестировании скриптов U-SQL. "
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/06/2017
ms.author: edmaca, yanacai
translationtype: Human Translation
ms.sourcegitcommit: 0b53a5ab59779dc16825887b3c970927f1f30821
ms.openlocfilehash: c26ac89bd7ef494331ba309aacf87de03506ac4c
ms.lasthandoff: 04/07/2017


---
# <a name="tutorial-develop-u-sql-scripts-using-data-lake-tools-for-visual-studio"></a>Учебник. Разработка скриптов U-SQL с помощью средств озера данных для Visual Studio
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Написание и тестирование скриптов U-SQL с помощью Data Lake Tools для Visual Studio.

U-SQL — это высокомасштабируемый и расширяемый язык для подготовки, преобразования и анализа любых данных в озере данных и не только. Дополнительные сведения см. в [справке по U-SQL](http://go.microsoft.com/fwlink/p/?LinkId=691348).

## <a name="prerequisites"></a>Предварительные требования
* **Visual Studio 2017 (для рабочей нагрузки хранения и обработки данных), Visual Studio 2015 с обновлением 3, Visual Studio 2013 с обновлением 4 или Visual Studio 2012. Поддерживаются выпуски Enterprise (Ultimate/Premium), Professional и Community. Выпуск Express не поддерживается.**
* **Пакет SDK Microsoft Azure для .NET (версии 2.7.1 или более поздней версии)**.  Вы можете установить его с помощью [установщика веб-платформы](http://www.microsoft.com/web/downloads/platform.aspx).
* **[Средства Data Lake для Visual Studio](http://aka.ms/adltoolsvs)**.

    После установки средств озера данных для Visual Studio в обозревателе сервера в узле Azure появится узел Data Lake Analytics. (Чтобы открыть обозреватель сервера, нажмите клавиши CTRL+ALT+S.)

* **Учетная запись Data Lake Analytics и пример данных.** Средства Data Lake не поддерживают создание учетных записей Data Lake Analytics. Такую запись можно создать с помощью портала Azure, Azure PowerShell, пакета SDK .NET или интерфейса командной строки Azure.
Для удобства в разделе [Пример Appx-A PowerShell для подготовки руководства](data-lake-analytics-data-lake-tools-get-started.md#appx-a-powershell-sample-for-preparing-the-tutorial) вы можете найти пример скрипта PowerShell для создания службы Data Lake Analytics и загрузки файла источника данных.

    При необходимости ознакомьтесь со следующими двумя разделами в руководстве по [началу работы с Azure Data Lake Analytics с помощью портала Azure](data-lake-analytics-get-started-portal.md), чтобы создать учетную запись и загрузить данные вручную.

    1. [Создание учетной записи аналитики озера данных Azure](data-lake-analytics-get-started-portal.md#create-data-lake-analytics-account).
    2. [Передача файла SearchLog.tsv в учетную запись хранения озера данных по умолчанию](data-lake-analytics-get-started-portal.md#prepare-source-data).

## <a name="connect-to-azure"></a>Подключение к Azure
**Подключение к Data Lake Analytics**

1. Откройте Visual Studio.
2. В меню **Вид** выберите **Обозреватель серверов**, чтобы открыть окно обозревателя. Или нажмите **[CTRL] + [ALT] + S**.
3. Щелкните правой кнопкой мыши **Azure**, выберите "Подключиться к подписке Microsoft Azure" и следуйте инструкциям.
4. В **обозревателе серверов** разверните **Azure**, а затем — **Data Lake Analytics**. Будет выведен список учетных записей аналитики озера данных, если они есть. Создавать учетные записи аналитики озера данных из Visual Studio нельзя. Описание создания учетной записи см. в [руководстве по началу работы с Azure Data Lake Analytics с помощью портала Azure](data-lake-analytics-get-started-portal.md) или [руководстве по началу работы с Azure Data Lake Analytics с помощью Azure PowerShell](data-lake-analytics-get-started-powershell.md).

## <a name="upload-source-data-files"></a>Передача исходных файлов данных
Вы уже передали некоторые данные в разделе **Предварительные требования** ранее в этом учебнике.  

Чтобы использовать собственные данные, выполните следующие действия для передачи данных с помощью средств Data Lake.

**Передача файлов в зависимую учетную запись Azure Data Lake**

1. В **обозревателе серверов** разверните узлы **Azure**, **Data Lake Analytics**, учетную запись Data Lake Analytics и **Учетные записи хранения**. Появятся учетная запись хранения озера данных по умолчанию, связанные учетные записи хранения озера данных и связанные учетные записи хранения Azure. Учетная запись озера данных по умолчанию имеет метку «Учетная запись хранения по умолчанию».
2. Щелкните правой кнопкой мыши учетную запись хранения озера данных по умолчанию и выберите пункт меню **Обозреватель**.  Откроется панель средств озера данных для обозревателя Visual Studio.  Слева показано дерево, справа — содержимое.
3. Перейдите в папку, в которую нужно передать файлы.
4. Щелкните правой кнопкой мыши в пустом месте и выберите пункт меню **Передать**.

    ![Проект U-SQL в Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-upload-files.png)

**Передача файлов в связанную учетную запись хранилища BLOB-объектов Azure**

1. В **обозревателе серверов** разверните узлы **Azure**, **Data Lake Analytics**, учетную запись Data Lake Analytics и **Учетные записи хранения**. Появятся учетная запись хранения озера данных по умолчанию, связанные учетные записи хранения озера данных и связанные учетные записи хранения Azure.
2. Разверните учетную запись хранения Azure.
3. Щелкните правой кнопкой мыши контейнер, в который нужно передать файлы, и нажмите кнопку **Обозреватель**. Если у вас нет контейнера, сначала создайте его с помощью портала Azure, Azure PowerShell или других средств.
4. Перейдите в папку, в которую нужно передать файлы.
5. Щелкните правой кнопкой мыши в пустом месте и выберите пункт меню **Передать**.

## <a name="develop-u-sql-scripts"></a>Разработка скриптов U-SQL
Задания аналитики озера данных пишутся на языке U-SQL. Дополнительные сведения о языке U-SQL см. в статье о [начале работы с языком U-SQL](data-lake-analytics-u-sql-get-started.md) и в [справочнике по языку U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348).

**Создание и отправка задания Data Lake Analytics**

1. В меню **Файл** выберите команду **Создать**, а затем — **Проект**.
2. Выберите тип **Проект U-SQL** .

    ![новый проект U-SQL в Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)
3. Нажмите кнопку **ОК**. Visual Studio создает решение с файлом **Script.usql** .
4. Скопируйте следующий скрипт в файл **Script.usql**:

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

        @res =
            SELECT *
            FROM @searchlog;        

        OUTPUT @res   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

    Этот сценарий U-SQL считывает файл исходных данных с помощью **Extractors.Tsv()**, а затем создает CSV-файл с помощью **Outputters.Csv()**.

    Не меняйте эти два пути, если только исходный файл не был скопирован в другое место.  Аналитика озера данных создаст выходную папку, если ее не существует.

    Проще использовать относительные пути для файлов, которые хранятся в учетных записях озера данных по умолчанию. Также можно использовать абсолютные пути.  Например:

        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv

    Для доступа к файлам в связанных учетных записях хранения используйте абсолютные пути.  Для файлов, хранящихся в связанной учетной записи хранения Azure, используется следующий синтаксис:

        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

   > [!NOTE]
   > Контейнер больших двоичных объектов Azure с разрешениями на доступ к общедоступным большим двоичным объектам или общедоступным контейнерам в настоящее время не поддерживается.  
   >
   >

    Обратите внимание на следующие возможности.

   * **IntelliSense**

       Автоматическое заполнение имени и отображение членов для наборов строк, классов, баз данных, схем и пользовательских объектов (UDO).

       IntelliSense для сущностей каталога (базы данных, схемы, таблицы, пользовательские объекты и т. д.) связывается с вашей учетной записью вычислений. Узнать текущую активную учетную запись вычислений, базу данных и схему можно в верхней панели инструментов, менять их можно в раскрывающихся списках.
   * **Развертывание столбцов ***

       Щелкните справа от *. Под * вы увидите синюю линию подчеркивания. Наведите указатель мыши на синюю линию подчеркивания и нажмите кнопку со стрелкой вниз.
       ![Средства Data Lake для Visual Studio. Развертывание столбца *](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-expand-asterisk.png)

       Щелкните **Развернуть столбцы**, средство заменит * именами столбцов.
   * **Автоформат**

       В меню "Правка" -> "Дополнительно" пользователи могут менять отступы в скрипте в соответствии со структурой кода.

     * Форматировать документ (Ctrl + E, D): форматирует весь документ   
     * Форматировать выделенный фрагмент (Ctrl + K, Ctrl + F): форматирует выделенный фрагмент. Если фрагмент не выделен, будет отформатирована строка, в которой находится курсор.  

       Все правила форматирования можно настроить в меню Сервис -> Параметры -> Текстовый редактор - > SIP -> Форматирование.  
   * **Автоматический отступ**

       Средства озера данных для Visual Studio позволяют автоматически вставлять отступы для выражений при написании скриптов. По умолчанию эта функция отключена. Чтобы включить ее, нужно выбрать параметр «Включить автоматический отступ» в меню U-SQL -> Параметры и настройки -> Параметры.
   * **Переход к определению и поиск всех ссылок**

       Щелкните правой кнопкой мыши имя набора строк, параметра, столбца, пользовательского объекта и пр. Выберите команду «Перейти к определению» (F12), чтобы перейти к определению объекта. Команда «Найти все ссылки» (Shift + F12) покажет все ссылки.
   * **Вставка пути Azure**

       Вместо того чтобы запоминать путь к файлу Azure и вводить его вручную при написании сценариев, воспользуйтесь простым способом средств Data Lake для Visual Studio: щелкните правой кнопкой мыши в редакторе и выберите команду Insert Azure Path (Вставить путь Azure). Перейдите к файлу в диалоговом окне браузера больших двоичных объектов Azure. Нажмите кнопку **ОК**. Путь к файлу будет вставлен в код.
5. Укажите учетную запись аналитики озера данных, базу данных и схему. Вы можете выбрать вариант **(локально)**, чтобы запустить сценарий локально в целях тестирования. Дополнительные сведения см. в разделе [Локальный запуск U-SQL](#run-u-sql-locally).

    ![Отправка проекта U-SQL в Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job.png)

    Дополнительные сведения см. в разделе [Использование каталога U-SQL](data-lake-analytics-use-u-sql-catalog.md).
6. В **обозревателе решений** щелкните правой кнопкой мыши файл **Script.usql** и выберите команду **Создать сценарий**. Проверьте результат на панели вывода.
7. В **обозревателе решений** щелкните правой кнопкой мыши файл **Script.usql** и выберите команду **Отправить сценарий**. При необходимости можно нажать кнопку **Отправить** на панели Script.usql.  См. предыдущий снимок экрана.  Щелкните стрелку вниз рядом с кнопкой «Отправить», чтобы отправить с использованием расширенных параметров.
8. Укажите значение в поле **Имя задания**, проверьте **учетную запись аналитики** и нажмите кнопку **Отправить**. Результаты отправки и ссылка на задание появятся в окне результатов средств озера данных для Visual Studio после завершения отправки.

    ![Отправка проекта U-SQL в Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job-advanced.png)
9. Чтобы увидеть текущее состояние задания, нажмите кнопку «Обновить». Если задание завершилось успешно, будут показаны **Граф задания**, **Операции с метаданными**, **Журнал состояний** и **Диагностика**.

    ![График выполнения задания аналитики озера данных U-SQL в Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-performance-graph.png)

   * Обзор задания. Показывает сводку по текущему заданию, например состояние, ход выполнения, время выполнения, имя среды выполнения, отправителя и т. д.   
   * Сведения о задании. Подробные сведения по этому заданию, включая скрипт, ресурсы, представление выполнения вершин.
   * График задания. Предлагается четыре графика для визуализации сведений о задании, включая ход выполнения, считанные данные, записанные данные, время выполнения, среднее время выполнения на каждом узле, пропускную способность ввода и пропускную способность вывода.
   * Операции с метаданными. Показываются все операции с метаданными.
   * Журнал состояний.
   * Диагностика. Средства озера данных для Visual Studio ведут автоматическую диагностику выполнения задания. При возникновении ошибок или проблем с производительностью в ваших заданиях будут выводиться предупреждения. Дополнительные сведения см. в разделе «Диагностика заданий» (link TBD).

**Проверка состояния задания**

1. В обозревателе серверов разверните узлы **Azure**, **Data Lake Analytics** и имя учетной записи Data Lake Analytics.
2. Дважды щелкните **Задания** для вывода списка заданий.
3. Выберите задание, чтобы показать его состояние.

**Просмотр выходных данных задания**

1. В **обозревателе сервера** разверните узлы **Azure** и **Data Lake Analytics**, разверните учетную запись Data Lake Analytics и **учетные записи хранения**, а затем щелкните правой кнопкой мыши учетную запись хранения Data Lake по умолчанию и выберите **Обозреватель**.
2. Дважды щелкните **выходные данные** , чтобы открыть папку.
3. Дважды щелкните файл **SearchLog-From-adltools.csv**.

### <a name="job-playback"></a>Воспроизведение задания
Воспроизведение задания позволяет отслеживать ход выполнения задания и визуально обнаруживать проблемы производительности и узкие места. Эта функция может использоваться до завершения задания (т. е. во время активного выполнения задания), а также после завершения выполнения. Воспроизведение во время выполнения задания позволяет пользователю воспроизвести ход выполнения до текущего времени.

**Просмотр хода выполнения задания**  

1. Нажмите кнопку **Загрузить профиль** в правом верхнем углу. См. предыдущий снимок экрана.
2. Нажмите кнопку «Воспроизвести» в левом нижнем углу для просмотра хода выполнения задания.
3. Вы можете остановить воспроизведение с помощью кнопки **Пауза** , кроме того, вы можете перетащить ползунок хода выполнения в нужное положение.

### <a name="heat-map"></a>Тепловая карта
Средства озера данных для Visual Studio позволяют пользователю выбирать подходящие наложенные цвета в представлении задания для выделения хода выполнения, ввода-вывода данных, времени выполнения, пропускной способности ввода-вывода на каждом этапе. Благодаря этому можно наглядно и напрямую определять потенциальные проблемы и распределение свойств задания. Отображаемый источник данных можно выбрать в раскрывающемся списке.  

## <a name="run-u-sql-locally"></a>Локальный запуск U-SQL

Вы можете использовать средства Azure Data Lake для Visual Studio и пакет SDK U-SQL для Azure Data Lake, чтобы запускать задания U-SQL на рабочей станции так же, как в службе Azure Data Lake. Оба этих компонента для локального выполнения помогут вам быстрее выполнять тестирование и отладку заданий U-SQL. 

* [Тестирование и отладка заданий U-SQL с помощью локального выполнения и пакета SDK U-SQL для Azure Data Lake](data-lake-analytics-data-lake-tools-local-run.md)


## <a name="see-also"></a>См. также
Для начала работы с аналитикой озера данных с использованием различных средств см. следующие статьи.

* [Начало работы с аналитикой озера данных с помощью портала Azure](data-lake-analytics-get-started-portal.md)
* [Начало работы с аналитикой озера данных с помощью Azure PowerShell](data-lake-analytics-get-started-powershell.md)
* [Начало работы с аналитикой озера данных с помощью пакета SDK .NET.](data-lake-analytics-get-started-net-sdk.md)
* [Отладка заданий U-SQL](data-lake-analytics-debug-u-sql-jobs.md)

Подробные сведения об инструментах Data Lake Tools для Visual Studio Code см. в статье [Use the Azure Data Lake Tools for Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md) (Использование Azure Data Lake Tools для Visual Studio Code).

Другие ресурсы по разработке

* [Анализ журналов веб-сайтов с помощью аналитики озера данных](data-lake-analytics-analyze-weblogs.md)
* [Разработка скриптов U-SQL с помощью средств озера данных для Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Начало работы с языком U-SQL в службе аналитики озера данных Azure.](data-lake-analytics-u-sql-get-started.md)
* [Разработка пользовательских операторов U-SQL для заданий аналитики озера данных](data-lake-analytics-u-sql-develop-user-defined-operators.md)

## <a name="appx-a-powershell-sample-for-preparing-the-tutorial"></a>Пример Appx-A PowerShell для подготовки руководства
Следующий сценарий PowerShell подготавливает учетную запись Azure Data Lake Analytics и источник данных, поэтому вы можете сразу перейти к статье [Учебник. Разработка скриптов U-SQL с помощью средств озера данных для Visual Studio](data-lake-analytics-data-lake-tools-get-started.md#develop-u-sql-scripts).

    #region - used for creating Azure service names
    $nameToken = "<Enter an alias>"
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")
    #endregion

    #region - service names
    $resourceGroupName = $namePrefix + "rg"
    $dataLakeStoreName = $namePrefix + "adas"
    $dataLakeAnalyticsName = $namePrefix + "adla"
    $location = "East US 2"
    #endregion


    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"

    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #endregion

    #region - Create an Azure Data Lake Analytics service account
    Write-Host "Create a resource group ..." -ForegroundColor Green
    New-AzureRmResourceGroup `
        -Name  $resourceGroupName `
        -Location $location

    Write-Host "Create a Data Lake account ..."  -ForegroundColor Green
    New-AzureRmDataLakeStoreAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $dataLakeStoreName `
        -Location $location

    Write-Host "Create a Data Lake Analytics account ..."  -ForegroundColor Green
    New-AzureRmDataLakeAnalyticsAccount `
        -Name $dataLakeAnalyticsName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -DefaultDataLake $dataLakeStoreName

    Write-Host "The newly created Data Lake Analytics account ..."  -ForegroundColor Green
    Get-AzureRmDataLakeAnalyticsAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $dataLakeAnalyticsName  
    #endregion

    #region - prepare the source data
    Write-Host "Import the source data ..."  -ForegroundColor Green
    $localFolder = "C:\Tutorials\Downloads\" # A temp location for the file.
    $storageAccount = "adltutorials"  # Don't modify this value.
    $container = "adls-sample-data"  #Don't modify this value.

    # Create the temp location  
    New-Item -Path $localFolder -ItemType Directory -Force

    # Download the sample file from Azure Blob storage
    $context = New-AzureStorageContext -StorageAccountName $storageAccount -Anonymous
    $blobs = Azure\Get-AzureStorageBlob -Container $container -Context $context
    $blobs | Get-AzureStorageBlobContent -Context $context -Destination $localFolder

    # Upload the file to the default Data Lake Store account    
    Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $localFolder"SearchLog.tsv" -Destination "/Samples/Data/SearchLog.tsv"

    Write-Host "List the source data ..."  -ForegroundColor Green
    Get-AzureRmDataLakeStoreChildItem -Account $dataLakeStoreName -Path  "/Samples/Data/"
    #endregion

