---
title: "Создание задачи наблюдателя в учетной записи службы автоматизации Azure | Документация Майкрософт"
description: "Узнайте, как создать задачу наблюдателя в учетной записи службы автоматизации Azure, чтобы отслеживать создание файлов в папке."
services: automation
documentationcenter: 
author: eamonoreilly
manager: 
editor: 
ms.assetid: 0dd95270-761f-448e-af48-c8b1e82cd821
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/15/2017
ms.author: eamono
ms.openlocfilehash: 0ddd31f7ce2217c1136eccd391bb30bd4461c3e5
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2017
---
# <a name="azure-automation-watcher-tasks-enable-you-to-respond-to-events-happening-in-your-local-datacenter"></a>Задачи наблюдателя в службе автоматизации Azure позволяют реагировать на события, происходящие в локальном центре обработки данных.

Из этого руководства вы узнаете, как создать задачу наблюдателя для следующих целей:

> [!div class="checklist"]
> * создание runbook наблюдателя для поиска новых файлов в каталоге;
> * создание переменной службы автоматизации для сохранения времени последней обработки файла наблюдателем;
> * создание runbook действия, который вызывается, когда runbook наблюдателя находит новый файл;
> * создание задачи наблюдателя, в которой выбираются runbook наблюдателя и runbook действия;
> * вызов наблюдателя путем добавления нового файла в каталог;
> * проверка наличия сведений о новом файле в выходных данных модуля runbook действия.  

## <a name="prerequisites"></a>Предварительные требования

Ниже перечислены необходимые условия для выполнения инструкций в этом учебнике.
+ Подписка Azure. Если у вас ее нет, [активируйте преимущества для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) или [зарегистрируйте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
+ [Учетная запись службы автоматизации](automation-offering-get-started.md) для хранения runbook наблюдателя, runbook действия и задачи наблюдателя.
+ [Гибридная рабочая роль runbook](automation-hybrid-runbook-worker.md), в которой выполняется задача наблюдателя.

## <a name="create-a-watcher-runbook-that-looks-for-new-files"></a>Создание runbook наблюдателя для поиска новых файлов
1.  Откройте учетную запись службы автоматизации и щелкните на странице модулей runbook.
2.  Нажмите кнопку "Просмотреть коллекцию".
![Список модулей runbook, пользовательский интерфейс](media/automation-watchers-tutorial/WatcherTasksRunbookList.png)
3.  Выполните поиск по фразе "Watch-NewFile" и импортируйте runbook в учетную запись службы автоматизации.
![Публикация модуля runbook, пользовательский интерфейс](media/automation-watchers-tutorial/Watch-NewFileRunbook.png)
4.  Щелкните "Изменить", чтобы просмотреть сведения об источнике модуля Runbook, и нажмите кнопку "Опубликовать".

## <a name="create-an-automation-variable-to-keep-the-last-time-a-file-was-processed-by-the-watcher"></a>Создание переменной службы автоматизации для сохранения времени последней обработки файла наблюдателем
1.  Откройте страницу переменных в списке общих ресурсов и щелкните "Добавить переменную" ![Список переменных, пользовательский интерфейс](media/automation-watchers-tutorial/WatcherVariableList.png)
2.  Введите имя "Watch-NewFileTimestamp"
3.  Выберите для типа значение DateTime и нажмите кнопку "Создать".
![Создание переменной водяного знака, пользовательский интерфейс](media/automation-watchers-tutorial/WatcherWatermarkVariable.png)

## <a name="create-an-action-runbook-that-is-called-when-the-watcher-runbook-finds-a-new-file"></a>Создание runbook действия, который вызывается, когда runbook наблюдателя находит новый файл
1.  Щелкните на странице модулей runbook в категории PROCESS AUTOMATION (Автоматизация процессов).
2.  Нажмите кнопку "Просмотреть коллекцию".
3.  Выполните поиск по фразе "Process-NewFile" и импортируйте runbook в учетную запись службы автоматизации.
4.  Щелкните "Изменить", чтобы просмотреть сведения об источнике модуля runbook, и нажмите кнопку "Опубликовать".
![Обработка задачи наблюдателя, пользовательский интерфейс](media/automation-watchers-tutorial/Watch-ProcessNewFile.png)


## <a name="create-a-watcher-task-that-selects-the-watcher-runbook-and-action-runbook"></a>Создание задачи наблюдателя, в которой выбираются runbook наблюдателя и runbook действия
1.  Откройте страницу задач наблюдателя и нажмите кнопку "Добавить задачу наблюдателя".
![Список задач наблюдателя, пользовательский интерфейс](media/automation-watchers-tutorial/WatchersList.png)
2.  Введите имя "WatchMyFolder".
3.  Нажмите "Настройка наблюдателя" и выберите модуль runbook с именем Watch-NewFile.
![Настройка наблюдателя, пользовательский интерфейс](media/automation-watchers-tutorial/ConfigureWatcher.png)
4.  Введите следующие значения параметров:
    *   FOLDERPATH. Папка в гибридной рабочей роли, где создаются файлы.
    *   EXTENSION. Оставьте поле пустым, чтобы обрабатывались все расширения файлов.
    *   RECURSE. Оставьте значение по умолчанию.
    *   RUN SETTINGS. Выберите гибридную рабочую роль.
5.  Нажмите кнопку "ОК" и щелкните "Выбрать", чтобы вернуться на страницу наблюдателя.
6.  Нажмите "Настройка действия" и выберите модуль runbook с именем Process-NewFile.
![Настройка действия наблюдателя, пользовательский интерфейс](media/automation-watchers-tutorial/ConfigureAction.png)
7.  Введите следующие значения параметров:
    *   EVENTDATA. Не указывайте. Данные передаются из runbook наблюдателя.
    *   Run Settings. Оставьте значение Azure, так как этот модуль runbook выполняется в службе автоматизации.
8.  Нажмите кнопку "ОК" и щелкните "Выбрать", чтобы вернуться на страницу наблюдателя.
9.  Нажмите кнопку "ОК", чтобы создать задачу наблюдателя.

## <a name="trigger-a-watcher-by-adding-a-new-file-to-a-directory"></a>Вызов наблюдателя путем добавления нового файла в каталог
1.  Установите удаленное подключение к гибридной рабочей роли.
2.  Добавьте новый текстовый файл в папку, которая отслеживается задачей наблюдателя.

## <a name="inspect-the-output-from-the-action-runbook-that-shows-information-on-the-new-file"></a>Проверка наличия сведений о новом файле в выходных данных модуля runbook действия
1.  Щелкните задачу наблюдателя для WatchMyFolder.
2.  Щелкните "Просмотреть потоки наблюдателей", чтобы убедиться, что наблюдателем обнаружен новый файл и запущен runbook действия.
3.  Щелкните View watcher action jobs (Просмотреть задания действия наблюдателя), чтобы просмотреть задание runbook действия.
![Задания действия наблюдателя, пользовательский интерфейс](media/automation-watchers-tutorial/WatcherActionJobs.png)


## <a name="next-steps"></a>Дальнейшие действия:

Дополнительные сведения см. в статье [Мой первый модуль Runbook PowerShell](automation-first-runbook-textual-powershell.md).








