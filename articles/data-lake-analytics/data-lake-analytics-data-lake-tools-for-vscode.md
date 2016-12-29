---
title: "Использование средств Azure Data Lake для Visual Studio Code | Документация Майкрософт"
description: "Узнайте, как с помощью средств Azure Data Lake для Visual Studio Code создавать, тестировать и выполнять скрипты U-SQL. "
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/22/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: fe5ef9bba31abdf9b29ad7c817a376407309f289
ms.openlocfilehash: 59f2e4473aac85a8476055d2d955f576c099d787

---

# <a name="use-the-azure-data-lake-tools-for-visual-studio-code"></a>Использование средств Azure Data Lake для Visual Studio Code

Узнайте, как с помощью средств Azure Data Lake для Visual Studio Code (VSCode) создавать, тестировать и выполнять скрипты U-SQL.


## <a name="prerequisites"></a>Предварительные требования

Средства Data Lake можно устанавливать на всех платформах, поддерживаемых VSCode, то есть на Windows, Linux и MacOS. Здесь описаны предварительные условия для различных платформ.

- Windows

    - [Visual Studio Code]( https://www.visualstudio.com/products/code-vs.aspx).
    - [Среда выполнения Java SE, версия 8 с обновлением 77 или более поздние выпуски](https://java.com/download/manual.jsp). Необходимо добавить путь к файлу java.exe в переменную окружения PATH.  Этот процесс описан в [инструкции по установке и изменению переменной окружения PATH]( https://www.java.com/download/help/path.xml). Обычно этот путь имеет формат C:\Program Files\Java\jdk1.8.0_77\jre\bin
    - [Пакет SDK для .NET Core 1.0.1-preview 2 или среда выполнения .NET Core 1.0.1]( https://www.microsoft.com/net/download).
    
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
            > Mono 4.6 не поддерживается.  Необходимо полностью удалить версию 4.6, прежде чем устанавливать 4.2.x.  

        - [Среда выполнения Java SE, версия 8 с обновлением 77 или более поздние выпуски](https://java.com/download/manual.jsp). Инструкции доступны [здесь]( https://java.com/en/download/help/linux_x64_install.xml).
        - [Пакет SDK для .NET Core 1.0.1-preview 2 или среда выполнения .NET Core 1.0.1]( https://www.microsoft.com/net/download).
- MacOS

    - [Visual Studio Code]( https://www.visualstudio.com/products/code-vs.aspx).
    - [Mono 4.2.4](http://download.mono-project.com/archive/4.2.4/macos-10-x86/). 
    - [Среда выполнения Java SE, версия 8 с обновлением 77 или более поздние выпуски](https://java.com/download/manual.jsp). Инструкции доступны [здесь](https://java.com/en/download/help/mac_install.xml).
    - [Пакет SDK для .NET Core 1.0.1-preview 2 или среда выполнения .NET Core 1.0.1]( https://www.microsoft.com/net/download).

## <a name="install-the-data-lake-tools"></a>Установка средств Data Lake

После установки всех необходимых компонентов можно переходить к установке средств Data Lake для VSCode.

**Установка средств Data Lake**

1. Откройте **Visual Studio Code**.
2. Нажмите клавиши **CTRL + P**, а затем введите:

        ext install usql-vscode-ext
    Вы увидите список расширений для Visual Studio Code. Среди них есть и **средство Azure Data Lake (предварительная версия)**.
3. Щелкните **Установить** рядом с элементом **Средство Azure Data Lake (предварительная версия)**. Через несколько секунд вместо кнопки "Установить" появится новая кнопка "Перезагрузить".
4. Щелкните **Перезагрузить**, чтобы активировать расширение.
5. Нажмите кнопку **ОК** для подтверждения. Средства Azure Data Lake теперь появятся в области "Расширения".

    ![Установка инструментов Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)


## <a name="connect-to-azure"></a>Подключение к Azure

Прежде чем компилировать и выполнять скрипты U-SQL, необходимо подключиться к учетной записи Azure.

**Подключение к Azure**

1.  Откройте палитру команд, нажав клавиши **CTRL + SHIFT + P**. 
2.  Введите текст **ADL:Login**.

    ![Палитра команд средств Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)

2.  Следуйте инструкциям, представленным на странице входа. После подключения в строке состояния в нижней части окна будет отображаться имя учетной записи.

> [!NOTE] 
> Если для учетной записи используется двухфакторная проверка подлинности, мы рекомендуем выбрать проверку подлинности через телефон, а не PIN-код.

Чтобы выйти из учетной записи, используйте команду **ADL:Logout**.

## <a name="list-data-lake-analytics-accounts"></a>Список учетных записей аналитики озера данных

Чтобы проверить подключение, попробуйте получить список учетных записей Data Lake Analytics:

**Получение списка учетных записей Data Lake Analytics в подписке Azure**

1. Откройте палитру команд, нажав клавиши **CTRL + SHIFT + P**.
2. Наберите **ADL:List Accounts**.  Учетные записи отображаются в области **Вывода**.

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
    - Необязательные параметры:

        - Priority: значение приоритета в диапазоне от 1 до 1000, где 1 — наивысший приоритет. По умолчанию используется значение 1000.
        - Parallelism: коэффициент параллелизма в диапазоне от 1 до 150. По умолчанию используется значение 150. 

        > [!NOTE] 
        > Если вы введете недопустимые значения, используются значения по умолчанию.

     ![Файл конфигурации средств Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-configuration-file.png)

    Для компиляции и запуска задания U-SQL вам потребуется учетная запись вычислений Data Lake Analytics.  Настройте учетную запись вычислений, прежде чем компилировать и запускать задания U-SQL.

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

Файл Code Behind представляет собой программу C#, связанную с одним из скриптов U-SQL. В файл Code Behind можно включить определяемые пользователем операторы, статистические функции, типы данных и функции для вашего скрипта. Все эти объекты можно будет напрямую использовать в скрипте, не регистрируя для них сборку. Файл Code Behind помещается в ту же папку, что и связанный с ним файл скрипта U-SQL. Например, для скрипта с именем xxx.usql, файл Code Behind будет иметь имя xxx.usql.cs. Если вручную удалить файл Code Behind, эта функция для связанного скрипта U-SQL будет отключена. Дополнительные сведения о написании пользовательского кода для скриптов U-SQL можно найти в записи блога [Writing and Using Custom Code in U-SQL – User-Defined Functions]( https://blogs.msdn.microsoft.com/visualstudio/2015/10/28/writing-and-using-custom-code-in-u-sql-user-defined-functions/) (Написание и использование пользовательского кода в SQL-U — определяемые пользователем функции).

Чтобы использовать возможности Code Behind, необходимо открыть рабочую папку. 

**Создание файла Code Behind**

1. Откройте файл исходного кода. 
2. Откройте палитру команд, нажав клавиши **CTRL + SHIFT + P**.
3. Введите **ADL: Generate Code Behind**.  Файл Code Behind будет создан в той же папке. 

Чтобы создать файл Code Behind, можно также щелкнуть правой кнопкой файл скрипта и нажать кнопку **ADL: Generate Code Behind**. 

Компиляция и отправка скрипта U-SQL с файлом Code Behind выполняется так же, как и для автономного скрипта U-SQL.

Следующие два снимка экрана демонстрируют файл Code Behind и связанный с ним файл скрипта U-SQL.
 
![Файл Code Behind в Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-behind.png)

![Файл Code Behind в Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-behind-call.png) 

## <a name="register-assemblies"></a>Регистрация сборок

С помощью средства Data Lake можно регистрировать сборки пользовательского кода в хранилище метаданных Data Lake Analytics.

**Регистрация сборки**

1.  Нажмите клавиши **CTRL + SHIFT + P**, чтобы открыть палитру команд.
2.  Введите **ADL:Register Assembly**.
3.  Выберите учетную запись Data Lake Analytics.
4.  Выберите базу данных.
5.  Укажите локальный путь к сборке.

## <a name="access-data-lake-analytics-metadata"></a>Доступ к метаданным Data Lake Analytics

После подключения к Azure вы можете получить доступ к метаданным U-SQL, используя следующие действия:

**Доступ к метаданным U-SQL**

1.  Нажмите клавиши **CTRL + SHIFT + P**, а затем введите **ADL:List Tables**.
2.  Щелкните одну из учетных записей Data Lake Analytics.
3.  Щелкните одну из баз данных Data Lake Analytics.
4.  Выберите одну из схем. Вы увидите таблицы.

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

##<a name="next-steps"></a>Дальнейшие действия:

- Сведения для начала работы с Data Lake Analytics вы найдете в статье [Tutorial: get started with Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md) (Руководство. Начало работы с Azure Data Lake Analytics с помощью портала Azure).
- Дополнительные сведения об использовании средств Data Lake для U-SQL есть в статье [Учебник. Разработка скриптов U-SQL с помощью средств озера данных для Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).






<!--HONumber=Nov16_HO4-->


