---
title: "Отладка заданий U-SQL | Документация Майкрософт"
description: "Узнайте, как выполнять отладку заданий U-SQL, завершившихся сбоем, с помощью Visual Studio. "
services: data-lake-analytics
documentationcenter: 
author: yanancai
manager: jhubbard
editor: cgronlun
ms.assetid: bcd0b01e-1755-4112-8e8a-a5cabdca4df2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/02/2016
ms.author: yanacai
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: aac455520ab62d69b406a254a54b0f000ea2e5bc
ms.contentlocale: ru-ru
ms.lasthandoff: 06/26/2017


---
<a id="debug-user-defined-c-code-for-failed-u-sql-jobs" class="xliff"></a>

# Отладка определяемого пользователем кода C# для заданий U-SQL, завершившихся сбоем

В U-SQL реализована возможность расширения кода с использованием C#. Пользователь может создать собственный код на языке C#, например определяемые модули извлечения или уменьшения, чтобы расширить доступные возможности (см. дополнительные сведения о [пользовательском коде в U-SQL](https://docs.microsoft.com/en-us/azure/data-lake-analytics/data-lake-analytics-u-sql-programmability-guide#user-defined-functions---udf)). Но разработка без ошибок невозможна, а отладка в системах больших данных затруднена, так как многие системы предоставляют достаточно ограниченный набор отладочных сведений о среде, таких как журналы и т. д. 

Средства ADL для Visual Studio включают функцию под названием **отладка вершин с ошибками**. Эта функция позволяет легко клонировать среду, в которой произошел сбой (включая промежуточные входные данные, пользовательский код и т. п.), из облака на локальный компьютер. Это позволяет выполнять трассировку и отладку проблемного задания с теми же условиями среды выполнения и теми же входными данными, которые существовали в облаке.

Следующее видео демонстрирует использование **отладки вершины с ошибками** в средствах ADL для Visual Studio.

