---
title: "Первый Runbook рабочего процесса PowerShell в службе автоматизации Azure | Документация Майкрософт"
description: "Учебник, в котором рассказывается, как создать, протестировать и опубликовать простой текстовый Runbook с помощью рабочего процесса PowerShell."
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: 
keywords: "рабочий процесс PowerShell, примеры рабочего процесса powershell, рабочие процессы powershell"
ms.assetid: 0002d7f7-e2b5-46e3-b5eb-4596b84fd526
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/19/2016
ms.author: magoedte;bwren
translationtype: Human Translation
ms.sourcegitcommit: 0ab72bd4ad531d1162726c6f5548fa253a4f5265
ms.openlocfilehash: 992ea5448ebc4b27f18e5621fbe62659f6bc4864


---
# <a name="my-first-powershell-workflow-runbook"></a>Первый Runbook рабочего процесса PowerShell

> [!div class="op_single_selector"]
> * [Графический](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Рабочий процесс PowerShell](automation-first-runbook-textual.md)
> 
> 

В данном учебнике описана процедура создания [Runbook рабочего процесса PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) в службе автоматизации Azure. Для начала мы протестируем и опубликуем простой модуль Runbook и расскажем, как отслеживать состояние его заданий. Затем мы изменим Runbook, настроив его на фактическое управление ресурсами Azure (в данном случае на запуск виртуальной машины Azure). Затем мы сделаем этот модуль Runbook еще надежнее, добавив параметры.

## <a name="prerequisites"></a>Предварительные требования
Для работы с этим учебником необходимо следующее.

