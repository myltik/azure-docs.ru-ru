---
title: "Разработка сборок U-SQL для заданий Azure Data Lake Analytics | Документация Майкрософт"
description: "Узнайте, как разрабатывать сборки (с возможностью повторного использования) для заданий Data Lake Analytics. "
services: data-lake-analytics
documentationcenter: 
author: jejiang
manager: jhubbard
editor: cgronlun
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/30/2016
ms.author: jejiang
ms.openlocfilehash: c49f80f8dcd330d7f46726241e7178351b9cc28f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="develop-u-sql-assemblies-for-azure-data-lake-analytics-jobs"></a>Разработка сборок U-SQL для заданий Azure Data Lake Analytics
Узнайте, как преобразовывать код программной части в сборки (с возможностью повторного использования) для заданий Data Lake Analytics. 

U-SQL позволяет с легкостью добавлять собственный пользовательский код на языках .Net, таких как C#, VB.Net или F#. Можно даже развернуть собственную среду выполнения для поддержки других языков.

Проще всего использовать собственный код с помощью средств Data Lake для работы с кодом программной части в Visual Studio. Дополнительные сведения см. в статье [Учебник. Разработка скриптов U-SQL с помощью средств озера данных для Visual Studio](data-lake-analytics-data-lake-tools-get-started.md). Использование кода программной части имеет несколько недостатков:

- Исходный код загружается заново при каждой передаче сценария.
- Код программной части не может использоваться совместно с другими заданиями.

Чтобы устранить эти недостатки, код программной части можно преобразовать в сборки и зарегистрировать их в каталоге Data Lake Analytics.

## <a name="prerequisites"></a>предварительным требованиям
* Visual Studio 2017, Visual Studio 2015, Visual Studio 2013 с обновлением 4 или Visual Studio 2012 с установленным Visual C++.
* Пакет SDK Microsoft Azure для .NET (версии 2.5 или выше).  Установите эти средства с помощью установщика веб-платформы или установщика Visual Studio.
* Учетная запись аналитики озера данных.  См. статью [Руководство. Начало работы с Azure Data Lake Analytics с помощью портала Azure](data-lake-analytics-get-started-portal.md).
* Пройдите руководство [Приступая к работе с U-SQL Studio для аналитики озера данных Azure](data-lake-analytics-u-sql-get-started.md) .
* Подключение к Azure.
* Загрузите исходные данные. Ознакомьтесь с руководством [Приступая к работе с U-SQL Studio для аналитики озера данных Azure](data-lake-analytics-u-sql-get-started.md). 

## <a name="develop-assemblies-for-u-sql"></a>Разработка сборок для U-SQL

**Создание и отправка задания U-SQL**

1. В меню **Файл** выберите команду **Создать**, а затем — **Проект**.
2. Разверните узел **Установлено**, **Шаблоны**, **Azure Data Lake**, **U-SQL(ADLA)**, выберите шаблон **Class Library (For U-SQL Application)** (Библиотека классов (для приложения U-SQL)) и нажмите кнопку **ОК**.
3. Запишите свой код в файл Class1.cs.  Ниже приведен пример кода.

        using Microsoft.Analytics.Interfaces;

        namespace USQLApplication_codebehind
        {
            [SqlUserDefinedProcessor]
            public class MyProcessor : IProcessor
            {
                public override IRow Process(IRow input, IUpdatableRow output)
                {
                    output.Set(0, input.Get<string>(0));
                    output.Set(0, input.Get<string>(0));
                    return output.AsReadOnly();
                }
            }
        }
4. Щелкните меню **Сборка** и выберите **Собрать решение**, чтобы создать DLL-файл.

## <a name="register-assemblies"></a>Регистрация сборок

Ознакомьтесь с разделом [Использование каталога U-SQL Azure Data Lake Analytics](data-lake-analytics-use-u-sql-catalog.md).


## <a name="use-the-assemblies"></a>Использование сборок

Ознакомьтесь с разделом [Использование средств Azure Data Lake для Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md).

## <a name="see-also"></a>См. также
* [Приступая к работе с аналитикой озера данных с помощью PowerShell](data-lake-analytics-get-started-powershell.md)
* [Приступая к работе с аналитикой озера данных с помощью портала Azure](data-lake-analytics-get-started-portal.md)
* [Использование инструментов озера данных для Visual Studio для разработки приложений U-SQL](data-lake-analytics-data-lake-tools-get-started.md)
* [Использование каталога U-SQL Azure Data Lake Analytics](data-lake-analytics-use-u-sql-catalog.md)
* [Использование средств Azure Data Lake для Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)