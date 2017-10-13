---
title: "Отладка заданий U-SQL | Документация Майкрософт"
description: "Узнайте, как выполнять отладку заданий U-SQL, завершившихся сбоем, с помощью Visual Studio."
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: jhubbard
editor: cgronlun
ms.assetid: bcd0b01e-1755-4112-8e8a-a5cabdca4df2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/02/2016
ms.author: saveenr
ms.openlocfilehash: 2a77c72d3062272305208934d6406d040266c753
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="debug-user-defined-c-code-for-failed-u-sql-jobs"></a>Отладка определяемого пользователем кода C# для заданий U-SQL, завершившихся сбоем

В U-SQL реализована возможность расширения кода с использованием C#. Пользователь может создать собственный код на языке C#, например определяемые модули извлечения или уменьшения, чтобы расширить доступные возможности. Дополнительные сведения см. в [руководстве по программированию U-SQL](https://docs.microsoft.com/en-us/azure/data-lake-analytics/data-lake-analytics-u-sql-programmability-guide#use-user-defined-functions-udf). Но, как показывает практика, разработка без ошибок невозможна, а отладка в системах больших данных затруднена, так как многие системы предоставляют достаточно ограниченный набор отладочных сведений о среде, таких как журналы.

Средства Azure Data Lake для Visual Studio включают функцию под названием **отладка вершины с ошибками**. Эта функция позволяет легко клонировать среду, в которой произошел сбой из облака на локальный компьютер. В локальном клоне содержится вся облачная среда, включая все входные данные и пользовательский код.

Следующее видео демонстрирует использование отладки вершины с ошибками в средствах Azure Data Lake для Visual Studio.