> [!VIDEO https://e0d1.wpc.azureedge.net/80E0D1/OfficeMixProdMediaBlobStorage/asset-d3aeab42-6149-4ecc-b044-aa624901ab32/b0fc0373c8f94f1bb8cd39da1310adb8.mp4?sv=2012-02-12&sr=c&si=a91fad76-cfdd-4513-9668-483de39e739c&sig=K%2FR%2FdnIi9S6P%2FBlB3iLAEV5pYu6OJFBDlQy%2FQtZ7E7M%3D&se=2116-07-19T09:27:30Z&rscd=attachment%3B%20filename%3DDebugyourcustomcodeinUSQLADLA.mp4]
>

> [!NOTE]
> Visual Studio может зависнуть или аварийно завершить работу, если у вас не установлены следующие два обновления для Windows: [Распространяемый компонент Microsoft Visual C++ 2015, обновление 2](https://www.microsoft.com/download/details.aspx?id=51682) и [Универсальная среда выполнения C для Windows](https://www.microsoft.com/download/details.aspx?id=50410&wa=wsignin1.0).
> 

<a id="download-failed-vertex-to-local" class="xliff"></a>

## Загрузка в локальную среду вершины, в которой произошел сбой

Открывая невыполненное задание в средствах ADL для Visual Studio, вы получите оповещение. Подробные сведения об ошибке отобразятся на вкладке "Ошибка" и в желтой области оповещений в верхней части окна.

1. Нажмите кнопку **Скачать** , чтобы скачать все необходимые ресурсы и входные потоки. Нажмите кнопку **Повторить** , если не удалось скачать. 

2. Когда загрузка завершится, щелкните **Открыть** для создания локальной среды отладки. Будет создан и автоматически открыт новый экземпляр Visual Studio с решением для отладки. 

3. Действия по отладке немного различаются для невыполненных заданий с кодом программной части и сборками.

    - [Отладка невыполненного задания с кодом программной части](#debug-job-failed-with-code-behind)
    - [Отладка невыполненного задания со сборками](#debug-job-failed-with-assemblies)

![Azure Data Lake Analytics U-SQL отладка visual studio скачивание вершины](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

<a id="debug-job-failed-with-code-behind" class="xliff"></a>

## Отладка невыполненного задания с кодом программной части

Если вы сохранили пользовательский код в файле кода программной части (в проектах U-SQL этот файл обычно имеет имя "Script.usql.cs") и после этого задание U-SQL завершается ошибкой, исходный код будет автоматически импортирован в решение для отладки. Для устранения неполадок вы можете просто использовать все возможности Visual Studio для отладки (контрольные значения, переменные, и т. д.). 

Перед отладкой обязательно проверьте **исключения среды CLR** в окне параметров исключений (клавиши **Ctrl+Alt+E**).

![Azure Data Lake Analytics U-SQL отладка visual studio настройка](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)

1. Нажмите клавишу **F5**. Код программной части будет автоматически выполняться, пока не возникнет исключение.

2. Откройте в проекте файл **ADLTool_Codebehind.usql.cs**, задайте точки останова, затем последовательно нажимайте клавишу F5 для пошаговой отладки кода.

    ![Исключение при отладке U-SQL в Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-exception.png)

<a id="debug-job-failed-with-assemblies" class="xliff"></a>

## Отладка невыполненного задания со сборками

Если в скрипте U-SQL вы зарегистрировали сборки, система не сможет автоматически получить исходный код. В этом случае перед отладкой пользовательского кода необходимо изменить некоторые настройки, а именно — добавить исходный код сборок в автоматически созданное решение.

<a id="configure-the-solution" class="xliff"></a>

### Настройка решения

1. Щелкните правой кнопкой мыши **Решение "VertexDebug"** > **Добавить** > **Существующий проект**, чтобы увидеть исходный код сборок и добавить проект в решение для отладки.

    ![Добавление проекта при отладке U-SQL в Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-add-project-to-debug-solution.png)

2. В этом решении щелкните правой кнопкой мыши **LocalVertexHost** > **Свойства**, а затем скопируйте путь из параметра **Рабочий каталог**.

3. Щелкните правой кнопкой мыши **Проект исходного кода сборок** > **Свойства**, выберите слева вкладку **Сборка** и вставьте путь, скопированный на шаге 2, в параметр **Вывод** > **Выходной путь**.  

    ![Настройка пути pdb при отладке U-SQL в Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-set-pdb-path.png)

4. Нажмите клавиши **Ctrl+Alt+E** и проверьте **Исключения среды CLR** в окне параметров исключений.

<a id="start-debug" class="xliff"></a>

### Запуск отладки

1. Щелкните правой кнопкой мыши **Проект исходного кода сборок** > **Перестроить**, чтобы сохранить PDB-файлы в рабочий каталог LocalVertexHost.

2. Нажмите клавишу **F5**. Проект будет автоматически выполняться, пока не возникнет исключение. При первом запуске может отобразиться следующее сообщение, которое можно игнорировать. Прежде чем откроется окно отладки, возможно, потребуется подождать до одной минуты.

    ![Azure Data Lake Analytics U-SQL отладка visual studio предупреждение](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-visual-studio-u-sql-debug-warning.png)

3. Откройте исходный код и задайте точки останова, затем нажмите клавишу **F5** для пошаговой отладки кода.

Для устранения проблемы можно использовать и другие средства отладки, доступные в Visual Studio (контрольные значения, переменные, и т. д.). 

**Обратите внимание, что** проект исходного кода сборок необходимо перестраивать после каждого изменения кода, чтобы использовать новые PDB-файлы.

Когда отладка будет завершена, в окне вывода отобразится следующее сообщение:

    The Program ‘LocalVertexHost.exe’ has exited with code 0 (0x0).

![Успешное завершение отладки U-SQL в Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-succeed.png)

<a id="resubmit-the-job" class="xliff"></a>

## Повторная отправка задания
После завершения отладки можно повторно отправить невыполненное задание.

1. Зарегистрируйте новые DLL-сборки в базе данных ADLA.

    1. В обозревателе серверов или в Cloud Explorer разверните узел **Учетная запись ADLA** > **Базы данных**.
    2. Щелкните правой кнопкой мыши пункт **Сборки**, чтобы зарегистрировать сборки. 
    3. Зарегистрируйте свои новые DLL-сборки в базе данных ADLA.
    ![Регистрация сборки при отладке U-SQL в Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-register-assembly.png)
2. Или скопируйте код C# обратно в файл кода программной части C# (Script.usql.cs).
3. Повторно отправьте задание.

<a id="next-steps" class="xliff"></a>

## Дальнейшие действия

* [Руководство по программированию U-SQL](data-lake-analytics-u-sql-programmability-guide.md)
* [Разработка определяемых пользователем операторов U-SQL для заданий аналитики озера данных Azure](data-lake-analytics-u-sql-develop-user-defined-operators.md)
* [Учебник. Разработка скриптов U-SQL с помощью средств озера данных для Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)



