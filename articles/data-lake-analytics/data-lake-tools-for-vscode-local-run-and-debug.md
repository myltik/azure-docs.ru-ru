---
title: "Средства Azure Data Lake — локальный запуск и локальная отладка U-SQL в Visual Studio Code | Документация Майкрософт"
description: "Узнайте, как выполнять локальную отладку и локальный запуск с помощью средств Azure Data Lake для Visual Studio Code."
Keywords: "VScode,средства Azure Data Lake,локальный запуск,локальная отладка,предварительный просмотр файла хранилища,отправка по пути хранилища"
services: data-lake-analytics
documentationcenter: 
author: jejiang
manager: DJ
editor: jejiang
tags: azure-portal
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: big-data
ms.date: 07/14/2017
ms.author: jejiang
ms.openlocfilehash: 78a5efb19f73192dcc95103abc70c14a3ce2e29a
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2017
---
# <a name="u-sql-local-run-and-local-debug-for-windows-with-visual-studio-code"></a>Локальный запуск и локальная отладка U-SQL в Windows с помощью Visual Studio Code
Из этого документа вы узнаете, как выполнять задания U-SQL на локальном компьютере для разработки, чтобы ускорить программирование на ранних этапах или отлаживать код локально в Visual Studio Code. Инструкции по использованию средств Azure Data Lake для Visual Studio Code см. в статье [Использование средств Azure Data Lake для Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md). 


## <a name="set-up-the-u-sql-local-run-environment"></a>Настройка среды для локального запуска U-SQL

1. Откройте палитру команд, нажав клавиши CTRL+SHIFT+P, и введите **ADL: Download Local Run Dependency** (ADL: скачать зависимости для локального запуска), чтобы скачать пакеты.  

   ![Скачивание пакетов зависимостей для локального запуска ADL](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/DownloadLocalRun.png)

2. Найдите пакеты зависимостей по пути, показанному на панели **вывода**, затем установите BuildTools и Win10SDK 10240. Пример пути:  
`C:\Users\xxx\.vscode\extensions\usqlextpublisher.usql-vscode-ext-x.x.x\LocalRunDependency
`  
  ![Поиск пакетов зависимостей](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/LocateDependencyPath.png)

   2.1. Чтобы установить **BuildTools**, щелкните файл visualcppbuildtools_full.exe в папке LocalRunDependency, а затем выполните инструкции мастера установки.   

    ![Установка BuildTools](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallBuildTools.png)

   2.2. Чтобы установить **Win10SDK 10240**, щелкните файл sdksetup.exe в папке LocalRunDependency/Win10SDK_10.0.10240_2, а затем выполните инструкции мастера установки.  

    ![Установка Win10SDK 10240](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallWin10SDK.png)

3. Настройте переменную среды. Установите для переменной среды **SCOPE_CPP_SDK** значение:  
`C:\Users\xxx\.vscode\extensions\usqlextpublisher.usql-vscode-ext-x.x.x\LocalRunDependency\CppSDK_3rdparty
`  
4. Перезапустите ОС, чтобы убедиться, что изменения переменных среды вступили в силу.  

   ![Проверка установки переменной среды SCOPE_CPP_SDK](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/ConfigScopeCppSDk.png)

## <a name="start-the-local-run-service-and-submit-the-u-sql-job-to-a-local-account"></a>Локальный запуск службы и отправка задания U-SQL в локальную учетную запись 
Новые пользователи должны ввести **ADL: Download Local Run Dependency** (ADL: скачать зависимости для локального запуска), чтобы скачать пакеты для локального запуска, если вы еще не [настроили среду для локального запуска U-SQL](#set-up-the-u-sql-local-run-environment).

1. Нажмите клавиши CTRL+SHIFT+P, чтобы открыть палитру команд, и введите **ADL: Start Local Run Service** (ADL: скачать зависимости для локального запуска).   
2. Выберите **Принять**, чтобы принять условия лицензионного соглашения об использовании программного обеспечения Майкрософт в первый раз. 

   ![Принятие условий лицензионного соглашения об использовании программного обеспечения Майкрософт](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/AcceptEULA.png)   
3. Откроется консоль команд. Для новых пользователей введите **3**, а затем найдите локальную папку для входных и выходных данных. Для остальных параметров можно использовать значения по умолчанию. 

   ![Локальный запуск команд в средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-cmd.png)
4. Нажмите клавиши CTRL+SHIFT+P, чтобы открыть палитру команд, введите **ADL: Submit Job** (ADL: отправить задание) и выберите **Локальный**, чтобы отправить задание в локальную учетную запись.

   ![Выбор локальной учетной записи в средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-select-local.png)
5. После отправки задания можно просмотреть сведения об отправке. Чтобы просмотреть сведения об отправке, выберите **jobUrl** в окне **вывода**. Вы также можете просмотреть состояние отправки задания в консоли команд. Для просмотра сведений о задании в консоли команд введите **7**.

   ![Результат локального запуска для средств Data Lake в Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-result.png)
   ![Состояние локального запуска команды для средств Data Lake в Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-localrun-cmd-status.png) 


## <a name="start-a-local-debug-for-the-u-sql-job"></a>Запуск локальной отладки для задания U-SQL  
Для новых пользователей:

1. Введите **ADL: Download Local Run Dependency** (ADL: скачать зависимости для локального запуска), чтобы скачать пакеты для локального запуска, если вы еще не [настроили среду для локального запуска U-SQL](#set-up-the-u-sql-local-run-environment).
2. Установите пакет SDK для .NET Core 2.0, как предложено в окне сообщения, если пакет еще не установлен.
 
  ![Напоминание об установке Dotnet](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/remind-install-dotnet.png)
3. Установите C# для Visual Studio Code, как предложено в окне сообщения, если вы еще не сделали этого. Нажмите кнопку **Установить** чтобы продолжить, а затем перезапустите VSCode.

    ![Напоминание об установке C#](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/install-csharp.png)

Чтобы выполнить отладку локально, сделайте следующее:
  
1. Нажмите клавиши CTRL+SHIFT+P, чтобы открыть палитру команд, и введите **ADL: Start Local Run Service** (ADL: скачать зависимости для локального запуска). Откроется консоль команд. Убедитесь, что установлено значение параметра **DataRoot**.
2. Установите точку останова в коде программной части C#.
3. Вернитесь в редактор скриптов, щелкните правой кнопкой мыши и выберите **ADL: Local Debug** (ADL: локальная отладка).
    
   ![Результат локальной отладки в средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-debug-result.png)


## <a name="next-steps"></a>Дальнейшие действия
- Сведения об использовании средств Azure Data Lake для Visual Studio Code см. в статье [Использование средств Azure Data Lake для Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md).
- Дополнительные сведения о начале работы с Data Lake Analytics см. в статье [Начало работы с Azure Data Lake Analytics с помощью портала Azure](data-lake-analytics-get-started-portal.md).
- Дополнительные сведения об использовании средств Data Lake для U-SQL см. в статье [Разработка скриптов U-SQL с помощью средств Data Lake для Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Сведения о разработке сборок см. в статье [Разработка сборок U-SQL для заданий Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-assemblies.md).
