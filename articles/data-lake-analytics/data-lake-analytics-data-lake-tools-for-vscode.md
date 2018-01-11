---
title: "Средства Azure Data Lake — использование средств Azure Data Lake для Visual Studio Code | Документация Майкрософт"
description: "Узнайте, как с помощью средств Azure Data Lake для Visual Studio Code создавать, тестировать и выполнять скрипты U-SQL. "
Keywords: VScode,Azure Data Lake Tools,Local run,Local debug,Local Debug,preview file,upload to storage path,download,upload
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
ms.date: 11/10/2017
ms.author: jejiang
ms.openlocfilehash: c70cfc309fe60f0641c89b4a341e3364af74771a
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/21/2017
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>Использование средств Azure Data Lake для Visual Studio Code

Узнайте, как с помощью средств Azure Data Lake для Visual Studio Code (VS Code) создавать, тестировать и выполнять скрипты U-SQL. Эти сведения также представлены в следующем видеоролике:

<a href="https://channel9.msdn.com/Series/AzureDataLake/Azure-Data-Lake-Tools-for-VSCode?term=ADL%20Tools%20for%20VSCode"><img src="./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png"></a>

## <a name="prerequisites"></a>Технические условия

Средства Озера данных Azure для VSCode поддерживает Windows, Linux и MacOS.  

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

5. Сохраните файл в открытой папке, присвоив ему имя **myUSQL.usql**. В папку добавляется еще и файл конфигурации xxx_settings.json.
6. Откройте файл xxx_settings.json и установите следующие значения свойств:

    - Учетная запись. Учетная запись Data Lake Analytics в вашей подписке Azure, необходимая для компиляции и запуска заданий U-SQL, поэтому необходимо настроить учетную запись компьютера перед компиляцией и запуском заданий U-SQL.
    - Database: база данных в вашей учетной записи. По умолчанию используется база данных **master**.
    - Schema: схема в базе данных. Значение по умолчанию — **dbo**.
    - Необязательные параметры:
        - Priority. Значение приоритета в диапазоне от 1 до 1000, где 1 — наивысший приоритет. По умолчанию используется значение **1000**.
        - Parallelism: коэффициент параллелизма в диапазоне от 1 до 150. Значением по умолчанию является максимально допустимый коэффициент параллелизма в учетной записи Azure Data Lake Analytics. 
        
        ![Файл конфигурации средств Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-configuration-file.png)
      
        > [!NOTE] 
        > После сохранения конфигурации сведения учетной записи, базы данных и схемы отображаются в строке состояния в нижнем левом углу соответствующего файла .usql.

**Компиляция скрипта U-SQL**

1. Нажмите клавиши CTRL+SHIFT+P, чтобы открыть палитру команд. 
2. Введите **ADL: Compile Script**. Результаты компиляции отображаются в окне **Вывод**. Чтобы запустить компиляцию задания U-SQL можно также щелкнуть правой кнопкой мыши файл скрипта и выбрать **ADL: Compile Script**. Результат компиляции отобразится в области **Вывод**.
 

**Отправка скрипта U-SQL**

1. Нажмите клавиши CTRL+SHIFT+P, чтобы открыть палитру команд. 
2. Введите **ADL: Submit Job**.  Также можно щелкнуть правой кнопкой мыши файл скрипта и выбрать **ADL: Submit Job**. 

После отправки задания U-SQL отобразятся журналы отправки в окне **Вывод** в VS Code. Если отправка пройдет успешно, отобразится URL-адрес задания. URL-адрес задания можно открыть в веб-браузере, чтобы отслеживать состояние задания в реальном времени.

Чтобы включить вывод сведений о задании, задайте **jobInformationOutputPath** в файле **vs code for the u-sql_settings.json**.
 
**Игнорировать набор Git**

1. Нажмите клавиши CTRL+SHIFT+P, чтобы открыть палитру команд. 
2. Введите **ADL: игнорировать набор Git**.

    - Если у вас нет **.gitIgnore** файла в рабочей папке VSCode, файл с именем **.gitIgnor** создается в папке. Четыре элемента (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache**, **obj**) по умолчанию будут добавлены в файл. Дополнительные обновления можно выполнять при необходимости.
    - Если у вас уже есть **.gitIgnore** файла в рабочей папке VSCode, средство добавляются четыре элемента (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache**, **obj**) в вашей **.gitIgnore** файл, если четыре элемента не были включены в файл.

  ![Файл конфигурации средств Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-gitignore.png)

