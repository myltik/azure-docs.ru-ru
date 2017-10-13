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
ms.openlocfilehash: 367e4ba792f83d6ee246208306e4c09b69cb49ef
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="u-sql-local-run-and-local-debug-with-visual-studio-code"></a>Локальный запуск и локальная отладка U-SQL в Visual Studio Code

## <a name="prerequisites"></a>Предварительные требования
Прежде чем приступить к работе, убедитесь, что у вас есть следующие необходимые компоненты.
- Средства Azure Data Lake для Visual Studio Code. Инструкции см. в статье [Использование средств Azure Data Lake для Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md).
- C# для Visual Studio Code (для локальной отладки U-SQL).

   ![Установка C# в средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-install-ms-vscodecsharp.png)
   
   > [!NOTE]
   > Функции локального запуска и отладки U-SQL сейчас поддерживаются только для пользователей Windows. 


## <a name="set-up-the-u-sql-local-run-environment"></a>Настройка среды для локального запуска U-SQL

1. Откройте палитру команд, нажав клавиши CTRL+SHIFT+P, и введите **ADL: Download LocalRun Dependency** (ADL: скачать зависимости для локального запуска), чтобы скачать пакеты.  

   ![Скачивание пакетов зависимостей для локального запуска ADL](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/DownloadLocalRun.png)

2. Найдите пакеты зависимостей по пути, показанному на панели **вывода**, затем установите BuildTools и Win10SDK 10240. Пример пути:  
`C:\Users\xxx\.vscode\extensions\usqlextpublisher.usql-vscode-ext-x.x.x\LocalRunDependency
`  
  ![Поиск пакетов зависимостей](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/LocateDependencyPath.png)

   а. Следуйте инструкциям мастера, чтобы установить BuildTools.   

  ![Установка BuildTools](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallBuildTools.png)

   b. Следуйте инструкциям мастера, чтобы установить Win10SDK 10240.  

  ![Установка Win10SDK 10240](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallWin10SDK.png)

3. Настройте переменную среды. Установите для переменной среды **SCOPE_CPP_SDK** значение:  
`C:\Users\xxx\.vscode\extensions\usqlextpublisher.usql-vscode-ext-x.x.x\LocalRunDependency\CppSDK_3rdparty
`  
4. Перезапустите ОС, чтобы убедиться, что изменения переменных среды вступили в силу.  

   ![Проверка установки переменной среды SCOPE_CPP_SDK](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/ConfigScopeCppSDk.png)

## <a name="start-the-local-run-service-and-submit-the-u-sql-job-to-a-local-account"></a>Локальный запуск службы и отправка задания U-SQL в локальную учетную запись 
Для новых пользователей будет предложено установить пакеты "ADL: скачать зависимости для локального запуска", если они еще не установлены.
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
Для новых пользователей будет предложено установить пакеты "ADL: скачать зависимости для локального запуска", если они еще не установлены.
  
1. Нажмите клавиши CTRL+SHIFT+P, чтобы открыть палитру команд, и введите **ADL: Start Local Run Service** (ADL: скачать зависимости для локального запуска). Откроется консоль команд. Убедитесь, что установлено значение параметра **DataRoot**.
3. Установите точку останова в коде программной части C#.
4. Вернитесь в редактор скриптов, нажмите клавиши CTRL+SHIFT+P, чтобы открыть командную консоль, а затем введите **Local Debug** (Локальная отладка) для запуска службы локальной отладки.

![Результат локальной отладки в средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-debug-result.png)


## <a name="next-steps"></a>Дальнейшие действия
- Сведения об использовании средств Azure Data Lake для Visual Studio Code см. в статье [Использование средств Azure Data Lake для Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md).
- Дополнительные сведения о начале работы с Data Lake Analytics см. в статье [Начало работы с Azure Data Lake Analytics с помощью портала Azure](data-lake-analytics-get-started-portal.md).
- Дополнительные сведения об использовании средств Data Lake для U-SQL см. в статье [Разработка скриптов U-SQL с помощью средств Data Lake для Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Сведения о разработке сборок см. в статье [Разработка сборок U-SQL для заданий Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-assemblies.md).