> [!VIDEO https://e0d1.wpc.azureedge.net/80E0D1/OfficeMixProdMediaBlobStorage/asset-d3aeab42-6149-4ecc-b044-aa624901ab32/b0fc0373c8f94f1bb8cd39da1310adb8.mp4?sv=2012-02-12&sr=c&si=a91fad76-cfdd-4513-9668-483de39e739c&sig=K%2FR%2FdnIi9S6P%2FBlB3iLAEV5pYu6OJFBDlQy%2FQtZ7E7M%3D&se=2116-07-19T09:27:30Z&rscd=attachment%3B%20filename%3DDebugyourcustomcodeinUSQLADLA.mp4]
>

> [!NOTE]
> Для Visual Studio необходимо установить следующие два обновления для Windows: [Распространяемый компонент Microsoft Visual C++ 2015, обновление 3](https://www.microsoft.com/en-us/download/details.aspx?id=53840) и [Универсальная среда выполнения C для Windows](https://www.microsoft.com/download/details.aspx?id=50410).

## <a name="download-failed-vertex-to-local-machine"></a>Загрузка на локальный компьютер вершины, в которой произошел сбой

Открывая невыполненное задание в средствах Azure Data Lake для Visual Studio, вы получите оповещение в желтой области оповещений; подробные сведения об ошибке также отобразятся на вкладке "Ошибка".

1. Нажмите кнопку **Скачать** , чтобы скачать все необходимые ресурсы и входные потоки. Нажмите кнопку **Повторить**, если не удалось скачать.

2. Когда загрузка завершится, щелкните **Открыть** для создания локальной среды отладки. Будет создан и автоматически открыт новый экземпляр Visual Studio с решением для отладки.

![Azure Data Lake Analytics U-SQL отладка visual studio скачивание вершины](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

Задания могут содержать файлы исходного кода или зарегистрированные сборки, и действия по отладке для этих двух типов различаются.

- [Отладка невыполненного задания с кодом программной части](#debug-job-failed-with-code-behind)
- [Отладка невыполненного задания со сборками](#debug-job-failed-with-assemblies)


## <a name="debug-job-failed-with-code-behind"></a>Отладка невыполненного задания с кодом программной части

Если вы сохранили пользовательский код в файле кода программной части (в проектах U-SQL этот файл обычно имеет имя "`Script.usql.cs`") и после этого задание U-SQL завершается ошибкой, исходный код импортируется в решение для отладки.  Здесь для устранения неполадок можно просто использовать все возможности Visual Studio для отладки (контрольные значения, переменные и т. д.).

> [!NOTE]
> Перед отладкой обязательно проверьте **исключения среды CLR** в окне параметров исключений (клавиши **CTRL+ALT+E**).

![Azure Data Lake Analytics U-SQL отладка visual studio настройка](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)

1. Нажмите клавишу **F5** для запуска кода программной части. Он будет выполняться до тех пор, пока не возникнет исключение.

2. Откройте файл `ADLTool_Codebehind.usql.cs` и задайте точки останова, затем нажмите клавишу **F5** для пошаговой отладки кода.

    ![Исключение при отладке U-SQL в Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-exception.png)

## <a name="debug-job-failed-with-assemblies"></a>Отладка невыполненного задания со сборками

Если в скрипте U-SQL вы зарегистрировали сборки, система не сможет автоматически получить исходный код. В этом случае вручную добавьте файлы исходного кода сборок в созданное решение.

### <a name="configure-the-solution"></a>Настройка решения

1. Щелкните правой кнопкой мыши **Решение "VertexDebug" > Добавить > Существующий проект**, чтобы увидеть исходный код сборок и добавить проект в решение для отладки.

    ![Добавление проекта при отладке U-SQL в Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-add-project-to-debug-solution.png)

2. В этом решении щелкните правой кнопкой мыши **LocalVertexHost > Свойства**, а затем скопируйте путь из параметра **Рабочий каталог**.

3. Щелкните правой кнопкой мыши **Проект исходного кода сборок > Свойства**, выберите слева вкладку **Сборка** и вставьте путь, скопированный на шаге 2, в параметр **Вывод > Выходной путь**.

    ![Настройка пути pdb при отладке U-SQL в Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-set-pdb-path.png)

4. Нажмите клавиши **Ctrl+Alt+E** и проверьте **Исключения среды CLR** в окне параметров исключений.

### <a name="start-debug"></a>Запуск отладки

1. Щелкните правой кнопкой мыши **Проект исходного кода сборок > Перестроить**, чтобы сохранить PDB-файлы в рабочий каталог `LocalVertexHost`.

2. Нажмите клавишу **F5**. Проект будет выполняться, пока не возникнет исключение. При первом запуске может отобразиться следующее предупреждение, которое можно игнорировать. Прежде чем откроется окно отладки, возможно, потребуется подождать до одной минуты.

    ![Azure Data Lake Analytics U-SQL отладка visual studio предупреждение](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-visual-studio-u-sql-debug-warning.png)

3. Откройте исходный код и задайте точки останова, затем нажмите клавишу **F5** для пошаговой отладки кода.

Для устранения неполадок можно просто использовать все возможности Visual Studio для отладки (контрольные значения, переменные и т. д.).

> [!NOTE]
> Проект исходного кода сборок необходимо перестраивать после каждого изменения кода, чтобы использовать новые PDB-файлы.

Когда отладка будет завершена, после успешного выполнения проекта в окне вывода отобразится следующее сообщение:

```
The Program 'LocalVertexHost.exe' has exited with code 0 (0x0).
```

![Успешное завершение отладки U-SQL в Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-succeed.png)

## <a name="resubmit-the-job"></a>Повторная отправка задания

После завершения отладки можно повторно отправить невыполненное задание.

1. Для заданий с кодом программной части скопируйте код C# в файл с кодом программной части (обычно `Script.usql.cs`).
2. Для заданий со сборками зарегистрируйте обновленные DLL-сборки в базе данных ADLA:
    1. В обозревателе серверов или в Cloud Explorer разверните узел **Учетная запись ADLA > Базы данных**.
    2. Щелкните правой кнопкой мыши пункт **Сборки**, чтобы зарегистрировать DLL-сборки: ![Регистрация сборки при отладке U-SQL в Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-register-assembly.png)
3. Повторно отправьте задание.

## <a name="next-steps"></a>Дальнейшие действия

- [Руководство по программированию U-SQL](data-lake-analytics-u-sql-programmability-guide.md)
- [Разработка определяемых пользователем операторов U-SQL для заданий аналитики озера данных Azure](data-lake-analytics-u-sql-develop-user-defined-operators.md)
- [Учебник. Разработка скриптов U-SQL с помощью средств озера данных для Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
