---
title: Настройка Azure Machine Learning Workbench для работы с IDE  | Документация Майкрософт
description: Руководство по настройке Azure Machine Learning Workbench для работы с IDE
services: machine-learning
author: svankam
ms.author: svankam
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: 1b43671b40e3244690371669fb60796f75174df0
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34834912"
---
# <a name="how-to-configure-azure-machine-learning-workbench-to-work-with-an-ide"></a>Настройка Azure Machine Learning Workbench для работы с IDE 

Azure Machine Learning Workbench можно настроить для работы с популярными IDE (интегрированная среда разработки) Python. Это позволяет обеспечить беспрепятственную обработку и анализ данных в процессе подготовки данных, разработки кода, отслеживания запуска и эксплуатации. Сейчас поддерживаются следующие интегрированные среды разработки:
- Microsoft Visual Studio Code; 
- JetBrain PyCharm. 

## <a name="configure-workbench"></a>Настройка Workbench
1. Выберите меню **Файл** в верхнем левом углу предложения. 
2. Выберите вариант **Configure Project IDE** (Настройка проектной IDE) во всплывающем элементе. 
3. Введите `VS Code` или `PyCharm` в поле **Имя** (имя может быть произвольным).
4. Введите расположение исполняемого файла IDE (полное имя исполняемого файла и расширение) в поле **Путь выполнения**.

### <a name="default-install-path-for-visual-studio-code"></a>Путь установки по умолчанию для Visual Studio Code.  

* 32-разрядная версия Windows — `C:\Program Files (x86)\Microsoft VS Code\Code.exe`.
* 64-разрядная версия Windows — `C:\Program Files\Microsoft VS Code\Code.exe`.
* macOS — выберите путь к APP-файлу, например `/Applications/Visual Studio Code.app`, а затем приложение добавит остаток пути. Полный путь к исполняемому файлу по умолчанию — `/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code`. Если команда `Shell Command: Install 'code' command in PATH` выполняется в VS Code, тогда также можно создать ссылку на скрипт VS Code в `/usr/local/bin/code`.

### <a name="default-install-path-for-pycharm"></a>Путь установки по умолчанию в PyCharm 

* 32-разрядная версия Windows — `C:\Program Files (x86)\JetBrains\PyCharm Community Edition 2017.2.1\bin\pycharm.exe`. 
* 64-разрядная версия Windows — `C:\Program Files\JetBrains\PyCharm Community Edition 2017.2.1\bin\pycharm64.exe`.
* macOS — выберите путь к APP-файлу, например /Applications/PyCharm CE.app, а затем приложение добавит остаток пути. Полный путь к исполняемому файлу по умолчанию — `/Applications/PyCharm CE.app/Contents/MacOS/pycharm`. Кроме того, PyCharm можно найти в папке bin, `/usr/local/bin/charm`.

## <a name="open-project-in-ide"></a>Открытие проекта в IDE 
Завершив настройку, откройте проект службы "Машинное обучение Azure", открыв меню **Файл** в Azure Machine Learning Workbench и выбрав **Открыть проект (<имя_IDE>)**. Откроется текущий активный проект в настроенной среде IDE. _Примечание. Если проект не выбран, параметр **Открыть проект (<имя_IDE>)** будет отключен._

## <a name="configuring-the-integrated-terminal-in-visual-studio-code"></a>Настройка интегрированного терминала в Visual Studio Code

### <a name="windows"></a>Windows 
Мы переопределили оболочку по умолчанию и выбрали cmd вместо PowerShell. Если щелкнуть **Открыть проект (<имя_IDE>)**, появится запрос: 

_Do you allow shell: `C:\windows\System32\cmd.exe` (defined as a workspace setting) to be launched in the terminal?_ (Разрешаете ли вы использование оболочки C:\windows\System32\cmd.exe (определена как параметр рабочей области) в терминале?)

Выберите `yes`, чтобы позволить настроить среду для эффективной работы с интерфейсом командной строки Azure ML Workbench.

### <a name="mac"></a>Mac
Для выполнения команды `az` с помощью встроенного терминала Visual Studio Code на компьютере Mac необходимо вручную задать для `PATH` то же значение, что и для `PATH` в разделе `terminal.integrated.env.osx` файла `.vscode/settings.json` проекта. Для этого выполните следующую команду в окне терминала: `PATH=<PATH in .vscode/settings>`.
