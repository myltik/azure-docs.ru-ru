---
title: Запуск задания в диспетчере данных StorSimple при помощи службы автоматизации Azure | Документация Майкрософт
description: Узнайте, как активировать задания диспетчера данных StorSimple с помощью службы автоматизации Azure
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: 1e5fcbee664271058ac1c7fa80bb285e09b8579a
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/17/2018
ms.locfileid: "27862202"
---
# <a name="use-azure-automation-to-trigger-a-job"></a>Активация задания с помощью службы автоматизации Azure

В этой статье объясняется, как использовать функцию преобразования данных в службе диспетчера данных StorSimple для преобразования данных устройства StorSimple. Задание преобразования данных можно запустить двумя способами: 

 - Использование пакета SDK для .NET
 - Использование модуля runbook службы автоматизации Azure.
 
В этой статье подробно описывается создание модуля runbook службы автоматизации Azure, а затем запуск задания преобразования данных с его помощью. Дополнительные сведения о том, как запустить преобразование данных с помощью пакета SDK для .NET, см. в статье [Запуск преобразования данных с помощью пакета SDK для .NET (закрытая предварительная версия)](storsimple-data-manager-dotnet-jobs.md).

## <a name="prerequisites"></a>предварительным требованиям

Перед началом работы убедитесь, что у вас есть следующие компоненты:

*   Среда Azure PowerShell, установленная на клиентском компьютере. [Скачать Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps).
*   Правильно настроенное определение задания в службе диспетчера данных StorSimple в одной группе ресурсов.
*   Файл [`DataTransformationApp.zip`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/raw/master/Azure%20Automation%20For%20Data%20Manager/DataTransformationApp.zip), скачанный из репозитория GitHub. 
*   Сценарий [`Trigger-DataTransformation-Job.ps1`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Azure%20Automation%20For%20Data%20Manager/Trigger-DataTransformation-Job.ps1), скачанный из репозитория GitHub.

## <a name="step-by-step-procedure"></a>Пошаговая процедура

### <a name="set-up-the-automation-account"></a>Настройка учетной записи службы автоматизации

1. Создайте учетную запись запуска от имени Azure на портале Azure. Для этого перейдите к **Azure Marketplace > Все** и выполните поиск **службы автоматизации**. Выберите элемент **Учетные записи автоматизации**.

    ![Создание учетной записи службы автоматизации запуска от имени](./media/storsimple-data-manager-job-using-automation/search-automation-account1.png)

2. Чтобы добавить новую учетную запись службы автоматизации, нажмите кнопку **+ Добавить**.

    ![Создание учетной записи службы автоматизации запуска от имени](./media/storsimple-data-manager-job-using-automation/add-automation-account1.png)

3. В диалоговом окне **Add Automation** (Добавление учетной записи службы автоматизации):

    1. Укажите **имя** учетной записи службы автоматизации.
    2. Выберите **подписку**, связанную со службой диспетчера данных StorSimple.
    3. Создайте группу ресурсов или выберите имеющуюся.
    4. Выберите **расположение**.
    5. Оставьте выбранный по умолчанию вариант **Создать учетную запись запуска от имени**.
    6. Чтобы получить ссылки для быстрого доступа к панели мониторинга, установите флажок **Закрепить на панели мониторинга**. Нажмите кнопку **Создать**.

    ![Создание учетной записи службы автоматизации запуска от имени](./media/storsimple-data-manager-job-using-automation/create-automation-run-as-account.png)
    
    После успешного создания учетной записи автоматизации вы получите уведомление.
    
    ![Уведомление о развернутой учетной записи службы автоматизации](./media/storsimple-data-manager-job-using-automation/deployment-automation-account-notification1.png)

    Дополнительные сведения см. в статье [Обновление проверки подлинности учетных записей службы автоматизации с использованием учетных записей запуска от имени](../automation/automation-create-runas-account.md).

3. В созданной учетной записи перейдите к разделу **Общие ресурсы > Модули** и выберите **+ Add module** (+ Добавить модуль).

    ![Импорт модуля 1](./media/storsimple-data-manager-job-using-automation/import-module-1.png)

