---
title: "Средства Azure Data Lake — использование средств Azure Data Lake для Visual Studio Code | Документы Майкрософт"
description: "Узнайте, как с помощью средств Azure Data Lake для Visual Studio Code создавать, тестировать и выполнять скрипты U-SQL. "
Keywords: "VScode,средства Azure Data Lake,локальный запуск,локальная отладка,предварительный просмотр файла хранилища,отправка по пути хранилища"
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
ms.date: 07/14/2017
ms.author: jejiang
ms.translationtype: HT
ms.sourcegitcommit: c999eb5d6b8e191d4268f44d10fb23ab951804e7
ms.openlocfilehash: 0ed3d7a0057eb446b3e1d16019ac74c641ae3138
ms.contentlocale: ru-ru
ms.lasthandoff: 07/17/2017

---

# <a name="use-the-azure-data-lake-tools-for-visual-studio-code"></a>Использование средств Azure Data Lake для Visual Studio Code

Узнайте, как с помощью средств Azure Data Lake для Visual Studio Code (VSCode) создавать, тестировать и выполнять скрипты U-SQL.  Эти сведения также представлены в следующем видеоролике:

<a href="https://www.youtube.com/watch?v=J_gWuyFnaGA&feature=youtu.be"><img src="./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png"></a>

## <a name="prerequisites"></a>Предварительные требования

Средства Data Lake можно устанавливать на всех платформах, поддерживаемых VSCode, то есть на Windows, Linux и MacOS. Здесь описаны предварительные условия для различных платформ.