* Подписка Azure. Если у вас ее нет, [активируйте преимущества для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) или <a href="/pricing/free-account/" target="_blank">[зарегистрируйте бесплатную учетную запись](https://azure.microsoft.com/free/).
* [Учетная запись службы автоматизации](automation-security-overview.md) , чтобы хранить модуль Runbook и выполнять проверку подлинности ресурсов Azure.  Эта учетная запись должна иметь разрешение на запуск и остановку виртуальной машины.
* Виртуальная машина Azure. Это не должна быть рабочая машина, поскольку в процессе изучения данного материала ее нужно будет остановить и запустить заново.

## <a name="step-1---create-new-runbook"></a>Шаг 1. Создание нового модуля Runbook
Для начала мы создадим простой модуль Runbook, выводящий на экран текст *Привет, мир!*

1. На портале Azure выберите свою учетную запись в службе автоматизации.  
   Страница учетной записи в службе автоматизации позволяет быстро получить представление о ресурсах, доступных в этой учетной записи. Некоторые ресурсы уже должны быть доступны. Большинство из них — это модули, которые добавляются в каждую новую учетную запись в службе автоматизации по умолчанию. Кроме того, вам потребуется ресурс учетных данных, упомянутый в [предварительных требованиях](#prerequisites).
2. Щелкните плитку **Модули Runbook** , чтобы открыть список модулей Runbook.<br> ![Элемент управления "Модули Runbook"](media/automation-first-runbook-textual/runbooks-control.png)
3. Создайте новый модуль Runbook. Для этого нажмите кнопку **Add a runbook** (Добавить модуль Runbook), а затем выберите **Создать новый Runbook**.
4. Присвойте Runbook имя *MyFirstRunbook-Workflow*.
5. В данном случае мы собираемся создать [Runbook рабочего процесса PowerShell](automation-runbook-types.md#powershell-workflow-runbooks), поэтому для параметра **Тип Runbook** следует выбрать значение **Рабочий процесс PowerShell**.<br> ![Новый Runbook](media/automation-first-runbook-textual/new-runbook.png)
6. Щелкните **Создать** , чтобы создать модуль Runbook и открыть текстовый редактор.

## <a name="step-2---add-code-to-the-runbook"></a>Шаг 2. Добавление кода в Runbook
Можно либо напрямую ввести код в модуль Runbook, либо выбрать командлеты, модули Runbook и ресурсы из элемента управления "Библиотека" и добавить их к модулю Runbook с любыми связанными параметрами. В этом пошаговом руководстве мы введем код напрямую в модуль Runbook.

1. Созданный нами модуль Runbook пока пуст и содержит лишь обязательное ключевое слово *workflow* , имя нашего модуля Runbook и фигурные скобки, в которые будет добавлен весь рабочий процесс.

   ```
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```
2. Введите *Write-Output "Привет, мир!"*  между фигурными скобками.

   ```
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```
3. Сохраните модуль Runbook, щелкнув **Сохранить**.<br> ![Сохранить Runbook](media/automation-first-runbook-textual/runbook-edit-toolbar-save.png)

## <a name="step-3---test-the-runbook"></a>Шаг 3. Тестирование модуля Runbook
Прежде чем опубликовать модуль Runbook и, таким образом, сделать его доступным для рабочей среды, необходимо проверить, нормально ли он работает. Чтобы протестировать модуль Runbook, нужно запустить его **черновую** версию и проверить его работу в интерактивном режиме.

1. Щелкните **Область тестирования**.<br> ![область тестирования](media/automation-first-runbook-textual/runbook-edit-toolbar-test-pane.png)
2. Щелкните **Пуск** , чтобы начать тестирование. Активным должен быть только этот параметр.
3. При этом создается [задание Runbook](automation-runbook-execution.md) и отображается его состояние.  
   Сначала задание получает состояние *В очереди* , показывающее, что оно ожидает доступа к исполнителю Runbook в облаке. Как только рабочая роль затребует задание, оно получит состояние *Запущено*, а с началом фактического выполнения модуля Runbook — состояние *Выполняется*.  
4. Когда задание модуля Runbook будет выполнено, на экране появится результат. В нашем случае это должен быть текст *Привет, мир!*.<br> ![Привет, мир!](media/automation-first-runbook-textual/test-output-hello-world.png)
5. Закройте область тестирования, чтобы вернуться на холст.

## <a name="step-4---publish-and-start-the-runbook"></a>Шаг 4. Публикация и запуск модуля Runbook
Модуль, который мы только что создали, все еще находится в режиме проекта. Прежде чем запустить модуль в рабочей среде, его нужно опубликовать. При публикации модуля Runbook существующая опубликованная версия перезаписывается черновой версией. В нашем случае опубликованной версии не существует, поскольку Runbook был создан только что.

1. Щелкните **Опубликовать**, чтобы опубликовать модуль Runbook, а затем нажмите кнопку **Да** в появившемся запросе.<br> ![Опубликовать](media/automation-first-runbook-textual/runbook-edit-toolbar-publish.png)
2. Прокрутив экран влево до области **Модули Runbook**, вы увидите, что в поле **Состояние авторизации** для данного модуля Runbook появилось значение **Опубликован**.
3. Прокрутите экран вправо до области **MyFirstRunbook-Workflow**.  
   Параметры в верхней части экрана позволяют запустить модуль Runbook, запланировать его запуск в определенное время или создать [Webhook](automation-webhooks.md) , чтобы модуль можно было запускать с помощью HTTP-вызова.
4. Нам нужно только запустить модуль Runbook, поэтому щелкните **Пуск**, а затем нажмите кнопку **Да** в появившемся запросе.<br> ![Запуск модуля Runbook](media/automation-first-runbook-textual/runbook-toolbar-start.png)
5. Откроется область заданий с созданным нами заданием Runbook. Эту область можно закрыть, но в данном случае мы оставим ее открытой, чтобы проследить за ходом выполнения задания.
6. Состояние задания отображается в поле **Сводка по заданию** и отражает состояния, которые мы наблюдали при тестировании модуля Runbook.<br> ![Сводные данные задания](media/automation-first-runbook-textual/job-pane-summary.png)
7. Как только состояние модуля Runbook изменится на *Выполнено*, щелкните **Выходные данные**. Откроется область "Выходные данные" с текстом *Привет, мир!*.<br> ![Сводные данные задания](media/automation-first-runbook-textual/job-pane-output.png)  
8. Закройте область выходных данных.
9. Щелкните **Потоки** , чтобы открыть область "Потоки" для задания Runbook. В потоке выходных данных должен отображаться только текст *Hello World* , но могут присутствовать и другие потоки заданий Runbook, например "Подробные сведения" и "Ошибка", если Runbook записывает в них какие-то данные.<br> ![Сводные данные задания](media/automation-first-runbook-textual/job-pane-streams.png)
10. Закройте область потоков и область заданий, чтобы вернуться в область MyFirstRunbook.
11. Щелкните **Задания** , чтобы открыть область "Задания" для этого Runbook. Откроется список всех заданий, созданных этим модулем Runbook. В нем должно быть только одно задание, так как мы запускали задание только один раз.<br> ![Задания](media/automation-first-runbook-textual/runbook-control-jobs.png)
12. Если щелкнуть это задание, откроется та же область заданий, которую мы видели при запуске модуля Runbook. С ее помощью можно вернуться назад и просмотреть сведения о любом задании, созданном для конкретного модуля Runbook.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Шаг 5. Добавление проверки подлинности для управления ресурсами Azure
Мы протестировали и опубликовали свой модуль Runbook, но пока он не выполняет никаких полезных действий. Нам нужно, чтобы он управлял ресурсами Azure. Runbook не сможет делать это, пока не пройдет проверку подлинности с использованием учетных данных, упомянутых в [предварительных требованиях](#prerequisites). Для этого используется командлет **Add-AzureRMAccount** .

1. Откройте текстовый редактор, щелкнув **Изменить** в области MyFirstRunbook-Workflow.<br> ![Изменить Runbook](media/automation-first-runbook-textual/runbook-toolbar-edit.png)
2. Нам больше не потребуется использовать строку **Write-Output** , поэтому удалите ее.
3. Поместите курсор в пустую строку между фигурными скобками.
4. Введите или скопируйте и вставьте следующий код, который выполнит проверку подлинности с помощью учетной записи запуска от имени службы автоматизации:

   ```
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   ```
5. Щелкните **область тестирования** , чтобы проверить модуль Runbook.
6. Щелкните **Пуск** , чтобы начать тестирование. После его завершения на экране должны отобразиться приблизительно такие основные сведения из вашей учетной записи. Это подтверждает допустимость учетных данных.<br> ![Проверка подлинности](media/automation-first-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Шаг 6. Добавление кода запуска виртуальной машины
Теперь, когда модуль Runbook прошел проверку подлинности в нашей подписке Azure, можно управлять ресурсами. Мы добавим команду запуска виртуальной машины. Вы можете выбрать любую виртуальную машину в своей подписке Azure. Имя этой машины будет прописано в коде командлета.

1. После команды *Add-AzureRmAccount* введите команду *Start-AzureRmVM -Name 'имя_ВМ' -ResourceGroupName 'имя_группы_ресурсов'*, указав имя виртуальной машины, которую нужно запустить, и имя ее группы ресурсов.  

   ```
   workflow MyFirstRunbook-Workflow
   {
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   }
   ```
2. Сохраните модуль Runbook, после чего щелкните **область тестирования** для выполнения проверок.
3. Щелкните **Пуск** , чтобы начать тестирование. После завершения теста проверьте, запущена ли виртуальная машина.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>Шаг 7. Добавление входного параметра в модуль Runbook
Созданный модуль Runbook в настоящее время запускает виртуальную машину, жестко указанную в модуле Runbook, однако было бы полезнее, если бы мы могли указать виртуальную машину при запуске Runbook. Для этого добавим в модуль Runbook входные параметры.

1. Добавьте значения для параметров *VMName* (Имя ВМ) и *ResourceGroupName* (Имя группы ресурсов) в модуль Runbook и используйте эти переменные в командлете **Start-AzureRmVM**, как показано в примере ниже.

   ```
   workflow MyFirstRunbook-Workflow
   {
    Param(
     [string]$VMName,
     [string]$ResourceGroupName
    )  
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
   }
   ```
2. Сохраните модуль Runbook и откройте область тестирования. Обратите внимание, что теперь значения двух входных переменных, которые будут использоваться в тесте, можно указать.
3. Закройте область тестирования.
4. Щелкните **Опубликовать** , чтобы опубликовать новую версию модуля Runbook.
5. Остановите виртуальную машину, запущенную на предыдущем этапе.
6. Щелкните **Пуск** , чтобы запустить модуль Runbook. Введите значения **VMName** и **ResourceGroupName** для виртуальной машины, которую нужно запустить.<br> ![Start Runbook](media/automation-first-runbook-textual/automation-pass-params.png)
7. Когда модуль Runbook будет выполнен, проверьте, запустилась ли виртуальная машина.

## <a name="next-steps"></a>Дальнейшие действия
* Чтобы начать работу с графическими модулями Runbook, см. инструкции в статье [Первый графический Runbook](automation-first-runbook-graphical.md).
* Чтобы начать работу с модулями Runbook PowerShell, см. инструкции в статье [Мой первый модуль Runbook PowerShell](automation-first-runbook-textual-powershell.md).
* Дополнительные сведения о типах модулей Runbook, их преимуществах и ограничениях см. в статье [Типы модулей Runbook в службе автоматизации Azure](automation-runbook-types.md).
* Дополнительные сведения о функции поддержки скриптов PowerShell см. в статье [Announcing Native PowerShell Script Support in Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/) (Встроенная поддержка скриптов PowerShell в службе автоматизации Azure).



<!--HONumber=Nov16_HO2-->


