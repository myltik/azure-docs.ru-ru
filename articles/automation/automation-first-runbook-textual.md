---
title: Первый runbook рабочего процесса PowerShell в службе автоматизации Azure
description: Учебник, в котором рассказывается, как создать, протестировать и опубликовать простой текстовый Runbook с помощью рабочего процесса PowerShell.
keywords: рабочий процесс PowerShell, примеры рабочего процесса powershell, рабочие процессы powershell
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 65aad3244ce49e77bdd2e207bf14cf2e7ef459b9
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34195368"
---
# <a name="my-first-powershell-workflow-runbook"></a>Первый Runbook рабочего процесса PowerShell

> [!div class="op_single_selector"]
> * [Графический](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Рабочий процесс PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 
> 

В данном учебнике описана процедура создания [Runbook рабочего процесса PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) в службе автоматизации Azure. Для начала вы протестируете и опубликуете простой модуль runbook, а также узнаете, как отслеживать состояние его заданий. Затем мы изменим модуль runbook, настроив его для фактического управления ресурсами Azure (в нашем примере это запуск виртуальной машины Azure). Затем вы сделаете этот модуль runbook еще надежнее, добавив параметры.

## <a name="prerequisites"></a>предварительным требованиям
Для работы с этим учебником требуется:

* Подписка Azure. Если у вас ее нет, [активируйте преимущества для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) или [зарегистрируйте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Учетная запись службы автоматизации](automation-offering-get-started.md) , чтобы хранить модуль Runbook и выполнять проверку подлинности ресурсов Azure.  Эта учетная запись должна иметь разрешение на запуск и остановку виртуальной машины.
* Виртуальная машина Azure. Это не должна быть рабочая виртуальная машина, так как для работы с этим руководством ее нужно будет остановить и запустить заново.

## <a name="step-1---create-new-runbook"></a>Шаг 1. Создание нового модуля Runbook
Для начала создайте простой модуль runbook, выводящий на экран текст *Hello World*.

1. На портале Azure выберите свою учетную запись службы автоматизации.

   Страница учетной записи в службе автоматизации позволяет быстро получить представление о ресурсах, доступных в этой учетной записи. Некоторые ресурсы уже должны быть доступны. Большинство из них — это модули, которые добавляются в каждую новую учетную запись в службе автоматизации по умолчанию. Кроме того, вам потребуется ресурс учетных данных, упомянутый в [предварительных требованиях](#prerequisites).

1. Выберите **Модули Runbook** в разделе **Автоматизация процессов**, чтобы открыть список модулей.
2. Создайте новый модуль runbook. Для этого нажмите кнопку **Добавить Runbook**, а затем выберите **Создать новый Runbook**.
3. Присвойте Runbook имя *MyFirstRunbook-Workflow*.
4. В данном случае вы создадите [модуль runbook рабочего процесса PowerShell](automation-runbook-types.md#powershell-workflow-runbooks), поэтому для параметра **Тип Runbook** следует выбрать значение **Рабочий процесс PowerShell**.
5. Щелкните **Создать** , чтобы создать модуль Runbook и открыть текстовый редактор.

## <a name="step-2---add-code-to-the-runbook"></a>Шаг 2. Добавление кода в Runbook
Можно либо напрямую ввести код в модуль Runbook, либо выбрать командлеты, модули Runbook и ресурсы из элемента управления "Библиотека" и добавить их к модулю Runbook с любыми связанными параметрами. В этом пошаговом руководстве вы введете код напрямую в модуль runbook.

1. Созданный модуль runbook пока пустой и содержит лишь обязательное ключевое слово *workflow*, имя и фигурные скобки, в которые будет добавлен весь рабочий процесс.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```
2. Введите *Write-Output "Привет, мир!"* между фигурными скобками.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```
3. Сохраните модуль Runbook, щелкнув **Сохранить**.

## <a name="step-3---test-the-runbook"></a>Шаг 3. Тестирование модуля Runbook
Прежде чем опубликовать модуль runbook и, таким образом, сделать его доступным для рабочей среды, необходимо проверить, правильно ли он работает. Чтобы протестировать модуль Runbook, нужно запустить его **черновую** версию и проверить его работу в интерактивном режиме.

1. Щелкните **Область тестирования**.
2. Щелкните **Пуск** , чтобы начать тестирование. Активным должен быть только этот параметр.
3. При этом создается [задание Runbook](automation-runbook-execution.md) и отображается его состояние.

   Сначала задание получает состояние *В очереди* , показывающее, что оно ожидает доступа к исполнителю Runbook в облаке. Как только рабочая роль запросит задание, оно получит состояние *Запущено*, а после того как модуль runbook начнет выполняться, состояние изменится на *Выполняется*.  

1. Когда задание модуля Runbook будет выполнено, на экране появится результат. В вашем случае отобразится текст *Hello World*.<br><br> ![Привет, мир!](media/automation-first-runbook-textual/test-output-hello-world.png)
2. Закройте область тестирования, чтобы вернуться на холст.

## <a name="step-4---publish-and-start-the-runbook"></a>Шаг 4. Публикация и запуск модуля Runbook
Модуль runbook, который вы только что создали, все еще находится в режиме черновика. Прежде чем запустить модуль в рабочей среде, его нужно опубликовать. При публикации модуля Runbook существующая опубликованная версия перезаписывается черновой версией. У вас пока нет опубликованной версии, так как модуль runbook создан только что.

1. Щелкните **Опубликовать**, чтобы опубликовать модуль Runbook, а затем нажмите кнопку **Да** в появившемся запросе.
2. Если сейчас прокрутить экран влево, чтобы просмотреть runbook на панели **Модули Runbook**, для параметра **Состояние создания** отобразится значение **Опубликовано**.
3. Прокрутите экран вправо до области **MyFirstRunbook-Workflow**.  
   Параметры в верхней части экрана позволяют запустить модуль Runbook, запланировать его запуск в определенное время или создать [Webhook](automation-webhooks.md) , чтобы модуль можно было запускать с помощью HTTP-вызова.
4. Вам нужно только запустить модуль runbook, поэтому нажмите кнопку **Запустить**. Когда появится запрос, нажмите кнопку **Да**.<br><br> ![Запуск модуля Runbook](media/automation-first-runbook-textual/automation-runbook-controls-start.png)
5. Откроется область заданий с созданным заданием runbook. Эту область можно закрыть, но сейчас оставьте ее открытой, чтобы следить за ходом выполнения задания.
6. Состояние задания отображается в поле **Сводка по заданию** и отражает состояния, которые вы наблюдали при тестировании модуля runbook.<br><br> ![Сводные данные задания](media/automation-first-runbook-textual/job-pane-status-blade-jobsummary.png)
7. Как только состояние модуля Runbook изменится на *Выполнено*, щелкните **Выходные данные**. Откроется область "Выходные данные" с текстом *Hello World*.<br><br> ![Сводные данные задания](media/automation-first-runbook-textual/job-pane-status-blade-outputtile.png)  
8. Закройте область выходных данных.
9. Щелкните **Все журналы** , чтобы открыть область "Потоки" для задания Runbook. В потоке вывода должен отображаться только текст *Hello World*, но могут присутствовать и другие потоки заданий runbook, например "Подробные сведения" и "Ошибка", если модуль записывает в них данные.<br><br> ![Сводные данные задания](media/automation-first-runbook-textual/job-pane-status-blade-alllogstile.png)
10. Закройте область потоков и область заданий, чтобы вернуться в область MyFirstRunbook.
11. Щелкните **Задания** , чтобы открыть область "Задания" для этого Runbook. Откроется список всех заданий, созданных этим модулем Runbook. Список должен содержать одно задание, так как вы запустили задание только один раз.<br><br> ![Задания](media/automation-first-runbook-textual/runbook-control-job-tile.png)
12. Если щелкнуть это задание, откроется та же область заданий, что и при запуске модуля runbook. С ее помощью можно вернуться назад и просмотреть сведения о любом задании, созданном для конкретного модуля Runbook.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Шаг 5. Добавление проверки подлинности для управления ресурсами Azure
Вы протестировали и опубликовали свой модуль runbook, но пока он не делает ничего полезного. Нужно, чтобы он управлял ресурсами Azure. Модуль runbook не сможет делать это, пока не пройдет аутентификацию с использованием учетных данных, упомянутых в [предварительных требованиях](#prerequisites). Для этого используйте командлет **Connect-AzureRmAccount**.

1. Откройте текстовый редактор, щелкнув **Изменить** в области MyFirstRunbook-Workflow.
2. Вам больше не потребуется строка **Write-Output**, поэтому удалите ее.
3. Поместите курсор в пустую строку между фигурными скобками.
4. Введите или скопируйте и вставьте следующий код, который выполнит проверку подлинности с помощью учетной записи запуска от имени службы автоматизации:

   ```powershell-interactive
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   ```
5. Щелкните **Тестовая область**, чтобы проверить модуль runbook.
6. Щелкните **Пуск** , чтобы начать тестирование. После его завершения на экране должны отобразиться приблизительно такие основные сведения из вашей учетной записи. Это подтверждает допустимость учетных данных.<br><br> ![Проверка подлинности](media/automation-first-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Шаг 6. Добавление кода запуска виртуальной машины
Теперь, когда модуль runbook прошел аутентификацию в подписке Azure, вы можете управлять ресурсами. Добавьте команду для запуска виртуальной машины. Вы можете выбрать любую виртуальную машину в своей подписке Azure. Имя этой машины будет прописано в модуле runbook.

1. После команды *Connect-AzureRmAccount* введите команду *Start-AzureRmVM -Name 'имя_ВМ' -ResourceGroupName 'имя_группы_ресурсов'*, указав имя виртуальной машины, которую нужно запустить, и имя ее группы ресурсов.  

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   }
   ```
2. Сохраните модуль runbook, а затем щелкните **Тестовая область**, чтобы проверить модуль.
3. Щелкните **Пуск** , чтобы начать тестирование. После завершения теста проверьте, запущена ли виртуальная машина.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>Шаг 7. Добавление входного параметра в модуль Runbook
Созданный модуль runbook сейчас запускает виртуальную машину, указанную в коде runbook, но лучше указать виртуальную машину при запуске runbook. Для этого добавьте в модуль runbook входные параметры.

1. Добавьте значения для параметров *VMName* (Имя ВМ) и *ResourceGroupName* (Имя группы ресурсов) в модуль Runbook и используйте эти переменные в командлете **Start-AzureRmVM**, как показано в примере ниже.

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
    Param(
     [string]$VMName,
     [string]$ResourceGroupName
    )  
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
   }
   ```
2. Сохраните модуль Runbook и откройте область тестирования. Теперь можно указать значения двух входных переменных, которые будут использоваться при тестировании.
3. Закройте область тестирования.
4. Щелкните **Опубликовать** , чтобы опубликовать новую версию модуля Runbook.
5. Остановите виртуальную машину, запущенную на предыдущем этапе.
6. Щелкните **Пуск** , чтобы запустить модуль Runbook. Введите значения **VMName** и **ResourceGroupName** для виртуальной машины, которую нужно запустить.<br><br> ![Start Runbook](media/automation-first-runbook-textual/automation-pass-params.png)<br>  
7. Когда модуль Runbook будет выполнен, проверьте, запустилась ли виртуальная машина.  

## <a name="next-steps"></a>Дополнительная информация
* Чтобы начать работу с графическими модулями Runbook, см. инструкции в статье [Первый графический Runbook](automation-first-runbook-graphical.md).
* Чтобы начать работу с модулями Runbook PowerShell, см. инструкции в статье [Мой первый модуль Runbook PowerShell](automation-first-runbook-textual-powershell.md).
* Дополнительные сведения о типах модулей Runbook, их преимуществах и ограничениях см. в статье [Типы модулей Runbook в службе автоматизации Azure](automation-runbook-types.md).
* Дополнительные сведения о функции поддержки скриптов PowerShell см. в статье [Announcing Native PowerShell Script Support in Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/) (Встроенная поддержка скриптов PowerShell в службе автоматизации Azure).