- Windows

    - [Visual Studio Code]( https://www.visualstudio.com/products/code-vs.aspx).
    - [Среда выполнения Java SE, версия 8 с обновлением 77 или более поздние выпуски](https://java.com/download/manual.jsp). Необходимо добавить путь к файлу java.exe в системную переменную среды path.  Этот процесс описан в [инструкции по установке и изменению переменной окружения PATH]( https://www.java.com/download/help/path.xml). Обычно этот путь имеет формат C:\Program Files\Java\jdk1.8.0_77\jre\bin
    - [Пакет SDK для .NET Core 1.0.3 или среда выполнения .NET Core 1.1](https://www.microsoft.com/net/download).
    
- Linux (мы рекомендуем использовать Ubuntu 14.04 LTS)

    - [Visual Studio Code]( https://www.visualstudio.com/products/code-vs.aspx). Используйте следующую команду для установки:

        sudo dpkg -i code_<version_number>_amd64.deb

    - [Mono 4.2.x](http://www.mono-project.com/docs/getting-started/install/linux/). 

        - Обновите источник пакетов deb, выполнив следующие команды:

                sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 3FA7E0328081BFF6A14DA29AA6A19B38D3D831EF
                echo "deb http://download.mono-project.com/repo/debian wheezy/snapshots 4.2.4.4/main" | sudo tee /etc/apt/sources.list.d/mono-xamarin.list
                sudo apt-get update

        - Установите mono, выполнив команду:

                sudo apt-get install mono-complete

            > [!NOTE] 
            > Mono 4.6 не поддерживается.  Необходимо полностью удалить версию 4.6, прежде чем устанавливать 4.2.x.  

        - [Среда выполнения Java SE, версия 8 с обновлением 77 или более поздние выпуски](https://java.com/download/manual.jsp). Инструкции доступны [здесь]( https://java.com/en/download/help/linux_x64_install.xml).
        - [Пакет SDK для .NET Core 1.0.3 или среда выполнения .NET Core 1.1](https://www.microsoft.com/net/download).
- MacOS

    - [Visual Studio Code]( https://www.visualstudio.com/products/code-vs.aspx).
    - [Mono 4.2.4](http://download.mono-project.com/archive/4.2.4/macos-10-x86/). 
    - [Среда выполнения Java SE, версия 8 с обновлением 77 или более поздние выпуски](https://java.com/download/manual.jsp). Инструкции доступны [здесь](https://java.com/en/download/help/mac_install.xml).
    - [Пакет SDK для .NET Core 1.0.3 или среда выполнения .NET Core 1.1](https://www.microsoft.com/net/download).

## <a name="install-the-data-lake-tools"></a>Установка средств Data Lake

После установки всех необходимых компонентов можно переходить к установке средств Data Lake для VSCode.

**Установка средств Data Lake**

1. Откройте **Visual Studio Code**.
2. Нажмите клавиши **CTRL + P**, а затем введите:
```
ext install usql-vscode-ext
```
Вы увидите список расширений для Visual Studio Code. Среди них есть и **средство Azure Data Lake**.

3. Щелкните **Установить** рядом с элементом **Средство Azure Data Lake**. Через несколько секунд вместо кнопки "Установить" появится новая кнопка "Перезагрузить".
4. Щелкните **Перезагрузить**, чтобы активировать расширение.
5. Нажмите кнопку **ОК** для подтверждения. Средства Azure Data Lake теперь появятся в области "Расширения".
    ![Установка Средств Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

## <a name="activate-azure-data-lake-tools"></a>Активация Средств Azure Data Lake
Чтобы активировать расширение, создайте новый или откройте существующий USQL-файл. 

## <a name="connect-to-azure"></a>Подключение к Azure

Прежде чем компилировать и выполнять скрипты U-SQL в Azure Data Lake Analytics, необходимо подключиться к учетной записи Azure.

**Подключение к Azure**

1.  Откройте палитру команд, нажав клавиши **CTRL + SHIFT + P**. 
2.  Введите текст **ADL: Login**. Сведения о входе будут выведены в области выходных данных.

    ![Палитра команд Средств Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)
    ![Сведения о входе в Средства Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)
3. Удерживая нажатой клавишу CTRL, щелкните URL-адрес входа (https://aka.ms/devicelogin), чтобы открыть веб-страницу входа в систему. Скопируйте и вставьте код G567LX42V в текстовое поле ниже, а затем нажмите кнопку "Продолжить", чтобы продолжить работу.

   ![Вставка кода для входа в Средства Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png )   
4.  Следуйте инструкциям, представленным на странице входа. После подключения в строке состояния в левом нижнем углу окна VSCode будет отображаться имя учетной записи Azure. 

    > [!NOTE] 
    > Если для учетной записи используется двухфакторная проверка подлинности, мы рекомендуем выбрать проверку подлинности через телефон, а не PIN-код.

Чтобы выйти из учетной записи, используйте команду **ADL: Logout**.

## <a name="list-data-lake-analytics-accounts"></a>Список учетных записей аналитики озера данных

Чтобы проверить подключение, попробуйте получить список учетных записей Data Lake Analytics:

**Получение списка учетных записей Data Lake Analytics в подписке Azure**

1. Откройте палитру команд, нажав клавиши **CTRL + SHIFT + P**.
2. Введите текст **ADL: List Accounts**.  Учетные записи отображаются в области **Вывода**.

## <a name="open-sample-script"></a>Открытие примера скрипта

Откройте палитру команд (**CTRL+SHIFT+P**) и выберите **ADL: Open Sample Script**. Откроется еще один экземпляр этого примера. Вы можете редактировать, настраивать его и отправлять скрипт.

## <a name="work-with-u-sql"></a>Работа с U-SQL

Для работы с U-SQL нужно открыть файл или папку U-SQL.

**Открытие папки для проекта U-SQL**

1. В Visual Studio Code щелкните меню **Файл** и выберите элемент **Открыть папку**.
2. Укажите папку и нажмите **Выбрать папку**.
3. Щелкните меню **Файл**, затем **Создать**. К проекту будет добавлен файл с именем **Untilted 1**.
4. Скопируйте приведенный ниже код и вставьте его в этот файл.

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
            TO “/Output/departments.csv”

    Этот скрипт создает в папке/output файл departments.csv с некоторыми данными.

5. Сохраните файл в открытой папке, присвоив ему имя **myUSQL.usql**. Обратите внимание, что к проекту добавляется еще и файл конфигурации **adltools_settings.json**.
4. Откройте файл **adltools_settings.json** и установите следующие значения свойств.

    - Account: учетная запись Data Lake Analytics, входящая в вашу подписку Azure.
    - Database: база данных в вашей учетной записи. По умолчанию используется база данных master.
    - Schema: схема в базе данных. Значение по умолчанию — dbo.
    - Необязательные параметры:
        - Priority: значение приоритета в диапазоне от 1 до 1000, где 1 — наивысший приоритет. По умолчанию используется значение 1000.
        - Parallelism: коэффициент параллелизма в диапазоне от 1 до 150. Значением по умолчанию является максимальный допустимый коэффициент параллелизма в вашей учетной записи ADLA. 
        
        > [!NOTE] 
        > Если вы введете недопустимые значения, используются значения по умолчанию.

    ![Файл конфигурации средств Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-configuration-file.png)

    Для компиляции и запуска задания U-SQL вам потребуется учетная запись вычислений Data Lake Analytics.  Настройте учетную запись вычислений, прежде чем компилировать и запускать задания U-SQL.
    
    После сохранения конфигурации сведения об учетной записи, базе данных и схеме отображаются в строке состояния в нижнем левом углу соответствующего файла USQL. 
 
 

Открытие папки дает несколько преимуществ по сравнению с открытием файла:

- использование файла Code Behind.  В режиме одного файла функция Code Behind не поддерживается;
- использование файла конфигурации. Если открыть папку, скрипты из этой рабочей папки будут совместно использовать общий файл конфигурации.


Компиляция скрипта U-SQL выполняется службой Data Lake Analytics удаленно.  Когда вы запустите команду компиляции, скрипт U-SQL отправляется в учетную запись Data Lake Analytics. Позднее в Visual Studio Code возвращается результат компиляции. Для выполнения удаленной компиляции нужно включить в файл конфигурации сведения, которые позволят Visual Studio Code подключиться к учетной записи Data Lake Analytics.

**Компиляция скрипта U-SQL**

1. Откройте палитру команд, нажав клавиши **CTRL + SHIFT + P**. 
2. Введите **ADL: Compile Script**. В окне вывода появится результат компиляции. Чтобы запустить компиляцию задания U-SQL, можно также щелкнуть правой кнопкой файл скрипта и нажать кнопку **ADL: Compile Script**. Результат компиляции отобразится на панели вывода.
 

**Отправка скрипта U-SQL**

1. Откройте палитру команд, нажав клавиши **CTRL + SHIFT + P**. 
2. Введите **ADL: Submit Job**.  Чтобы отправить задание U-SQL, можно также щелкнуть правой кнопкой файл скрипта и нажать кнопку **ADL: Submit Job**. 

После отправки задания U-SQL в окне вывода VSCode отобразится протокол отправки. Если отправка пройдет успешно, вы увидите здесь и URL-адрес задания. URL-адрес задания можно открыть в веб-браузере, чтобы отслеживать состояние задания в реальном времени.

Чтобы включить вывод сведений о задании, установите значение "jobInformationOutputPath" в файле **vscode for u-sql_settings.json**.
 
## <a name="use-code-behind-file"></a>Использование файла Code Behind

Файл с выделенным кодом представляет собой файл CSharp, связанный с одним скриптом U-SQL. В файл Code Behind можно включить определяемые пользователем операторы, статистические функции, типы данных и функции для вашего скрипта. Все эти объекты можно будет напрямую использовать в скрипте, не регистрируя для них сборку. Файл Code Behind помещается в ту же папку, что и связанный с ним файл скрипта U-SQL. Например, для скрипта с именем xxx.usql, файл Code Behind будет иметь имя xxx.usql.cs. Если вручную удалить файл Code Behind, эта функция для связанного скрипта U-SQL будет отключена. Дополнительные сведения о написании пользовательского кода для скриптов U-SQL можно найти в записи блога [Writing and Using Custom Code in U-SQL – User-Defined Functions]( https://blogs.msdn.microsoft.com/visualstudio/2015/10/28/writing-and-using-custom-code-in-u-sql-user-defined-functions/) (Написание и использование пользовательского кода в SQL-U — определяемые пользователем функции).

Чтобы использовать возможности Code Behind, необходимо открыть рабочую папку. 

**Создание файла Code Behind**

1. Откройте исходный файл. 
2. Откройте палитру команд, нажав клавиши **CTRL + SHIFT + P**.
3. Введите **ADL: Generate Code Behind**.  Файл Code Behind будет создан в той же папке. 

Чтобы создать файл кода программной части, можно также щелкнуть правой кнопкой файл сценария и нажать кнопку **ADL: Generate Code Behind**. 

Компиляция и отправка скрипта U-SQL с файлом Code Behind выполняется так же, как и для автономного скрипта U-SQL.

Следующие два снимка экрана демонстрируют файл Code Behind и связанный с ним файл скрипта U-SQL.
 
![Файл Code Behind в Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-behind.png)

![Файл Code Behind в Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-behind-call.png) 

## <a name="use-assemblies"></a>Использование сборок

Сведения о разработке сборок см. в статье [Разработка сборок U-SQL для заданий Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-assemblies.md).

С помощью средств Data Lake можно регистрировать сборки пользовательского кода в каталоге Data Lake Analytics.

**Регистрация сборки**

Зарегистрировать сборку можно с помощью **ADL: Register Assembly** или **ADL: Register Assembly through Configuration**.

**ADL: Register Assembly**
1.  Нажмите клавиши **CTRL + SHIFT + P**, чтобы открыть палитру команд.
2.  Введите **ADL: Register Assembly**. 
3.  Укажите локальный путь к сборке. 
4.  Выберите учетную запись Data Lake Analytics.
5.  Выберите базу данных.

Результат: портал откроется в браузере и будет показан процесс регистрации сборки.  

Еще один удобный способ запуска команды **ADL: Register Assembly** — щелкнуть правой кнопкой мыши DLL-файл в проводнике. 

**ADL: Register Assembly through Configuration**.
1.  Нажмите клавиши **CTRL + SHIFT + P**, чтобы открыть палитру команд.
2.  Введите **ADL: Register Assembly through Configuration**. 
3.  Укажите локальный путь к сборке. 
4.  Отобразится JSON-файл. Просмотрите и при необходимости измените зависимости сборки и параметры ресурсов. Инструкции приводятся в окне вывода. Сохраните (**CTRL+S**) JSON-файл, чтобы продолжить регистрацию сборки.

![Файл Code Behind в Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)
>[!NOTE]
>- Зависимости сборки: средства Azure Data Lake автоматически определяют наличие зависимостей в библиотеке DLL. После обнаружения зависимости автоматически отображаются в JSON-файле. 
>- Ресурсы: вы можете отправить ресурсы библиотеки DLL (например, файлы TXT, PNG, CSV и т. д.) в процессе регистрации сборки. 

Еще один более удобный способ запуска команды **ADL: Register Assembly through Configuration** — щелкнуть правой кнопкой мыши DLL-файл в проводнике. 

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


## <a name="access-data-lake-analytics-catalog"></a>Доступ к каталогу Data Lake Analytics

После подключения к Azure вы можете получить доступ к каталогу U-SQL, выполнив следующие действия:

**Доступ к метаданным Azure Data Lake Analytics**

1.  Нажмите клавиши **CTRL+SHIFT+P**, а затем введите **ADL: List Tables**.
2.  Щелкните одну из учетных записей Data Lake Analytics.
3.  Щелкните одну из баз данных Data Lake Analytics.
4.  Выберите одну из схем. Вы увидите таблицы.

## <a name="show-data-lake-analytics-jobs"></a>Отображение заданий Data Lake Analytics

Откройте палитру команд (**CTRL+SHIFT+P**) и выберите **ADL: Show Job**. 

1.  Выберите ADLA или локальную учетную запись. 
2.  Подождите, пока отобразится список заданий для учетной записи.
3.  Выберите задание в списке. Средства ADL отобразят сведения о задании на портале и откроют файл JobInfo в VSCode.

![Типы объектов IntelliSencse в средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="azure-data-lake-storage-adls-integration"></a>Интеграция с Azure Data Lake Store (ADLS)

Вы можете использовать связанные с ADLS команды для навигации по ресурсам ADLS, предварительного просмотра файла ADLS и отправки файла в ADLS непосредственно в VSCode.  
### <a name="list-storage-path"></a>Вывод пути к хранилищу

Откройте палитру команд (**CTRL+SHIFT+P**) и выберите **ADL: List Storage Path**.
1.  Откройте палитру команд и введите команду.

    ![Вывод пути к хранилищу в Средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-storage.png)

2.  Выберите предпочтительный способ вывода пути к хранилищу. В этом примере выбран вариант **Enter a path** (Введите путь).

    ![Выбор одного из способов вывода пути к хранилищу в Средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account-selectoneway.png)

    > [!NOTE]
    >- В VSCode для каждой учетной записи ADLA сохраняется последний путь, по которому вы переходили. Пример：/tt/ss.
    >- Browse from root path (Просмотр из корневого пути): вывод корневого пути из выбранной учетной записи ADLA или локальной учетной записи.
    >- Enter a path (Введите путь): вывод указанного пути из выбранной учетной записи ADLA или локальной учетной записи.
    
3. Выберите локальную учетную запись или учетную запись ADLA.

    ![Средства Data Lake для Visual Studio Code: выбор дополнительных учетных записей](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

4.  Выберите пункт "more" (дополнительно), чтобы вывести список дополнительных учетных записей ADLA и выбрать одну из них.

    ![Средства Data Lake для Visual Studio Code: выбор учетной записи](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-select-adla-account.png)

5.  Введите путь к хранилищу Azure. Пример: /output

       ![Ввод пути к хранилищу в Средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-input-path.png)

6.  Результат: в палитре команд выводятся сведения о пути в соответствии с входными данными.

    ![Результат вывода пути к хранилищу в Средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-path.png)

Еще один более удобный способ вывода относительного пути — открыть контекстное меню.

1.  Щелкните строку пути правой кнопкой мыши и выберите пункт "List Storage Path" (Вывод пути к хранилищу).

       ![Контекстное меню в Средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)
2. Выбранный относительный путь отобразится в палитре команд.

   ![Вывод выбранного пути в Средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-relative-path.png)

3.  Выберите локальную учетную запись или учетную запись ADLA.

       ![Средства Data Lake для Visual Studio Code: выбор учетной записи](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

4.  Результат: в палитре команд будет выведен список папок и файлов, находящихся по текущему пути.

       ![Вывод текущего пути в Средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-current.png)

### <a name="preview-storage-file"></a>Предварительный просмотр файла хранилища

Откройте палитру команд (**CTRL+SHIFT+P**) и выберите **ADL: : Preview Storage File**.
1.  Откройте палитру команд и введите команду.

       ![Предварительный просмотр файла хранилища в Средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-preview.png)

2.  Выберите локальную учетную запись или учетную запись ADLA.

       ![Список учетных данных в Средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

3.  Выберите пункт "more" (дополнительно), чтобы вывести список дополнительных учетных записей ADLA и выбрать одну из них.

       ![Средства Data Lake для Visual Studio Code: выбор учетной записи](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-select-adla-account.png)

4.  Введите путь к хранилищу или имя файла хранилища Azure. Пример: /output/SearchLog.txt

       ![Ввод пути к хранилищу и имени файла хранилища в Средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-input-preview-file.png)

5.  Результат: в палитре команд выводятся сведения о пути в соответствии с входными данными.

       ![Результат предварительного просмотра файла в Средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-preview-results.png)

Еще один более удобный способ предварительного просмотра файла — открыть контекстное меню.

1.  Для предварительного просмотра файла щелкните правой кнопкой мыши путь к файлу.

       ![Контекстное меню в Средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-preview.png)

2.  Выберите локальную учетную запись или учетную запись ADLA.

       ![Средства Data Lake для Visual Studio Code: выбор учетной записи](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

3.  Результат: в VSCode отобразятся результаты предварительного просмотра файла.

       ![Результат предварительного просмотра файла в Средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-preview-results.png)

### <a name="upload-file"></a>Отправка файла 

Отправить файл можно с помощью команды **ADL: Upload File** или **ADL: Upload File through Configuration**.

**ADL: Upload File**
1.  Нажмите клавиши CTRL+SHIFT+P, чтобы открыть палитру команд, или щелкните правой кнопкой мыши в редакторе скриптов, а затем введите **Upload File**.
2. Укажите локальный путь для отправки.

    ![Ввод локального пути в Средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-input-local-path.png)

3. Выберите один из способов для вывода пути к хранилищу. В этом примере выбран вариант **Enter a path** (Введите путь).

    ![Вывод пути к хранилищу в Средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account-selectoneway.png)
    >[!NOTE]
    >- В VSCode для каждой учетной записи ADLA сохраняется последний путь, по которому вы переходили. Пример：/tt/ss.
    >- Browse from root path (Просмотр из корневого пути): вывод корневого пути из выбранной учетной записи ADLA или локальной учетной записи.
    >- Enter a path (Введите путь): вывод указанного пути из выбранной учетной записи ADLA или локальной учетной записи.

4. Выберите локальную учетную запись или учетную запись ADLA.

    ![Контекстное меню хранилища в Средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

5.  Введите путь к хранилищу Azure. Пример: /output/

       ![Ввод пути к хранилищу в Средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-input-preview-file.png)

6. Будет выведен список содержимого, находящегося по пути к хранилищу Azure. Выберите пункт **Choose current folder** (Выбрать текущую папку).

    ![Выбор папки в Средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-choose-current-folder.png)

7.  Результат: в окне вывода отобразится состояние отправки файла.

       ![Состояние отправки в Средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-upload-status.png)    

**ADL: Upload File through Configuration**
1.  Нажмите клавиши CTRL+SHIFT+P, чтобы открыть палитру команд, или щелкните правой кнопкой мыши в редакторе скриптов, а затем введите **Upload File through Configuration**.
2.  В VSCode отобразится JSON-файл. Вы можете ввести сразу несколько путей к файлам, чтобы отправить несколько файлов. Инструкции приводятся в окне вывода. Сохраните (**CTRL+S**) JSON-файл, чтобы продолжить отправку файла.

       ![Ввод имени файла в Средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-upload-file.png)

3.  Результат: в окне вывода отобразится состояние отправки файла.

       ![Состояние отправки в Средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-upload-status.png)     

Еще один удобный способ отправки файла в хранилище — открыть контекстное меню полного или относительного пути к файлу в редакторе скриптов. Введите локальный путь к файлу и выберите учетную запись. В окне вывода отобразится состояние отправки. 

### <a name="open-web-azure-storage-explorer"></a>Открытие обозревателя веб-хранилищ Azure
Вы можете открыть **обозреватель веб-хранилищ Azure** с помощью команды **ADL: Open Web Azure Storage Explorer** или в контекстном меню.

1. Нажмите клавиши CTRL+SHIFT+P, чтобы открыть палитру команд.
2. Введите команду **Open Web Azure Storage Explorer** или щелкните правой кнопкой мыши относительный либо полный путь в редакторе скриптов и выберите пункт **Open Web Azure Storage Explorer** (Открыть обозреватель веб-хранилищ Azure).
3. Выберите учетную запись Data Lake Analytics.

Средства ADL откроют путь к хранилищу Azure на портале. Вы можете перейти по этому пути и просмотреть файл в Интернете.

### <a name="local-run-and-local-debug-windows-users"></a>Локальное выполнение и локальная отладка для пользователей Windows
Для тестирования локальных данных и проверки скрипта в локальной среде перед публикацией кода в ADLA было реализовано локальное выполнение U-SQL. Функция локальной отладки позволяет отлаживать код программной части C#, осуществлять пошаговое выполнение кода и проверять скрипт в локальной среде перед его отправкой в ADLA. Инструкции см. в статье [Локальное выполнение и локальная отладка U-SQL с помощью Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md).

## <a name="additional-features"></a>Дополнительные функции

Средства Data Lake для VSCode поддерживают следующие функции:

-   Автоматическое завершение IntelliSense. Предлагаются подсказки для ключевых слов, методов, переменных и т. д. Разные значки обозначают объекты разных типов:

    - тип данных Scala;
    - сложный тип данных;
    - встроенные пользовательские типы;
    - коллекции и классы .NET;
    - выражения C#;
    - встроенные функции и объекты C#, определяемые пользователем; 
    - функции U-SQL;
    - функции U-SQL для работы с окнами.
 
    ![Типы объектов IntelliSencse в средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)
 
-   Технология автозавершения IntelliSense для метаданных Data Lake Analytics. Средства Data Lake загружают метаданные Data Lake Analytics в локальное хранилище.  Функция IntelliSense автоматически заполняет объекты, включая базы данных, схемы, таблицы, представления, функции, возвращающие табличное значение, процедуры и сборки C# на основе метаданных Data Lake Analytics.
 
    ![Метаданные IntelliSense в средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

-   Маркер ошибки IntelliSense. Средства Data Lake подчеркивают ошибки ввода в файлах U-SQL и C#. 
-   Подсветка синтаксиса. Средства Data Lake выделяют разными цветами переменные, ключевые слова, типы данных, функции и т. д. 

    ![Подсветка синтаксиса в средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

## <a name="next-steps"></a>Дальнейшие действия

- Сведения о локальном выполнении и локальной отладке U-SQL с помощью Visual Studio Code см. в статье [Локальное выполнение и локальная отладка U-SQL с помощью Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md).
- Сведения для начала работы с Data Lake Analytics вы найдете в статье [Tutorial: get started with Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md) (Руководство. Начало работы с Azure Data Lake Analytics с помощью портала Azure).
- Дополнительные сведения об использовании средств Data Lake для U-SQL есть в статье [Учебник. Разработка скриптов U-SQL с помощью средств озера данных для Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Сведения о разработке сборок см. в статье [Разработка сборок U-SQL для заданий Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-assemblies.md).




