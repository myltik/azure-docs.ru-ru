---
title: Мой первый модуль runbook PowerShell в службе автоматизации Azure
description: Учебник, в котором описывается создание, тестирование и публикация простого модуля Runbook PowerShell.
keywords: Azure PowerShell, руководство по скриптам PowerShell, инструменты автоматизации PowerShell
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9fe9d98b694b8c42f3342e615d92fae9824dca26
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34195154"
---
# <a name="my-first-powershell-runbook"></a>Мой первый модуль Runbook PowerShell

> [!div class="op_single_selector"]
> * [Графический](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Рабочий процесс PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

В этом руководстве описана процедура создания [модуля Runbook PowerShell](automation-runbook-types.md#powershell-runbooks) в службе автоматизации Azure. Сначала вы протестируете и опубликуете простой модуль runbook и узнаете, как отслеживать состояние его заданий. Затем мы изменим модуль runbook, настроив его для фактического управления ресурсами Azure (в нашем примере это запуск виртуальной машины Azure). Затем вы сделаете этот модуль runbook еще надежнее, добавив параметры runbook.

## <a name="prerequisites"></a>предварительным требованиям
Для работы с этим учебником требуется:

* Подписка Azure. Если у вас ее нет, [активируйте преимущества для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) или [зарегистрируйте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Учетная запись службы автоматизации](automation-offering-get-started.md) , чтобы хранить модуль Runbook и выполнять проверку подлинности ресурсов Azure. Эта учетная запись должна иметь разрешение на запуск и остановку виртуальной машины.
* Виртуальная машина Azure. Это не должна быть рабочая виртуальная машина, так как в процессе изучения этого руководства ее нужно будет остановить и запустить заново.

## <a name="step-1---create-new-runbook"></a>Шаг 1. Создание нового модуля Runbook
Для начала создайте простой модуль runbook, выводящий на экран текст *Hello World*.

1. На портале Azure выберите свою учетную запись службы автоматизации.

   Страница учетной записи в службе автоматизации позволяет быстро получить представление о ресурсах, доступных в этой учетной записи. Некоторые ресурсы уже должны быть доступны. Большинство из них — это модули, которые добавляются в каждую новую учетную запись в службе автоматизации по умолчанию. Кроме того, вам потребуется ресурс учетных данных, упомянутый в [предварительных требованиях](#prerequisites).

1. Выберите **Модули Runbook** в разделе **Автоматизация процессов**, чтобы открыть список модулей.
2. Создайте модуль runbook. Для этого нажмите кнопку **+ Добавить Runbook**, а затем выберите **Создать новый Runbook**.
3. Присвойте модулю Runbook имя *MyFirstRunbook-PowerShell*.
4. В этом случае вы создадите [модуль runbook PowerShell](automation-runbook-types.md#powershell-runbooks), поэтому для параметра **Тип Runbook** выберите значение **PowerShell**.
5. Щелкните **Создать** , чтобы создать модуль Runbook и открыть текстовый редактор.

## <a name="step-2---add-code-to-the-runbook"></a>Шаг 2. Добавление кода в Runbook
Можно либо напрямую ввести код в модуль Runbook, либо выбрать командлеты, модули Runbook и ресурсы из элемента управления "Библиотека" и добавить их к модулю Runbook с любыми связанными параметрами. В этом пошаговом руководстве вы введете код напрямую в модуль runbook.

1. Сейчас модуль runbook пуст, поэтому введите *Write-Output "Hello World"*. в текст скрипта.<br><br> ![Привет, мир!](media/automation-first-runbook-textual-powershell/automation-helloworld.png)  
2. Сохраните модуль Runbook, щелкнув **Сохранить**.

## <a name="step-3---test-the-runbook"></a>Шаг 3. Тестирование модуля Runbook
Прежде чем опубликовать модуль runbook и, таким образом, сделать его доступным для рабочей среды, необходимо проверить, правильно ли он работает. Чтобы протестировать модуль Runbook, нужно запустить его **черновую** версию и проверить его работу в интерактивном режиме.

1. Щелкните **Область тестирования**.
2. Щелкните **Пуск** , чтобы начать тестирование. Активным должен быть только этот параметр.
3. При этом создается [задание Runbook](automation-runbook-execution.md) и отображается его состояние.

   Сначала задание получает состояние *В очереди*, которое означает, что задание ожидает доступа к рабочей роли runbook в облаке. Как только рабочая роль запросит задание, оно получит состояние *Запущено*, а после того как модуль runbook начнет выполняться, состояние изменится на *Выполняется*.  

1. Когда задание модуля Runbook будет выполнено, на экране появится результат. В вашем случае отобразится текст *Hello World*.<br><br> ![Вывод области тестирования](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)  
2. Закройте область тестирования, чтобы вернуться на холст.

## <a name="step-4---publish-and-start-the-runbook"></a>Шаг 4. Публикация и запуск модуля Runbook
Модуль runbook, который вы только что создали, все еще находится в режиме черновика. Прежде чем запустить модуль в рабочей среде, его нужно опубликовать.  При публикации модуля Runbook существующая опубликованная версия перезаписывается черновой версией. У вас пока нет опубликованной версии, так как модуль runbook создан только что.

1. Щелкните **Опубликовать**, чтобы опубликовать модуль Runbook, а затем нажмите кнопку **Да** в появившемся запросе.
2. Если сейчас прокрутить экран влево, чтобы просмотреть runbook на панели **Модули Runbook**, для параметра **Состояние создания** отобразится значение **Опубликовано**.
3. Прокрутите экран вправо до области **MyFirstRunbook-PowerShell**.  
   Параметры в верхней части экрана позволяют запускать и просматривать модуль runbook, назначать его запуск в определенное время в будущем, а также создавать вызовы [webhook](automation-webhooks.md), чтобы запускать модуль с помощью HTTP-вызова.
4. Нужно запустить модуль runbook, поэтому щелкните **Запустить**, а затем, когда откроется страница "Запуск Runbook", нажмите кнопку **ОК**.
5. Откроется страница заданий с созданным заданием runbook. Эту область можно закрыть, но пока оставьте ее открытой, чтобы следить за ходом выполнения задания.
6. Состояние задания отображается в поле **Сводка по заданию** и отражает состояния, которые вы наблюдали при тестировании модуля runbook.<br><br> ![Сводные данные задания](media/automation-first-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)<br>  
7. Когда в разделе **Обзор** состояние модуля runbook изменится на *Выполнено*, щелкните **Выходные данные**. Откроется область "Выходные данные" с текстом *Hello World*.<br><br> ![Выходные данные задания](media/automation-first-runbook-textual-powershell/job-pane-status-blade-outputtile.png)<br> 
8. Закройте страницу выходных данных.
9. Щелкните **Все журналы** , чтобы открыть область "Потоки" для задания Runbook. В потоке вывода должен отображаться только текст *Hello World*, но могут присутствовать и другие потоки заданий runbook, например "Подробные сведения" и "Ошибка", если модуль записывает в них какие-то данные.<br><br> ![Все журналы](media/automation-first-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)<br>   
10. Закройте страницы "Потоки" и "Задание", чтобы вернуться на страницу MyFirstRunbook-PowerShell.
11. В разделе **Сведения** щелкните **Задания**, чтобы открыть область "Задания" для этого модуля runbook. Откроется список всех заданий, созданных этим модулем Runbook. В нем должно быть только одно задание, так как вы запустили задание только один раз.<br><br> ![Список заданий](media/automation-first-runbook-textual-powershell/runbook-control-job-tile.png)  
12. Если щелкнуть это задание, откроется та же область заданий, что и при запуске модуля runbook. С ее помощью можно вернуться назад и просмотреть сведения о любом задании, созданном для конкретного модуля Runbook.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Шаг 5. Добавление проверки подлинности для управления ресурсами Azure
Вы протестировали и опубликовали свой модуль runbook, но пока он не выполняет никаких полезных действий. Нужно, чтобы он управлял ресурсами Azure. Это станет возможным, только когда модуль runbook пройдет проверку подлинности с использованием учетных данных, указанных в [предварительных требованиях](#prerequisites). Для этого используйте командлет **Connect-AzureRmAccount**.

1. Откройте текстовый редактор, щелкнув **Изменить** на странице MyFirstRunbook-PowerShell.
2. Вам больше не потребуется использовать строку **Write-Output**, поэтому удалите ее.
3. Введите или скопируйте и вставьте следующий код, который выполнит проверку подлинности с помощью учетной записи запуска от имени службы автоматизации:
   
   ```
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   ```
   <br>
4. Щелкните **Тестовая область**, чтобы проверить модуль runbook.
5. Щелкните **Пуск** , чтобы начать тестирование. После его завершения на экране должны отобразиться приблизительно такие основные сведения из вашей учетной записи. Это подтверждает допустимость учетных данных.<br><br> ![Проверка подлинности](media/automation-first-runbook-textual-powershell/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Шаг 6. Добавление кода запуска виртуальной машины
Теперь, когда модуль runbook прошел аутентификацию в подписке Azure, вы можете управлять ресурсами. Добавьте команду для запуска виртуальной машины. Вы можете выбрать любую виртуальную машину в своей подписке Azure. Имя этой машины вы пропишете в модуле runbook.

1. После команды *Connect-AzureRmAccount* введите команду *Start-AzureRmVM -Name 'имя_ВМ' -ResourceGroupName 'имя_группы_ресурсов'*, указав имя виртуальной машины, которую нужно запустить, и имя ее группы ресурсов.  
   
   ```
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   ```
   <br>
2. Сохраните модуль runbook, а затем щелкните **Тестовая область**, чтобы проверить модуль.
3. Щелкните **Пуск** , чтобы начать тестирование. После завершения теста проверьте, запущена ли виртуальная машина.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>Шаг 7. Добавление входного параметра в модуль Runbook
Созданный модуль runbook сейчас запускает виртуальную машину, указанную в коде runbook, но лучше указать виртуальную машину при запуске runbook. Для этого добавьте в модуль runbook входные параметры.

1. Добавьте значения для параметров *VMName* и *ResourceGroupName* в модуле runbook и используйте эти переменные в командлете **Start-AzureRmVM**, как показано в следующем примере.

   ```
   Param(
    [string]$VMName,
    [string]$ResourceGroupName
   )
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
   ```
   <br>
1. Сохраните модуль Runbook и откройте область тестирования. Теперь можно указать значения двух входных переменных, которые будут использоваться в тесте.
2. Закройте область тестирования.
3. Щелкните **Опубликовать** , чтобы опубликовать новую версию модуля Runbook.
4. Остановите виртуальную машину, запущенную на предыдущем этапе.
5. Нажмите кнопку **ОК**, чтобы запустить модуль Runbook. Введите значения **VMName** и **ResourceGroupName** для виртуальной машины, которую нужно запустить.<br><br> ![Передача параметра](media/automation-first-runbook-textual-powershell/automation-pass-params.png)<br>  
6. Когда модуль Runbook будет выполнен, проверьте, запустилась ли виртуальная машина.

## <a name="differences-from-powershell-workflow"></a>Отличия от рабочего процесса PowerShell
Модули runbook PowerShell имеют тот же жизненный цикл, возможности и средства управления, что и модули runbook рабочих процессов PowerShell, но существуют некоторые различия и ограничения.

1. Модули Runbook PowerShell выполняются быстрее (по сравнению с модулями Runbook рабочих процессов PowerShell), так как они не имеют этапа компиляции.
2. Модули Runbook PowerShell поддерживают контрольные точки, с помощью которых их выполнение можно возобновить с любого момента, тогда как модули Runbook рабочих процессов PowerShell можно возобновить только с начала.
3. Модули Runbook PowerShell поддерживают параллельное и последовательное выполнение, тогда как модули Runbook рабочих процессов PowerShell могут выполнять команды только последовательно.
4. В модуле runbook рабочего процесса PowerShell действие, команда или блок скрипта могут иметь свои собственные пространства выполнения, тогда как в модуле runbook PowerShell все содержимое скрипта выполняется в одном пространстве выполнения. Кроме того, существуют некоторые [синтаксические различия](https://technet.microsoft.com/magazine/dn151046.aspx) между собственным модулем Runbook PowerShell и модулем Runbook рабочих процессов PowerShell.

## <a name="next-steps"></a>Дополнительная информация
* Чтобы начать работу с графическими модулями Runbook, см. инструкции в статье [Первый графический Runbook](automation-first-runbook-graphical.md).
* Чтобы приступить к работе с модулями Runbook рабочих процессов PowerShell, обратитесь к статье [Мой первый модуль Runbook рабочего процесса PowerShell](automation-first-runbook-textual.md)
* Чтобы получить дополнительные сведения о типах модулей Runbook, их преимуществах и ограничениях, обратитесь к статье [Типы модулей Runbook в службе автоматизации Azure](automation-runbook-types.md)
* Дополнительные сведения о функции поддержки скриптов PowerShell см. в статье [Announcing Native PowerShell Script Support in Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/) (Встроенная поддержка скриптов PowerShell в службе автоматизации Azure).

