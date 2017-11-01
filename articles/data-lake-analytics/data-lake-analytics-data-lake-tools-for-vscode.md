---
title: "Средства Azure Data Lake — использование средств Azure Data Lake для Visual Studio Code | Документация Майкрософт"
description: "Узнайте, как с помощью средств Azure Data Lake для Visual Studio Code создавать, тестировать и выполнять скрипты U-SQL. "
Keywords: "VScode,средства Azure Data Lake,локальный запуск,локальная отладка,предварительный просмотр файла хранилища,отправка по пути хранилища,загрузить,отправить"
services: data-lake-analytics
documentationcenter: 
author: jejiang
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/10/2017
ms.author: jejiang
ms.openlocfilehash: e724a8db4424a1e608ae7ee5625cd4cc16f6078f
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2017
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>Использование средств Azure Data Lake для Visual Studio Code

Узнайте, как с помощью средств Azure Data Lake для Visual Studio Code (VS Code) создавать, тестировать и выполнять скрипты U-SQL. Эти сведения также представлены в следующем видеоролике:

<a href="https://www.youtube.com/watch?v=J_gWuyFnaGA&feature=youtu.be"><img src="./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png"></a>

## <a name="prerequisites"></a>Предварительные требования

Средства Azure Data Lake для VSCode поддерживают Windows, Linux и MacOS.  

- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx).

