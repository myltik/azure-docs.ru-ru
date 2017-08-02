---
title: "Средства Azure Data Lake — локальный запуск и локальная отладка U-SQL в Visual Studio Code | Документы Майкрософт"
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
ms.translationtype: HT
ms.sourcegitcommit: c999eb5d6b8e191d4268f44d10fb23ab951804e7
ms.openlocfilehash: e5e767a6f31be052c9bae059b49b31d59fdfd894
ms.contentlocale: ru-ru
ms.lasthandoff: 07/17/2017

---

# <a name="u-sql-local-run-and-local-debug-with-visual-studio-code"></a>Локальный запуск и локальная отладка U-SQL в Visual Studio Code

## <a name="prerequisites"></a>Предварительные требования
- Средства Azure Data Lake для Visual Studio Code. Инструкции см. в разделе [Использование средств Azure Data Lake для Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md).
- C# для Visual Studio Code (для локальной отладки U-SQL).

   ![Установка средств Data Lake для Visual Studio Code install vscodeCsharp](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-install-ms-vscodecsharp.png)
   
   > [!NOTE]
   > Функции локального запуска и отладки U-SQL сейчас поддерживаются только для пользователей Windows. 


## <a name="set-up-u-sql-local-run-environment"></a>Настройка среды для локального запуска U-SQL

1. Откройте палитру команд, нажав **CTRL+SHIFT+P**, и введите **ADL: скачать зависимости для локального запуска**, чтобы скачать пакеты.  

   ![Скачать зависимость для локального запуска](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/DownloadLocalRun.png)

2. Найдите пакеты зависимостей по пути, показанному на панели вывода ниже, затем установите BuildTools и Win10SDK 10240. Например:  
`C:\Users\xxx\.vscode\extensions\usqlextpublisher.usql-vscode-ext-x.x.x\LocalRunDependency
`  
  ![Найти зависимости](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/LocateDependencyPath.png)

- Установка BuildTools — следуйте инструкциям мастера установки.   

  ![Установка BuildTools](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallBuildTools.png)
- Установка Win10SDK 10240 — следуйте инструкциям по установке.  

  ![Установка Win10SDK](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallWin10SDK.png)
3. Установите переменную окружения **SCOPE_CPP_SDK** в значение:  
`C:\Users\xxx\.vscode\extensions\usqlextpublisher.usql-vscode-ext-x.x.x\LocalRunDependency\CppSDK_3rdparty
`  
Не запустите перезапустить ОС, чтобы изменения переменных среды вступили в силу.  

   ![ConfigSCOPE_CPP_SDK](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/ConfigScopeCppSDk.png)

## <a name="start-local-run-service-and-submit-u-sql-job-to-local-account"></a>Запустите службу локально и отправьте задание U-SQL в локальную учетную запись 
Для новых пользователей будет предложено установить пакеты **ADL: скачать зависимости для локального запуска**, если они еще не установлены.
1. Нажмите **CTRL+SHIFT+P**, чтобы открыть палитру команд, и введите **ADL: скачать зависимости для локального запуска**.
2. Примите условия лицензии в первый раз. 

   ![Принятие условий лицензии ](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/AcceptEULA.png)   
3. Откроется консоль команд. Для новых пользователей введите 3, затем введите путь к локальной папке для входных и выходных данных. Для остальных параметров можно использовать значения по умолчанию. 

   ![Локальный запуск команд в средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-cmd.png)
4. Нажмите CTRL+SHIFT+P, чтобы открыть палитру команд, и введите **ADL: отправить задание**, а затем выберите **Локальная**, чтобы отправить задание в локальную учетную запись.

   ![Выбор локальной учетной записи в средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-select-local.png)
5. После отправки задания можно просмотреть сведения об отправке, щелкнув URL-адрес задания в окне вывода. Состояние отправки задания также можно просмотреть в консоли команд (для просмотра сведений о задании введите 7).

   ![Результат локального запуска для средств Data Lake в Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-result.png)
   ![Состояние локального запуска команды для средств Data Lake в Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-localrun-cmd-status.png) 



## <a name="start-local-debug-for-u-sql-job"></a>Запуск локальной отладки задания U-SQL  
Для новых пользователей будет предложено установить пакеты **ADL: скачать зависимости для локального запуска**, если они еще не установлены.
  
1. Нажмите **CTRL+SHIFT+P**, чтобы открыть палитру команд, и введите **ADL: скачать зависимости для локального запуска**. Откроется окно команд. Убедитесь, что установлено значение параметра **DataRoot**.
3. Установите точку останова в коде C#.
4. Вернитесь в редактор сценариев, нажмите **CTRL+SHIFT+P**, чтобы открыть палитру команд, и введите **Локальная отладка**, чтобы запустить службу локальной отладки.

![Результат локальной отладки в средствах Data Lake для Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-debug-result.png)


## <a name="next-steps"></a>Дальнейшие действия
- Сведения об использовании средств Azure Data Lake для Visual Studio Code см. в статье [Использование средств Azure Data Lake для Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md).
- Сведения для начала работы с Data Lake Analytics вы найдете в статье [Tutorial: get started with Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md) (Руководство. Начало работы с Azure Data Lake Analytics с помощью портала Azure).
- Дополнительные сведения об использовании средств Data Lake для U-SQL есть в статье [Учебник. Разработка скриптов U-SQL с помощью средств озера данных для Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Сведения о разработке сборок см. в статье [Разработка сборок U-SQL для заданий Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-assemblies.md).
