---
title: "Активация задания с помощью службы автоматизации Azure | Документация Майкрософт"
description: "Узнайте, как активировать задания диспетчера данных StorSimple (закрытая предварительная версия)"
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/16/2017
ms.author: vidarmsft
ms.openlocfilehash: 9691408bcd80afb6eba534f26749b76dd3bfe315
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-automation-to-trigger-a-job-private-preview"></a>Активация задания с помощью службы автоматизации Azure (закрытая предварительная версия)

В этой статье описывается активация задания диспетчера данных StorSimple с помощью службы автоматизации Azure.

## <a name="prerequisites"></a>Предварительные требования

Перед началом работы убедитесь, что у вас есть следующие компоненты:

*   Azure PowerShell установлен. [Скачать Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
*   Параметры конфигурации для инициализации задания преобразования данных (инструкции для получения этих параметров приведены в этой статье).
*   Определение задания, которое правильно настроено в гибридном ресурсе данных в группе ресурсов.
*   Скачайте [ZIP-файл](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/raw/master/Azure%20Automation%20For%20Data%20Manager/DataTransformationApp.zip) `DataTransformationApp.zip` из репозитория GitHub.
*   Скачайте [сценарий](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Azure%20Automation%20For%20Data%20Manager/Get-ConfigurationParams.ps1) `Get-ConfigurationParams.ps1` из репозитория GitHub.
*   Скачайте [сценарий](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Azure%20Automation%20For%20Data%20Manager/Trigger-DataTransformation-Job.ps1) `Trigger-DataTransformation-Job.ps1` из репозитория GitHub.

## <a name="step-by-step"></a>Пошаговое руководство

### <a name="get-azure-active-directory-permissions-for-the-automation-job-to-run-the-job-definition"></a>Получите разрешения Azure Active Directory для выполнения определения задания в задании службы автоматизации.

1. Чтобы получить параметры конфигурации Active Directory, сделайте следующее.

    1. Откройте Windows PowerShell на локальном компьютере. Убедитесь, что среда [Azure PowerShell](https://azure.microsoft.com/downloads/) установлена.
    1. Запустите сценарий `Get-ConfigurationParams.ps1` (в папке, скачанной ранее). В окне PowerShell введите следующую команду:

        ```
        .\Get-ConfigurationParams.ps1 -SubscriptionName "AzureSubscriptionName" -ActiveDirectoryKey "AnyRandomPassword" -AppName "ApplicationName"
         ```

        ActiveDirectoryKey представляет собой пароль, который будет использоваться позже. Введите выбранный пароль. AppName может быть любой строкой.

2. Этот сценарий выводит следующие значения, которые следует использовать во время активации модуля Runbook службы автоматизации. Запишите эти значения:

    - идентификатор клиента
    - Tenant ID
    - ключ Active Directory (совпадает с указанным выше);
    - Идентификатор подписки

### <a name="set-up-the-automation-account"></a>Настройка учетной записи службы автоматизации

1. Войдите в Azure и откройте учетную запись службы автоматизации.
2. Щелкните плитку **Ресурсы**, чтобы открыть список ресурсов.
3. Щелкните плитку **Модули**, чтобы открыть список модулей.
4. Нажмите кнопку **+ Add a module** (+ Добавить модуль), и запустится колонка "Добавить модуль".

    ![Параметры учетной записи службы автоматизации](./media/storsimple-data-manager-job-using-automation/add-module1m.png)

5. Выбрав файл `DataTransformationApp.zip` на локальном компьютере, нажмите кнопку **ОК** для импорта модуля.

   Когда служба автоматизации Azure импортирует модуль в вашу учетную запись, она извлекает метаданные о модуле. Эта операция может занять несколько минут.

   ![Параметры учетной записи службы автоматизации](./media/storsimple-data-manager-job-using-automation/add-module2m.png)

   

6. Вы получите уведомления о начале и завершении развертывания модуля.  Состояние также можно проверить на плитке **Модули**.

### <a name="to-import-the-runbook-that-triggers-the-job-definition"></a>Импорт модуля Runbook, активирующего определение задания

1. На портале Azure выберите свою учетную запись службы автоматизации.
2. Щелкните плитку **Модули Runbook**, чтобы открыть список модулей Runbook.
3. Щелкните **+ Add a runbook** (+ Добавить модуль Runbook) и **Импортировать существующий Runbook**.

   ![Импорт существующего модуля Runbook](./media/storsimple-data-manager-job-using-automation/import-a-runbook.png)

4. Щелкните **Файл Runbook** и выберите файл для импорта `Trigger-DataTransformation-Job.ps1`.
5. Щелкните **Создать**, чтобы импортировать модуль Runbook. Новый модуль Runbook появится в списке модулей Runbook для учетной записи службы автоматизации.
7. Щелкните модуль Runbook **Trigger-DataTransformation-Job** и нажмите кнопку **Изменить**.
8. Щелкните **Опубликовать** и при появлении запроса на подтверждение щелкните **Да**.


### <a name="to-run-the-runbook"></a>Чтобы запустить модуль Runbook, сделайте следующее.
1. На портале Azure выберите свою учетную запись службы автоматизации.
2. Щелкните элемент **Модули Runbook** , чтобы открыть список модулей Runbook.
3. Щелкните **Trigger-DataTransformation-Job**.
4. Щелкните **Пуск** , чтобы запустить модуль Runbook.

   ![Запуск модуля Runbook](./media/storsimple-data-manager-job-using-automation/run-runbook1m.png)

5. В колонке **Запуск Runbook** введите все параметры. Нажмите кнопку **ОК**, чтобы отправить задание преобразования данных.

   ![Запуск модуля Runbook](./media/storsimple-data-manager-job-using-automation/run-runbook2m.png)


## <a name="next-steps"></a>Дальнейшие действия

[Преобразуйте данные с помощью пользовательского интерфейса диспетчера данных StorSimple](storsimple-data-manager-ui.md).