4. С локального компьютера перейдите к расположению файла `DataTransformationApp.zip`, а затем выберите и откройте модуль. Щелкните **ОК**, чтобы импортировать модуль.

    ![Импорт модуля 2](./media/storsimple-data-manager-job-using-automation/import-module-2.png)

   Когда служба автоматизации Azure импортирует модуль в вашу учетную запись, она извлекает метаданные о модуле. Эта операция может занять несколько минут.

   ![Импорт модуля 4](./media/storsimple-data-manager-job-using-automation/import-module-4.png)

5. Вы получите уведомления о начале и завершении развертывания модуля.  Состояние из раздела **Модули** изменится на **Доступно**.

    ![Импорт модуля 5](./media/storsimple-data-manager-job-using-automation/import-module-5.png)

### <a name="import-publish-and-run-automation-runbook"></a>Импорт, публикация и запуск модуля Runbook службы автоматизации

Чтобы активировать определение задания, выполните следующие действия для модуля runbook: импорт, публикация и запуск.

1. На портале Azure выберите свою учетную запись службы автоматизации. Последовательно выберите пункты **Автоматизация процессов > Модули Runbook** и щелкните **+ Add a runbook** (+ Добавить модуль Runbook).

    ![Добавление модуля runbook 1](./media/storsimple-data-manager-job-using-automation/add-runbook-1.png)

2. В разделе **Добавить модуль Runbook** щелкните **Импорт существующего модуля Runbook**.

3. Укажите файл сценария Azure PowerShell `Trigger-DataTransformation-Job.ps1` для **файла модуля runbook**. Тип модуля Runbook выбирается автоматически. Укажите имя и необязательное описание модуля Runbook. Нажмите кнопку **Создать**.

    ![Добавление модуля runbook 2](./media/storsimple-data-manager-job-using-automation/add-runbook-2.png)

4. Новый модуль Runbook появится в списке модулей Runbook для учетной записи службы автоматизации. Выберите и щелкните этот модуль Runbook.

    ![Добавление модуля runbook 3](./media/storsimple-data-manager-job-using-automation/add-runbook-3.png)

5. Измените модуль runbook и щелкните область **Test** (Тест).

    ![Добавление модуля runbook 4](./media/storsimple-data-manager-job-using-automation/add-runbook-4.png)

6. Укажите такие параметры, как имя службы диспетчера данных StorSimple, связанная группа ресурсов и имя определения задания. **Запустите** тест. После завершения выполнения создается отчет. Дополнительные сведения см. в разделе [Шаг 3. Тестирование модуля Runbook](../automation/automation-first-runbook-textual-powershell.md#step-3---test-the-runbook).

    ![Добавление модуля runbook 8](./media/storsimple-data-manager-job-using-automation/add-runbook-8.png)    

7. Проверьте выходные данные модуля Runbook в области тестирования. Если условие выполнено, закройте область. Щелкните **Публикация** и при появлении запроса на подтверждение подтвердите и опубликуйте модуль Runbook.

    ![Добавление модуля runbook 6](./media/storsimple-data-manager-job-using-automation/add-runbook-6.png)

8. Вернитесь к разделу **модулей runbook** и выберите только что созданный модуль runbook.

    ![Добавление модуля runbook 7](./media/storsimple-data-manager-job-using-automation/add-runbook-7.png)

9. **Запустите** модуль runbook. В колонке **Запуск Runbook** введите все параметры. Нажмите кнопку **ОК**, чтобы отправить и запустить задание преобразования данных.

10. Чтобы отслеживать ход выполнения задания на портале Azure, перейдите к окну **Задания** службы диспетчера данных StorSimple. Выберите и щелкните задание, чтобы просмотреть сведения о нем.

    ![Добавление модуля runbook 10](./media/storsimple-data-manager-job-using-automation/add-runbook-10.png)

## <a name="next-steps"></a>Дополнительная информация

[Преобразуйте данные с помощью пользовательского интерфейса диспетчера данных StorSimple](storsimple-data-manager-ui.md).