## <a name="use-python-r-and-csharp-code-behind-file"></a>Использование файл с выделенным кодом CSharp, Python и R
Средство Azure Озера данных поддерживает несколько настраиваемых кодов см. инструкции [разработки U-SQL с Python, R и c# для аналитики Озера данных Azure в VSCode](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md).

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
>- Ресурсы: Библиотека DLL ресурсов (например, txt, .png и CSV) можно передать в процессе регистрации сборки. 

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
2.  Введите текст **ADL: Login**. Сведения об имени входа отображается в верхней области.

    ![Палитра команд средств Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)
    ![Сведения о входе устройства в средства Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)
3.  Нажмите кнопку **& Копировать откройте** открыть веб-страницу входа с URL-адрес: https://aka.ms/devicelogin. Вставьте код **G567LX42V** в текстовое поле, а затем выберите **Продолжить**.

   ![Вставка кода для входа в Средства Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png )   
4.  Следуйте инструкциям, предоставленным на веб-странице входа. При подключении имя учетной записи Azure отображается в строке состояния в нижнем левом углу окна **VS Code**. 

    > [!NOTE] 
    >- Если вы выполняли вход до, но вы не вышли из еще средство Озера данных автоматически подписывает в следующий раз.
    >- Если для учетной записи используется двухфакторная проверка подлинности, мы советуем использовать проверку подлинности через телефон, а не PIN-код.


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
 - отправки файла напрямую в Azure Data Lake Store в VS Code. [Отправка папки или файла](#upload-file-or-folder).
 - загрузки файла напрямую из службы хранилища Azure Data Lake в VS Code. [Загрузка файла.](#download-file)

## <a name="list-the-storage-path"></a>Вывод пути к хранилищу 

**Вывод пути к хранилищу через палитру команд**

Щелкните редактор сценариев правой кнопкой мыши и выберите **ADL: List Path** (ADL: путь к списку).

Выберите папку из списка или щелкните **Enter Path** (Укажите путь) или **Browse from Root**  (Перейти от корня) (в качестве примера используется 1 вариант). Выберите **учетную запись ADLA**. Перейдите или укажите путь к папке хранилища (например, /output/). В палитре команд выводятся сведения о пути в соответствии с записями.

![Результат вывода пути к хранилищу в средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/list-storage-path.png)

Более удобный способ вывода относительного пути — открыть контекстное меню.

**Вывод пути к хранилищу с помощью щелчка правой кнопкой мыши**

Чтобы продолжить, щелкните строку пути правой кнопкой мыши и выберите **List Path** (Путь к списку).

![Контекстное меню в средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)


## <a name="preview-the-storage-file"></a>Предварительный просмотр файла хранилища

Щелкните редактор сценариев правой кнопкой мыши и выберите **ADL: Preview File** (ADL: предварительный просмотр файла).

Выберите **учетную запись ADLA**. Введите путь к файлу хранилища Azure (например, /output/SearchLog.txt). В результате файл откроется в VSCode.

   ![Результат предварительного просмотра файла в Средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/preview-storage-file.png)

Еще один способ предварительно просмотреть файл — открыть контекстное меню полного или относительного пути к файлу в редакторе скриптов. 

## <a name="upload-file-or-folder"></a>Отправка папки или файла

1. Щелкните редактор сценариев правой кнопкой мыши и выберите **Upload File** (Отправить файл) или **Upload Folder** (Отправить папку).

2. Если вы выбрали отправку файла, выберите один или несколько файлов, а если вы выбрали отправку папки, выберите всю папку. Затем щелкните **Отправить**. Выберите папку хранилища из списка или щелкните **Enter Path** (Укажите путь) или **Browse from Root** (Перейти от корня) (в качестве примера используется 1 вариант). Выберите **учетную запись ADLA**. Перейдите или укажите путь к папке хранилища (например, /output/). Щелкните **Choose Current Folder** (Выбрать текущую папку), чтобы указать место назначения отправки.

   ![Состояние отправки в средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/upload-file.png)    


   Еще один способ отправки файлов в хранилище — открыть контекстное меню полного или относительного пути к файлу в редакторе скриптов.

В то же время вы можете отслеживать [состояние отправки](#check-storage-tasks-status).


## <a name="download-file"></a>Загрузка файла 
Скачать файлы можно путем ввода команды **ADL: Download File** (ADL: скачать файл) или **ADL: Download File (Advanced)** (ADL: скачать файл (Дополнительно)).

**Скачивание файлов с использованием команды ADL: Download File (Advanced) (ADL: скачивание файла (Дополнительно))**
1. Щелкните правой кнопкой мыши редактор скриптов, а затем выберите **Download File (Advanced)** (Скачать файл (Дополнительно)).
2. В VS Code отобразится JSON-файл. Вы можете ввести несколько путей к файлам, чтобы загрузить несколько файлов одновременно. Инструкции отображаются в окне **Вывод**. Чтобы продолжить загрузку файла, сохраните (CTRL+S) JSON-файл.

    ![Загрузка файлов с помощью конфигурации в средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-files.png)

3.  Результат: в окне **Вывод** отобразится состояние отправки файла.

    ![Результаты загрузки нескольких файлов в средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-file-result.png)     

В то же время вы можете отслеживать [состояние загрузки](#check-storage-tasks-status).

**Скачивание файлов с использованием команды ADL: Download File (ADL: скачать файл)**

1. Щелкните редактор сценариев правой кнопкой мыши, а затем выберите **Загрузить файл**, далее укажите папку назначения в диалоговом окне **Выбор папки**.

2. Выберите папку из списка или щелкните **Enter Path** (Укажите путь) или **Browse from Root**  (Перейти от корня) (в качестве примера используется 1 вариант). Выберите **учетную запись ADLA**. Перейдите или укажите путь к папке хранилища (например, /output/) и выберите файл для загрузки.

   ![Состояние загрузки в средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/download-file.png) 

   
   Еще один способ загрузки файлов хранилища — открыть контекстное меню полного или относительного пути к файлу в редакторе скриптов.

В то же время вы можете отслеживать [состояние загрузки](#check-storage-tasks-status).

## <a name="check-storage-tasks-status"></a>Проверка состояния задач хранилища
После завершения загрузки и отправки состояние отображается в нижней строке состояния.
1. Щелкните строку состояния, и информация о загрузке и отправке отобразится на панели **OUTPUT** (ВЫХОДНЫЕ ДАННЫЕ).

   ![Проверка состояния хранилища в средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-status.png)

## <a name="vscode-explorer-integration-with-azure-data-lake"></a>Интеграция обозревателя VSCode с Azure Data Lake

**Интеграция Azure** 

- До входа в Azure, всегда можно развернуть **DATALAKE EXPLORER**, нажмите кнопку **входа в Azur** входа в Azure. После входа в систему, вы увидите всех подписок под учетной записью Azure, перечислены в левой панели **DATALAKE EXPLORER**. 

   ![Обозреватель Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/sign-in-datalake-explorer.png)

   ![Обозреватель Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer.png)

**ADLA метаданных навигации** 

- Разверните узел подписки Azure, можно перейти U-SQL базы данных, представление **схемы**, **учетные данные**, **сборки**, **таблицы**, **Индекс**, и т. д., в узле U-SQL базы данных.

**Управление сущностями метаданных ADLA**

- Разверните **баз данных U SQL**, можно создать новую базу данных, схемы, таблицы, табличные типы, индекс, статистика, щелкнув правой кнопкой мыши **скрипт для создания** контекстное меню в соответствующий узел. На странице открытого скрипта изменить скрипт в соответствии с потребностями, а затем отправить задание, щелкните правой кнопкой мыши контекстном меню **ADL: Отправка задания**. После завершения его создания, — контекстное меню **обновление** для отображения новых создан элемент. Элемент также можно удалить, щелкнув правой кнопкой мыши в контекстном меню **удалить**.

   ![DataLake explorer создает новый элемент меню](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create.png)

   ![DataLake explorer создает новый элемент скрипт](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create-snippet.png)

**Регистрация сборки ADLA**

 - Вы можете **зарегистрировать сборку** в соответствующей базы данных, щелкнув правой кнопкой мыши **сборки** узла.

    ![Обозреватель Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer-register-assembly.png)

**Интеграция ADLS.** 

 - Перейдите к **учетной записи хранилища**, вы можете **предварительного просмотра**, **загрузки**, **удаление**, **относительный путь копирования**, **Копировать полный путь** контекстное меню для узла файла. Вы можете **обновление**, **отправить**, **отправить папку**, **удалить** щелкните правой кнопкой мыши в контекстном меню узла папки.

   ![Обозреватель Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-folder-menu.png)

   ![Обозреватель Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-download-preview-file.png)

## <a name="open-adl-storage-explorer-in-portal"></a>Открытие обозревателя хранилища ADL на портале
1. Нажмите клавиши CTRL+SHIFT+P, чтобы открыть палитру команд.
2. Введите команду **Open Web Azure Storage Explorer** или щелкните правой кнопкой мыши относительный либо полный путь в редакторе скриптов, а затем выберите **Open Web Azure Storage Explorer** (Открыть обозреватель веб-хранилищ Azure).
3. Выберите учетную запись Data Lake Analytics.

Средства Data Lake откроют путь к хранилищу Azure на портале Azure. Можно найти путь и предварительно просмотреть файл в Интернете.

## <a name="local-run-and-local-debug-for-windows-users"></a>Локальное выполнение и локальная отладка для пользователей Windows
U-SQL локального выполнения проверяет локальных данных и проверяет скрипт локально перед публикацией кода для аналитики Озера данных. Функция локальной отладки позволяет завершить следующие задачи перед отправкой кода в Data Lake Analytics: 
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
- [Разработка U-SQL с помощью Python, R, и CSharp для Azure Data Lake Analytics в VSCode](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [Локальный запуск и локальная отладка U-SQL в Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
- [Начало работы с Azure Data Lake Analytics с помощью портала Azure](data-lake-analytics-get-started-portal.md)
- [Разработка скриптов U-SQL с помощью средств Data Lake для Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
- [Разработка сборок U-SQL для заданий Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-assemblies.md)