Для MacOS и Linux:
- [Пакет SDK для .NET Core 2.0](https://www.microsoft.com/net/download/core); 
- [Mono 5.2.x](http://www.mono-project.com/download/).

## <a name="install-data-lake-tools"></a>Установка средств Data Lake

После установки необходимых компонентов можно установить средства Data Lake для VS Code.

**Установка средств Data Lake**

1. Откройте Visual Studio Code.
2. Щелкните **Расширения** в левой области. В поле поиска введите **Azure Data Lake**.
3. Щелкните **Установить** рядом с элементом **Azure Data Lake Tools**. Через несколько секунд вместо кнопки **Установить** появится кнопка **Перезагрузить**.
4. Нажмите кнопку **Перезагрузить** для активации расширения **Azure Data Lake Tools**.
5. Щелкните **Перезагрузить окно** для подтверждения. **Средства Azure Data Lake** теперь появятся в области расширений.

    ![Область "Расширения" в средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

 
## <a name="activate-azure-data-lake-tools"></a>Активация Средств Azure Data Lake
Чтобы активировать расширение, создайте новый или откройте имеющийся USQL-файл. 

## <a name="open-the-sample-script"></a>Открытие примера скрипта
Откройте палитру команд (CTRL+SHIFT+P) и введите **ADL: Open Sample Script**. При этом откроется другой экземпляр этого примера. Также можно изменять, настраивать и отправлять скрипт в этом экземпляре.

## <a name="work-with-u-sql"></a>Работа с U-SQL

Для работы с U-SQL нужно открыть файл или папку U-SQL.

**Открытие папки для проекта U-SQL**

1. В Visual Studio Code выберите меню **Файл**, а затем — **Открыть папку**.
2. Укажите папку и выберите **Выбрать папку**.
3. Выберите меню **Файл**, а затем **Создать**. К проекту будет добавлен файл с именем Untitled-1.
4. Введите следующий код в файл Untitled-1:

        @departments  = 
            SELECT * FROM 
                (VALUES
                    (31,    "Sales"),
                    (33,    "Engineering"), 
                    (34,    "Clerical"),
                    (35,    "Marketing")
                ) AS 
                      D( DepID, DepName );
         
        OUTPUT @departments
            TO "/Output/departments.csv"
        USING Outputters.Csv();

    Этот скрипт создает в папке/output файл departments.csv с некоторыми данными.

5. Сохраните файл в открытой папке, присвоив ему имя **myUSQL.usql**. К проекту добавляется еще и файл конфигурации adltools_settings.json.
4. Откройте файл adltools_settings.json и установите следующие значения свойств:

    - Account: учетная запись Data Lake Analytics, входящая в вашу подписку Azure.
    - Database: база данных в вашей учетной записи. По умолчанию используется база данных **master**.
    - Schema: схема в базе данных. Значение по умолчанию — **dbo**.
    - Необязательные параметры:
        - Priority. Значение приоритета в диапазоне от 1 до 1000, где 1 — наивысший приоритет. По умолчанию используется значение **1000**.
        - Parallelism: коэффициент параллелизма в диапазоне от 1 до 150. Значением по умолчанию является максимально допустимый коэффициент параллелизма в учетной записи Azure Data Lake Analytics. 
        
        > [!NOTE] 
        > Если вы введете недопустимые значения, используются значения по умолчанию.

        ![Файл конфигурации средств Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-configuration-file.png)

        Для компиляции и выполнения заданий U-SQL вам потребуется учетная запись вычислений Data Lake Analytics. Необходимо настроить учетную запись вычислений, прежде чем компилировать и запускать задания U-SQL.
    
        После сохранения конфигурации сведения учетной записи, базы данных и схемы отображаются в строке состояния в нижнем левом углу соответствующего USQL-файла. 
 
 
По сравнению с открытием файла при открытии папки можно:

- Использовать файл с выделенным кодом. В режиме одного файла функция Code Behind не поддерживается;
- Использовать файл конфигурации. Если открыть папку, скрипты из этой рабочей папки будут совместно использовать один файл конфигурации.


Скрипт U-SQL компилируется удаленно через службу Data Lake Analytics. При запуске команды **compile** скрипт U-SQL отправляется в учетную запись Data Lake Analytics. Позже Visual Studio Code получит результат компилирования. Из-за удаленной компиляции нужно внести в файл конфигурации сведения, которые позволят Visual Studio Code подключиться к учетной записи Data Lake Analytics.

**Компиляция скрипта U-SQL**

1. Нажмите клавиши CTRL+SHIFT+P, чтобы открыть палитру команд. 
2. Введите **ADL: Compile Script**. Результаты компиляции отображаются в окне **Вывод**. Чтобы запустить компиляцию задания U-SQL можно также щелкнуть правой кнопкой мыши файл скрипта и выбрать **ADL: Compile Script**. Результат компиляции отобразится в области **Вывод**.
 

**Отправка скрипта U-SQL**

1. Нажмите клавиши CTRL+SHIFT+P, чтобы открыть палитру команд. 
2. Введите **ADL: Submit Job**.  Также можно щелкнуть правой кнопкой мыши файл скрипта и выбрать **ADL: Submit Job**. 

После отправки задания U-SQL отобразятся журналы отправки в окне **Вывод** в VS Code. Если отправка пройдет успешно, отобразится URL-адрес задания. URL-адрес задания можно открыть в веб-браузере, чтобы отслеживать состояние задания в реальном времени.

Чтобы включить вывод сведений о задании, задайте **jobInformationOutputPath** в файле **vs code for the u-sql_settings.json**.
 
## <a name="use-a-code-behind-file"></a>Использование файла с выделенным кодом

Файл с выделенным кодом является файлом C#, который связан с одним скриптом U-SQL. В файле с выделенным кодом можно определить скрипт, который относится к UDO, UDA, UDT и UDF. Все эти объекты можно будет напрямую использовать в скрипте, не регистрируя для них сборку. Файл с выделенным кодом помещается в ту же папку, что и связанный с ним файл скрипта U-SQL. Например, для скрипта с именем xxx.usql, файл Code Behind будет иметь имя xxx.usql.cs. Если вручную удалить файл с выделенным кодом, функция выделенного кода будет отключена для связанного с ним скрипта U-SQL. Дополнительные сведения о написании пользовательского кода для скриптов U-SQL можно найти в записи блога [Writing and Using Custom Code in U-SQL – User-Defined Functions]( https://blogs.msdn.microsoft.com/visualstudio/2015/10/28/writing-and-using-custom-code-in-u-sql-user-defined-functions/) (Написание и использование пользовательского кода в U-SQL — определяемые пользователем функции).

Для поддержки выделенного кода необходимо открыть рабочую папку. 

**Создание файла Code Behind**

1. Откройте исходный файл. 
2. Нажмите клавиши CTRL+SHIFT+P, чтобы открыть палитру команд.
3. Введите **ADL: Generate Code Behind**. Файл Code Behind будет создан в той же папке. 

Также можно щелкнуть правой кнопкой мыши файл скрипта и выбрать **ADL: Generate Code Behind**. 

Компиляция и отправка скрипта U-SQL с помощью файла с выделенным кодом выполняется так же, как и для автономного файла скрипта U-SQL.

Следующие два снимка экрана демонстрируют файл Code Behind и связанный с ним файл скрипта U-SQL.
 
![Код программной части в средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-behind.png)

![Файл скрипта кода программной части в средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-behind-call.png) 

Инструкции по поддерживаемых локальному выполнению и отладке см. в статье [Локальный запуск и локальная отладка U-SQL в Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md).

## <a name="use-assemblies"></a>Использование сборок

Дополнительные сведения о разработке сборок см. в статье [Разработка сборок U-SQL для заданий Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-assemblies.md).

Средства Data Lake можно использовать для регистрации сборки пользовательского кода в каталоге Data Lake Analytics.

**Регистрация сборки**

Зарегистрировать сборку можно с помощью команд **ADL: Register Assembly** или **ADL: Register Assembly through Configuration**.

**Для регистрации с помощью команды ADL: Register Assembly**
1.  Нажмите клавиши CTRL+SHIFT+P, чтобы открыть палитру команд.
2.  Введите **ADL: Register Assembly**. 
3.  Укажите локальный путь к сборке. 
4.  Выберите учетную запись Data Lake Analytics.
5.  Выберите базу данных.

Результат: портал откроется в браузере и отобразит процесс регистрации сборки.  

Еще один удобный способ запуска команды **ADL: Register Assembly** — щелкнуть правой кнопкой мыши DLL-файл в Проводнике. 

**Для регистрации с помощью команды ADL: Register Assembly through Configuration**
1.  Нажмите клавиши CTRL+SHIFT+P, чтобы открыть палитру команд.
2.  Введите **ADL: Register Assembly through Configuration**. 
3.  Укажите локальный путь к сборке. 
4.  Отобразится JSON-файл. Просмотрите и при необходимости измените зависимости сборки и параметры ресурсов. Инструкции отображаются в окне **Вывод**. Чтобы перейти к регистрации сборки, сохраните (CTRL+S) JSON-файл.

![Код программной части в средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)
>[!NOTE]
>- Зависимости сборки. Средства Azure Data Lake автоматически определяют наличие любых зависимостей в библиотеке DLL. После обнаружения зависимости отображаются в JSON-файле. 
>- Ресурсы. Ресурсы DLL (например, TXT, PNG и CSV) можно отправить как часть регистрации сборки. 

Еще один удобный способ запуска команды **ADL: Register Assembly through Configuration** — щелкнуть правой кнопкой мыши DLL-файл в Проводнике. 

В приведенном ниже коде U-SQL показано, как вызвать сборку. В этом примере имя сборки — *test*.

```
REFERENCE ASSEMBLY [test];

@a = 
    EXTRACT 
        Iid int,
    Starts DateTime,
    Region string,
    Query string,
    DwellTime int,
    Results string,
    ClickedUrls string 
    FROM @"Sample/SearchLog.txt" 
    USING Extractors.Tsv();

@d =
    SELECT DISTINCT Region 
    FROM @a;

@d1 = 
    PROCESS @d
    PRODUCE 
        Region string,
    Mkt string
    USING new USQLApplication_codebehind.MyProcessor();

OUTPUT @d1 
    TO @"Sample/SearchLogtest.txt" 
    USING Outputters.Tsv();
```

## <a name="connect-to-azure"></a>Подключение к Azure

Перед компиляцией и выполнением скрипта U-SQL в Data Lake Analytics необходимо подключиться к учетной записи Azure.

**Подключение к Azure**

1.  Нажмите клавиши CTRL+SHIFT+P, чтобы открыть палитру команд. 
2.  Введите текст **ADL: Login**. Сведения для входа отобразятся в области **Вывод**.

    ![Палитра команд средств Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)
    ![Сведения о входе устройства в средства Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)
3. Удерживая клавишу CTRL, щелкните URL-адрес входа https://aka.ms/devicelogin, чтобы открыть веб-страницу входа. Введите код **G567LX42V** в текстовое поле, а затем выберите **Продолжить**.

   ![Вставка кода для входа в Средства Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png )   
4.  Следуйте инструкциям, предоставленным на веб-странице входа. При подключении имя учетной записи Azure отображается в строке состояния в нижнем левом углу окна **VS Code**. 

    > [!NOTE] 
    > Если для учетной записи используется двухфакторная проверка подлинности, мы советуем использовать проверку подлинности через телефон, а не PIN-код.

Для выхода введите команду **ADL: Logout**.

## <a name="list-your-data-lake-analytics-accounts"></a>Вывод списка учетных записей Data Lake Analytics

Чтобы проверить подключение, получите список учетных записей Data Lake Analytics.

**Получение списка учетных записей Data Lake Analytics в подписке Azure**

1. Нажмите клавиши CTRL+SHIFT+P, чтобы открыть палитру команд.
2. Введите команду **ADL: List Accounts**. Учетные записи отображаются в области **Вывода**.


## <a name="access-the-data-lake-analytics-catalog"></a>Доступ к каталогу Data Lake Analytics

После подключения к Azure можно получить доступ к каталогу U-SQL, выполнив следующие действия.

**Доступ к метаданным Azure Data Lake Analytics**

1.  Нажмите клавиши CTRL+SHIFT+P, а затем введите **ADL: List Tables**.
2.  Выберите одну из учетных записей Data Lake Analytics.
3.  Выберите одну из баз данных Data Lake Analytics.
4.  Выберите одну из схем. Можно просмотреть список таблиц.

## <a name="view-data-lake-analytics-jobs"></a>Просмотр заданий Data Lake Analytics

**Просмотр заданий Data Lake Analytics**
1.  Откройте палитру команд (CTRL+SHIFT+P) и выберите **ADL: Show Job**. 
2.  Выберите Data Lake Analytics или локальную учетную запись. 
3.  Подождите, пока отобразится список заданий для учетной записи.
4.  Выберите задание из списка заданий. Средства Data Lake отобразят сведения о задании на портале Azure и откроют файл JobInfo в VS Code.

    ![Типы объектов IntelliSencse в средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="azure-data-lake-storage-integration"></a>Интеграция с Azure Data Lake Store

Связанные с Azure Data Lake Store команды можно использовать для:
 - просмотра ресурсов Azure Data Lake Storage; [Вывод пути к хранилищу.](#list-the-storage-path) 
 - предварительного просмотра файла Azure Data Lake Storage; [Предварительный просмотр файла хранилища.](#preview-the-storage-file) 
 - отправки файла напрямую в Azure Data Lake Store в VS Code. [Отправка файла.](#upload-file)
 - загрузки файла напрямую из службы хранилища Azure Data Lake в VS Code. [Загрузка файла.](#download-file)

## <a name="list-the-storage-path"></a>Вывод пути к хранилищу 

**Вывод пути к хранилищу через палитру команд**

Щелкните правой кнопкой мыши редактор скриптов и выберите **ADL: List Storage Path**.

Выберите папку из списка или щелкните **Enter Path** (Укажите путь) или **Browse from Root**  (Перейти от корня) (в качестве примера используется 1 вариант). Выберите **учетную запись ADLA**. Перейдите или укажите путь к папке хранилища (например, /output/). В палитре команд выводятся сведения о пути в соответствии с записями.

![Результат вывода пути к хранилищу в средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/list-storage-path.png)

Более удобный способ вывода относительного пути — открыть контекстное меню.

**Вывод пути к хранилищу с помощью щелчка правой кнопкой мыши**

Чтобы продолжить, щелкните строку пути правой кнопкой мыши и выберите **List Storage Path** (Вывод пути к хранилищу).

![Контекстное меню в средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)


## <a name="preview-the-storage-file"></a>Предварительный просмотр файла хранилища

Щелкните правой кнопкой мыши редактор скриптов и выберите **ADL: Preview Storage File** (ADL: предварительно просмотреть файл хранилища).

Выберите **учетную запись ADLA**. Введите путь к файлу хранилища Azure (например, /output/SearchLog.txt). В результате файл откроется в VSCode.

   ![Результат предварительного просмотра файла в Средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/preview-storage-file.png)

Еще один способ предварительно просмотреть файл хранилища — открыть контекстное меню полного или относительного пути к файлу в редакторе скриптов. 

## <a name="upload-file"></a>Отправка файла 

Файлы можно отправить с помощью команд **ADL: Upload File** и **ADL: Upload File through Configuration**.

**Отправка файлов с помощью команды ADL: Upload File through Configuration**
1.  Щелкните правой кнопкой мыши редактор скриптов, а затем выберите **Upload File through Configuration** (Отправить файл с помощью конфигурации).
2.  В VS Code отобразится JSON-файл. Вы можете ввести сразу несколько путей к файлам, чтобы отправить несколько файлов. Инструкции отображаются в окне **Вывод**. Чтобы продолжить отправку файла, сохраните (CTRL+S) JSON-файл.

       ![Путь к файлу в средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-upload-file.png)

3.  Результат: в окне **Вывод** отобразится состояние отправки файла.

       ![Состояние отправки в средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-upload-status.png)     

В то же время вы можете отслеживать [состояние отправки](#check-storage-tasks-status).

**Отправка файлов с помощью команды ADL: Upload File**

Щелкните правой кнопкой мыши редактор скриптов и выберите **Upload File** (Отправить файл).

Введите **путь к локальному файлу**. Выберите папку из списка или щелкните **Enter Path** (Укажите путь) или **Browse from Root**  (Перейти от корня) (в качестве примера используется 1 вариант). Выберите **учетную запись ADLA**. Перейдите или укажите путь к папке хранилища (например, /output/). Щелкните **Choose Current Folder** (Выбрать текущую папку), чтобы указать место назначения отправки.

![Состояние отправки в средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/upload-file.png)    


Еще один способ отправки файлов в хранилище — открыть контекстное меню полного или относительного пути к файлу в редакторе скриптов.

В то же время вы можете отслеживать [состояние отправки](#check-storage-tasks-status).

## <a name="download-file"></a>Загрузка файла 
Файлы можно загрузить путем ввода команд **ADL: Download Storage File** (ADL: загрузить файл хранилища) или **ADL: Download Storage File through Configuration** (ADL: загрузить файл хранилища с помощью конфигурации).

**Загрузка файлов с помощью команды ADL: Download File through Configuration** (ADL: загрузить файл хранилища с помощью конфигурации)
1. Щелкните правой кнопкой мыши редактор скриптов, а затем выберите **Download Storage File through Configuration** (Загрузить файл с помощью конфигурации).
2. В VS Code отобразится JSON-файл. Вы можете ввести несколько путей к файлам, чтобы загрузить несколько файлов одновременно. Инструкции отображаются в окне **Вывод**. Чтобы продолжить загрузку файла, сохраните (CTRL+S) JSON-файл.

    ![Загрузка файлов с помощью конфигурации в средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-files.png)

3.  Результат: в окне **Вывод** отобразится состояние отправки файла.

    ![Результаты загрузки нескольких файлов в средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-file-result.png)     

В то же время вы можете отслеживать [состояние загрузки](#check-storage-tasks-status).

**Загрузка файлов c помощью команды "ADL: Download Storage File"** (ADL: загрузить файл хранилища)

Щелкните правой кнопкой мыши редактор скриптов, а затем выберите **Download Storage File** (Загрузить файл хранилища).

Выберите папку из списка или щелкните **Enter Path** (Укажите путь) или **Browse from Root**  (Перейти от корня) (в качестве примера используется 1 вариант). Выберите **учетную запись ADLA**. Перейдите или укажите путь к папке хранилища (например, /output/) и выберите файл для загрузки.

   ![Состояние загрузки в средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/download-file.png) 

   На рисунке результатов файл сохранен в качестве временной папки. Можно самостоятельно задать путь загрузки по умолчанию для параметра **usql.defaultLocalFolderForDownload** через меню VSCode **File** (Файл) -> **Preferences** (Параметры) -> **Setting** (Настройка).

Еще один способ загрузки файлов хранилища — открыть контекстное меню полного или относительного пути к файлу в редакторе скриптов.

В то же время вы можете отслеживать [состояние загрузки](#check-storage-tasks-status).

## <a name="check-storage-tasks-status"></a>Проверка состояния задач хранилища
После завершения загрузки и отправки состояние отображается в нижней строке состояния.
1. Щелкните строку состояния, и информация о загрузке и отправке отобразится на панели **OUTPUT** (ВЫХОДНЫЕ ДАННЫЕ).

   ![Проверка состояния хранилища в средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-status.png)


## <a name="open-azure-storage-explorer"></a>Открытие обозревателя хранилищ Azure
Вы можете открыть **обозреватель хранилищ Azure**, введя команду **ADL: Open Web Azure Storage Explorer** или выбрав ее в контекстном меню.

**Открытие обозревателя хранилищ Azure**

1. Нажмите клавиши CTRL+SHIFT+P, чтобы открыть палитру команд.
2. Введите команду **Open Web Azure Storage Explorer** или щелкните правой кнопкой мыши относительный либо полный путь в редакторе скриптов, а затем выберите **Open Web Azure Storage Explorer** (Открыть обозреватель веб-хранилищ Azure).
3. Выберите учетную запись Data Lake Analytics.

Средства Data Lake откроют путь к хранилищу Azure на портале Azure. Можно найти путь и предварительно просмотреть файл в Интернете.

## <a name="local-run-and-local-debug-for-windows-users"></a>Локальное выполнение и локальная отладка для пользователей Windows
Локальное выполнение U-SQL тестирует локальные данные и проверяет скрипт локально перед публикацией кода в Data Lake Analytics. Функция локальной отладки позволяет завершить следующие задачи перед отправкой кода в Data Lake Analytics: 
- Отладка кода программной части C#. 
- Пошаговая отладка кода. 
- Проверка скрипта локально.

Инструкции по локальному выполнению и отладке см. в статье [Локальный запуск и локальная отладка U-SQL в Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md).

## <a name="additional-features"></a>Дополнительные функции

Средства Data Lake для VS Code поддерживают следующие функции:

-   Автоматическое завершение IntelliSense: предлагаемые варианты отображаются во всплывающих окнах вокруг элементов, таких как ключевые слова, методы и переменные. Разные значки обозначают объекты разных типов:

    - тип данных Scala;
    - сложный тип данных;
    - встроенные пользовательские типы;
    - коллекции и классы .NET;
    - выражения C#;
    - встроенные функции и объекты C#, определяемые пользователем; 
    - функции U-SQL;
    - функции U-SQL для работы с окнами.
 
    ![Типы объектов IntelliSencse в средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)
 
-   Автоматическое завершение IntelliSense в метаданных Data Lake Analytics: средства Data Lake скачивают сведения метаданных Data Lake Analytics локально. Функция IntelliSense автоматически заполняет объекты, включая базы данных, схемы, таблицы, представления, функции, функции с табличным значением, процедуры и сборки C#, на основе метаданных Data Lake Analytics.
 
    ![Метаданные IntelliSense в средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

-   Маркер ошибки IntelliSense: средства Data Lake подчеркивают ошибки ввода в файлах U-SQL и C#. 
-   Выделение синтаксиса: средства Data Lake используют различные цвета для выделения элементов, таких как переменные, ключевые слова, типы данных и функции. 

    ![Подсветка синтаксиса в средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о локальном выполнении и локальной отладке U-SQL с помощью Visual Studio Code см. в статье [Локальный запуск и локальная отладка U-SQL в Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md).
- Дополнительные сведения о начале работы с Data Lake Analytics см. в статье [Начало работы с Azure Data Lake Analytics с помощью портала Azure](data-lake-analytics-get-started-portal.md).
- Дополнительные сведения об использовании средств Data Lake для U-SQL см. в статье [Разработка скриптов U-SQL с помощью средств Data Lake для Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Дополнительные сведения о разработке сборок см. в статье [Разработка сборок U-SQL для заданий Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-assemblies.md).



