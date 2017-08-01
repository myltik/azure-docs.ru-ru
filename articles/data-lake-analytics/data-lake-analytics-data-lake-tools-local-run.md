---
title: "Тестирование и отладка заданий U-SQL с помощью локального выполнения и пакета SDK U-SQL для Azure Data Lake | Документация Майкрософт"
description: "Сведения о том, как с помощью средств Azure Data Lake для Visual Studio и пакета SDK U-SQL для Azure Data Lake выполнять тестирование и отладку заданий U-SQL на локальной рабочей станции."
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/15/2016
ms.author: yanacai
ms.translationtype: Human Translation
ms.sourcegitcommit: a1ba750d2be1969bfcd4085a24b0469f72a357ad
ms.openlocfilehash: 771a96df5cc66bac46e7144785be8cc072b57b31
ms.contentlocale: ru-ru
ms.lasthandoff: 06/20/2017


---
# <a name="test-and-debug-u-sql-jobs-by-using-local-run-and-the-azure-data-lake-u-sql-sdk"></a>Тестирование и отладка заданий U-SQL с помощью локального выполнения и пакета SDK U-SQL для Azure Data Lake

Вы можете использовать средства Azure Data Lake для Visual Studio и пакет SDK U-SQL для Azure Data Lake, чтобы запускать задания U-SQL на рабочей станции так же, как в службе Azure Data Lake. Оба этих компонента для локального выполнения помогут вам быстрее выполнять тестирование и отладку заданий U-SQL.

## <a name="understand-the-data-root-folder-and-the-file-path"></a>Сведения о корневой папке данных и пути к файлу

Для локального выполнения и использования пакетов SDK U-SQL вам потребуется настроить корневую папку данных. Корневая папка данных служит "локальным хранилищем" для локальной учетной записи среды вычислений. Она действует так же, как учетная запись Azure Data Lake Store в учетной записи Data Lake Analytics. Переход на другую корневую папку данных аналогичен переходу на другую учетную запись хранения. Если вам нужен доступ к данным, которые совместно используются различными корневыми папками данных, следует использовать в скриптах абсолютные пути или создать в корневой папке данных символические ссылки файловой системы (например, с помощью команды **mklink** для файловой системы NTFS), указывающие на совместно используемые данные.

Корневая папка данных используется в следующих целях.

- Хранение метаданных, включая базы данных, таблицы, функции с табличным значением (TVF) и сборки.
- Поиск путей ввода-вывода, которые определяются как относительные пути в U-SQL. Использование относительных путей упрощает развертывание проектов U-SQL в Azure.

В скриптах U-SQL можно использовать как относительные, так и абсолютные локальные пути. Относительный путь задается относительно пути к выбранной корневой папке данных. Мы советуем использовать в качестве разделителя пути символ "/", чтобы обеспечить совместимость скриптов с выполнением на стороне сервера. Ниже приведены некоторые примеры относительных путей и их абсолютные эквиваленты. В этих примерах предполагается, что мы используем корневую папку данных C:\LocalRunDataRoot.

|Относительный путь|Абсолютный путь|
|-------------|-------------|
|/abc/def/input.csv |C:\LocalRunDataRoot\abc\def\input.csv|
|abc/def/input.csv  |C:\LocalRunDataRoot\abc\def\input.csv|
|D:/abc/def/input.csv |D:\abc\def\input.csv|

## <a name="use-local-run-from-visual-studio"></a>Локальное выполнение из Visual Studio

Средства Data Lake для Visual Studio предоставляют возможность локального выполнения U-SQL в Visual Studio. Это позволит вам:

- локально запускать скрипты U-SQL вместе со сборками C#;
- локально выполнять отладку сборок C#;
- создавать, просматривать и удалять каталоги U-SQL (локальные базы данных, сборки, схемы и таблицы) с помощью обозревателя сервера. Локальный каталог также можно найти с помощью обозревателя сервера.

    ![Локальный каталог для локального выполнения средств Data Lake для Visual Studio](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-local-catalog.png)

Установщик средств Data Lake создает папку C:\LocalRunRoot, которая по умолчанию используется в качестве корневой папки данных. По умолчанию для локального выполнения используется коэффициент параллелизма 1.

### <a name="to-configure-local-run-in-visual-studio"></a>Настройка локального выполнения в Visual Studio

1. Откройте Visual Studio.
2. Откройте **обозреватель сервера**.
3. Разверните узлы **Azure** > **Data Lake Analytics**.
4. Откройте меню **Data Lake** и выберите пункт **Параметры и настройки**.
5. В дереве слева разверните узел **Azure Data Lake**, а затем — **Общие**.

    ![Настройка параметров для локального выполнения средств Data Lake для Visual Studio](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-configure.png)

Для локального выполнения требуется создать проект U-SQL для Visual Studio. Эта часть выполняется не так, как при запуске скриптов U-SQL в Azure.

### <a name="to-run-a-u-sql-script-locally"></a>Локальное выполнение скрипта U-SQL
1. В Visual Studio откройте нужный проект U-SQL.   
2. В обозревателе решений щелкните правой кнопкой мыши скрипт U-SQL и выберите команду **Submit Script** (Отправить скрипт).
3. Выберите **(Local)** в качестве учетной записи аналитики для локального выполнения скрипта.
Можно также щелкнуть учетную запись **(Local)** в верхней части окна скрипта, а затем нажать кнопку **Отправить** (или использовать клавиши CTRL+F5).

    ![Отправка заданий для локального выполнения средств Data Lake для Visual Studio](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-submit-job.png)

### <a name="debug-scripts-and-c-assemblies-locally"></a>Локальная отладка скриптов и сборок C#

Можно выполнять отладку сборок C# без их отправки и регистрации в службе Azure Data Lake Analytics. Точки останова можно установить в файле кода и в ссылочном проекте C#.

#### <a name="to-debug-local-code-in-code-behind-file"></a>Отладка локального кода в файле кода

1. Установите точки останова в файле кода.
2. Нажмите клавишу F5 для запуска локальной отладки сценария.

> [!NOTE]
   > Следующая процедура работает только в Visual Studio 2015. В более ранних версиях Visual Studio необходимо вручную добавить PDB-файлы.  
   >
   >

#### <a name="to-debug-local-code-in-a-referenced-c-project"></a>Отладка локального кода в указанном проекте C#

1. Создайте проект сборки C# и постройте его для создания выходной библиотеки DLL.
2. Зарегистрируйте библиотеку DLL с помощью инструкции U-SQL:

        CREATE ASSEMBLY assemblyname FROM @"..\..\path\to\output\.dll";
        
3. Установите точки останова в коде C#.
4. Нажмите клавишу F5 для локальной отладки сценария со ссылками на библиотеку DLL C#.

## <a name="use-local-run-from-the-data-lake-u-sql-sdk"></a>Использование локального выполнения из пакета SDK U-SQL для Data Lake

Для локального выполнения скриптов U-SQL можно использовать не только Visual Studio, но и пакет SDK U-SQL для Azure Data Lake. В последнем случае используется интерфейс командной строки или программные интерфейсы. Это позволяет масштабировать сценарии локального тестирования U-SQL.

Узнайте больше о [пакете SDK Azure Data Lake для U-SQL](data-lake-analytics-u-sql-sdk.md).


## <a name="next-steps"></a>Дальнейшие действия

* Более сложный запрос см. в руководстве [Анализ журналов веб-сайта с помощью службы Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Для просмотра сведений о заданиях см. статью [Использование браузера и представления для заданий Azure Data Lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md).
* Дополнительные сведения см. в статье [Использование представления выполнения вершин в инструментах Data Lake для Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).

