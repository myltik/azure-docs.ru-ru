<properties 
	pageTitle="Тестирование модуля Runbook в службе автоматизации Azure"
	description="Перед публикацией модуля Runbook в службе автоматизации Azure его можно протестировать и проверить, работает ли он должным образом. В этой статье описывается тестирование модулей Runbook и просмотр его выходных данных."
	services="automation"
	documentationCenter=""
	authors="bwren"
	manager="stevenka"
	editor="tysonn" />
<tags 
	ms.service="automation"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="09/23/2015"
	ms.author="bwren" />

# Тестирование модуля Runbook в службе автоматизации Azure
При тестировании модуля Runbook запускается его [черновая версия](automation-creating-importing-runbook.md#publishing-a-runbook) и завершаются все действия, которые он выполняет. Журнал заданий не создается, однако в области вывода теста отображаются потоки [Выходные данные](automation-runbook-output-and-messages.md#output-stream) и [Предупреждения и ошибки](automation-runbook-output-and-messages.md#message-streams). Сообщения, предназначенные для [подробного потока](automation-runbook-output-and-messages.md#message-streams), отображаются в области выходных данных, только если [ переменная $VerbosePreference](automation-runbook-output-and-messages.md#preference-variables) имеет значение Continue.

Несмотря на то что выполняется черновая версия, модуль Runbook выполняет рабочий процесс в обычном режиме и выполняет все действия с использованием ресурсов среды. В связи с этим тестировать модули Runbook можно только в непроизводственных ресурсах.

Процедура тестирования для всех [типов модулей Runbook](automation-runbook-types.md) одна и та же и выполняется одинаково и в текстовом, и в графическом редакторе на портале предварительной версии Azure.


## Тестирование модуля Runbook на портале предварительной версии Azure

На портале Azure предварительной версии можно работать с любыми [типами модулей Runbook](automation-runbook-types.md).

1. Откройте черновую версию модуля Runbook либо в [текстовом](automation-editing-a-runbook#Portal), либо в [графическом редакторе](automation-graphical-authoring-intro.md).
2. Нажмите кнопку **Тест**, чтобы открыть колонку «Тест».
3. Если модуль Runbook имеет параметры, они отображаются в левой области, где можно указать значения для теста.
4. Если вы хотите запустить тест в [гибридной рабочей роли Runbook](automation-hybrid), измените **Параметры запуска** на **Гибридную роль** и выберите имя целевой группы. В противном случае оставьте значение **Azure** по умолчанию, чтобы тест выполнялся в облаке.
5. Нажмите кнопку **Запуск**, чтобы запустить тест.
6. Если модуль Runbook является [модулем рабочего процесса PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) или [графическим](automation-runbook-types.md#graphical-runbooks), вы можете остановить или приостановить его в процессе тестирования с помощью кнопок под областью выходных данных. В случае приостановки модуль Runbook завершает действие, начатое до приостановки. Приостановленный модуль Runbook можно остановить или перезапустить.
7. Проверьте выходные данные модуля Runbook в области выходных данных.



## Тестирование модуля Runbook на портале Azure

На портале Azure можно работать только с [модулями Runbook рабочих процессов PowerShell](automation-runbook-types.md#powershell-workflow-runbooks).


1. [Откройте черновую версию модуля Runbook](automation-edit-textual-runbook.md#to-edit-a-runbook-with-the-azure-portal).
2. Нажмите кнопку **Тест**, чтобы запустить тест. Если модуль Runbook имеет параметры, откроется диалоговое окно для указания значений, необходимых для проведения теста.
6. Тестируемый модуль Runbook можно остановить или приостановить с помощью кнопок под областью выходных данных. В случае приостановки модуль Runbook завершает действие, начатое до приостановки. Приостановленный модуль Runbook можно остановить или перезапустить.
7. Проверьте выходные данные модуля Runbook в области выходных данных.


## Связанные статьи

- [Создание или импорт модуля Runbook в службе автоматизации Azure](automation-creating-importing-runbook.md)
- [Графические модули Runbook в службе автоматизации Azure](automation-graphical-authoring-intro.md)
- [Изменение текстовых модулей Runbook в службе автоматизации Azure](automation-edit-textual-runbook.md)
- [Выходные данные и сообщения Runbook в службе автоматизации Azure](automation-runbook-output-and-messages.md)

<!---HONumber=Oct15_HO1-->