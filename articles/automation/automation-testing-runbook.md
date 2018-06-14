---
title: Тестирование модуля Runbook в службе автоматизации Azure
description: Перед публикацией модуля Runbook в службе автоматизации Azure его можно протестировать и проверить, работает ли он должным образом.  В этой статье описывается тестирование модулей Runbook и просмотр его выходных данных.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ebeaa8eb75373fc94f7e4e714e36d1167fd7f060
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34192104"
---
# <a name="testing-a-runbook-in-azure-automation"></a>Тестирование модуля Runbook в службе автоматизации Azure
При тестировании модуля Runbook запускается его [черновая версия](automation-creating-importing-runbook.md#publishing-a-runbook) и завершаются все действия, которые он выполняет. Журнал заданий не создается, однако в области вывода теста отображаются потоки [выходных данных](automation-runbook-output-and-messages.md#output-stream) и [предупреждений и ошибок](automation-runbook-output-and-messages.md#message-streams). Сообщения, предназначенные для [подробного потока](automation-runbook-output-and-messages.md#message-streams), отображаются в области выходных данных, только если [переменная $VerbosePreference](automation-runbook-output-and-messages.md#preference-variables) имеет значение Continue.

Несмотря на то что выполняется черновая версия, модуль Runbook выполняет рабочий процесс в обычном режиме и выполняет все действия с использованием ресурсов среды. В связи с этим тестировать модули Runbook можно только в непроизводственных ресурсах.

Процедура тестирования для всех [типов модулей Runbook](automation-runbook-types.md) одна и та же и выполняется одинаково и в текстовом, и в графическом редакторе на портале Azure.  

## <a name="to-test-a-runbook-in-the-azure-portal"></a>Тестирование модуля Runbook на портале Azure
На портале Azure можно работать с любыми [типами модулей Runbook](automation-runbook-types.md) .

1. Откройте черновую версию модуля Runbook в [текстовом](automation-edit-textual-runbook.md) или [графическом редакторе](automation-graphical-authoring-intro.md).
2. Нажмите кнопку **Тест** , чтобы открыть колонку «Тест».
3. Если модуль Runbook имеет параметры, они отображаются в левой области, где можно указать значения для теста.
4. Если вы хотите запустить тест в [гибридной рабочей роли Runbook](automation-hybrid-runbook-worker.md), то измените **Параметры запуска** на **Гибридную рабочую роль** и выберите имя целевой группы.  В противном случае оставьте значение **Azure** по умолчанию, чтобы тест выполнялся в облаке.
5. Нажмите кнопку **Запуск** , чтобы запустить тест.
6. Если модуль Runbook является [модулем рабочего процесса PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) или [графическим](automation-runbook-types.md#graphical-runbooks), то вы можете остановить или приостановить его в процессе тестирования с помощью кнопок под областью выходных данных. В случае приостановки модуль Runbook завершает действие, начатое до приостановки. Приостановленный модуль Runbook можно остановить или перезапустить.
7. Проверьте выходные данные модуля Runbook в области выходных данных.

## <a name="next-steps"></a>Дальнейшие действия
* Инструкции по созданию и импорту модуля Runbook см. в статье [Создание или импорт модуля Runbook в службе автоматизации Azure](automation-creating-importing-runbook.md).
* Дополнительные сведения о графической разработке см. в статье [Графическая разработка в службе автоматизации Azure](automation-graphical-authoring-intro.md).
* Чтобы приступить к работе с модулями Runbook рабочих процессов PowerShell, обратитесь к статье [Мой первый модуль Runbook рабочего процесса PowerShell](automation-first-runbook-textual.md)
* Дополнительные сведения о настройке модулей runbook для возврата ошибок и сообщений о состоянии, включая рекомендации, см. в статье [Выходные данные и сообщения Runbook в службе автоматизации Azure](automation-runbook-output-and-messages.md).

