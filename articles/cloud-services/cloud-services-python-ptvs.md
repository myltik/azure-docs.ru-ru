---
title: Начало работы с облачными службами Azure и Python | Документация Майкрософт
description: Обзор использования Python Tools в Visual Studio для создания облачных служб Azure, включая веб-роли и рабочие роли.
services: cloud-services
documentationcenter: python
author: thraka
manager: timlt
editor: ''
ms.assetid: 5489405d-6fa9-4b11-a161-609103cbdc18
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: hero-article
ms.date: 07/18/2017
ms.author: adegeo
ms.openlocfilehash: f6bd89c160387abbb2b0339a5a5f62d998c0c84e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33765421"
---
# <a name="python-web-and-worker-roles-with-python-tools-for-visual-studio"></a>Использование веб-ролей и рабочих ролей Python с помощью средств Python для Visual Studio

В этой статье представлен обзор способов использования веб-ролей и рабочих ролей Python с помощью [инструментов Python для Visual Studio][Python Tools for Visual Studio]. Узнайте, как с помощью Visual Studio создать и развернуть базовую облачную службу, которая использует Python.

## <a name="prerequisites"></a>предварительным требованиям
* [Visual Studio 2013, 2015 или 2017](https://www.visualstudio.com/)
* [Инструменты Python для Visual Studio][Python Tools for Visual Studio] (PTVS)
* [Инструменты пакета SDK для Azure для VS 2013][Azure SDK Tools for VS 2013] или  
[Инструменты пакета SDK для Azure для VS 2015][Azure SDK Tools for VS 2015] или  
[Инструменты пакета SDK для Azure для VS 2017][Azure SDK Tools for VS 2017]
* [Python 2.7 (32-разрядная версия)][Python 2.7 32-bit] или [Python 3.5 (32-разрядная версия)][Python 3.5 32-bit]

[!INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

## <a name="what-are-python-web-and-worker-roles"></a>Что такое веб-роли и рабочие роли Python?
Azure предоставляет три вычислительные модели запуска приложений: [веб-приложения в службе приложений Azure][execution model-web sites], [виртуальные машины Azure][execution model-vms] и [облачные службы Azure][execution model-cloud services]. Все три модели поддерживают Python. Облачные службы, которые включают в себя веб-роли и рабочие роли, предоставляют *платформу как службу (PaaS)*. В рамках облачной службы веб-роль предоставляет выделенный веб-сервер IIS для размещения внешних веб-приложений, тогда как рабочая роль может выполнять асинхронные, долгосрочные или постоянные задачи, не зависящие от пользовательских действий и пользовательского ввода.

Дополнительные сведения см. в разделе [Информация об облачных службах].

> [!NOTE]
> *Требуется создать простой веб-сайт?*
> Если вам необходимо создать сайт с простым внешним интерфейсом, воспользуйтесь упрощенными веб-приложениями в службе приложений Azure. По мере роста веб-сайта и изменения требований можно легко выполнить обновление к облачной службе. В <a href="/develop/python/">Центре разработчиков Python</a> можно найти статьи, посвященные разработке веб-приложений в службе приложений Azure.
> <br />
> 
> 

## <a name="project-creation"></a>Создание проекта
В Visual Studio в диалоговом окне **Новый проект** в разделе **Python** выберите **Облачная служба Azure**.

![Диалоговое окно "Новый проект"](./media/cloud-services-python-ptvs/new-project-cloud-service.png)

В мастере настройки облачной службы Azure можно создать новую веб-роль и рабочие роли.

![Диалоговое окно Облачной службы Azure](./media/cloud-services-python-ptvs/new-service-wizard.png)

Шаблон рабочей роли входит в состав стандартного кода для подключения к учетной записи хранения Azure или к служебной шине Azure.

![Решение для Облачной службы](./media/cloud-services-python-ptvs/worker.png)

Добавить веб-роль или рабочую роль к уже существующей облачной службе можно в любое время.  Также можно добавить к собственному решению существующий проект или создать новые проекты.

![Команда добавления роли](./media/cloud-services-python-ptvs/add-new-or-existing-role.png)

В облачной службе могут присутствовать роли, реализованные на различных языках программирования.  Например, это может быть веб-роль на Python, реализованная с помощью Django, с рабочими ролями на Python или C#.  С помощью очередей служебной шины или очередей хранилища можно легко обмениваться данными между ролями.

## <a name="install-python-on-the-cloud-service"></a>Установка Python в облачной службе
> [!WARNING]
> Скрипты установки, которые устанавливаются вместе с Visual Studio (на момент последнего обновления этой статьи), не работают. В этом разделе описывается обходное решение.
> 
> 

Основная проблема скриптов установки заключается в том, что они не устанавливают Python. Сначала определите две [задачи запуска](cloud-services-startup-tasks.md) в файле [ServiceDefinition.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef). Первая задача (**PrepPython.ps1**) скачивает и устанавливает среду выполнения Python. Вторая задача (**PipInstaller.ps1**) запускает PIP, чтобы установить зависимости.

Приведенные ниже скрипты написаны для Python 3.5. Если нужно использовать Python версии 2.x, в файле переменной **PYTHON2** установите значение **on** для двух задач запуска и задачи выполнения: `<Variable name="PYTHON2" value="<mark>on</mark>" />`.

```xml
<Startup>

  <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PrepPython.ps1">
    <Environment>
      <Variable name="EMULATED">
        <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
      </Variable>
      <Variable name="PYTHON2" value="off" />
    </Environment>
  </Task>

  <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PipInstaller.ps1">
    <Environment>
      <Variable name="EMULATED">
        <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
      </Variable>
      <Variable name="PYTHON2" value="off" />
    </Environment>

  </Task>

</Startup>
```

В задачу запуска рабочей роли необходимо добавить переменные **PYTHON2** и **PYPATH**. Переменная **PYPATH** используется, только если для переменной **PYTHON2** установлено значение **on**.

```xml
<Runtime>
  <Environment>
    <Variable name="EMULATED">
      <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
    </Variable>
    <Variable name="PYTHON2" value="off" />
    <Variable name="PYPATH" value="%SystemDrive%\Python27" />
  </Environment>
  <EntryPoint>
    <ProgramEntryPoint commandLine="bin\ps.cmd LaunchWorker.ps1" setReadyOnProcessStart="true" />
  </EntryPoint>
</Runtime>
```

#### <a name="sample-servicedefinitioncsdef"></a>Пример файла ServiceDefinition.csdef
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="AzureCloudServicePython" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRole1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
      <Setting name="Python2" />
    </ConfigurationSettings>
    <Startup>
      <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PrepPython.ps1">
        <Environment>
          <Variable name="EMULATED">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
          <Variable name="PYTHON2" value="off" />
        </Environment>
      </Task>
      <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PipInstaller.ps1">
        <Environment>
          <Variable name="EMULATED">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
          <Variable name="PYTHON2" value="off" />
        </Environment>
      </Task>
    </Startup>
    <Runtime>
      <Environment>
        <Variable name="EMULATED">
          <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
        </Variable>
        <Variable name="PYTHON2" value="off" />
        <Variable name="PYPATH" value="%SystemDrive%\Python27" />
      </Environment>
      <EntryPoint>
        <ProgramEntryPoint commandLine="bin\ps.cmd LaunchWorker.ps1" setReadyOnProcessStart="true" />
      </EntryPoint>
    </Runtime>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
  </WorkerRole>
</ServiceDefinition>
```



Далее в папке **./bin** своей роли создайте файлы **PrepPython.ps1** и **PipInstaller.ps1**.

#### <a name="preppythonps1"></a>Файл PrepPython.ps1
Скрипт в этом файле устанавливает Python. Если для переменной среды **PYTHON2** задано значение **on**, будет установлена версия Python 2.7. В противном случае будет установлена версия Python 3.5.

```powershell
[Net.ServicePointManager]::SecurityProtocol = "tls12, tls11, tls"
$is_emulated = $env:EMULATED -eq "true"
$is_python2 = $env:PYTHON2 -eq "on"
$nl = [Environment]::NewLine

if (-not $is_emulated){
    Write-Output "Checking if python is installed...$nl"
    if ($is_python2) {
        & "${env:SystemDrive}\Python27\python.exe"  -V | Out-Null
    }
    else {
        py -V | Out-Null
    }

    if (-not $?) {

        $url = "https://www.python.org/ftp/python/3.5.2/python-3.5.2-amd64.exe"
        $outFile = "${env:TEMP}\python-3.5.2-amd64.exe"

        if ($is_python2) {
            $url = "https://www.python.org/ftp/python/2.7.12/python-2.7.12.amd64.msi"
            $outFile = "${env:TEMP}\python-2.7.12.amd64.msi"
        }

        Write-Output "Not found, downloading $url to $outFile$nl"
        Invoke-WebRequest $url -OutFile $outFile
        Write-Output "Installing$nl"

        if ($is_python2) {
            Start-Process msiexec.exe -ArgumentList "/q", "/i", "$outFile", "ALLUSERS=1" -Wait
        }
        else {
            Start-Process "$outFile" -ArgumentList "/quiet", "InstallAllUsers=1" -Wait
        }

        Write-Output "Done$nl"
    }
    else {
        Write-Output "Already installed"
    }
}
```

#### <a name="pipinstallerps1"></a>Файл PipInstaller.ps1
Этот скрипт вызывает PIP и устанавливает все зависимости в файле **requirements.txt**. Если для переменной среды **PYTHON2** задано значение **on**, будет использоваться Python 2.7. В противном случае будет использоваться Python 3.5.

```powershell
$is_emulated = $env:EMULATED -eq "true"
$is_python2 = $env:PYTHON2 -eq "on"
$nl = [Environment]::NewLine

if (-not $is_emulated){
    Write-Output "Checking if requirements.txt exists$nl"
    if (Test-Path ..\requirements.txt) {
        Write-Output "Found. Processing pip$nl"

        if ($is_python2) {
            & "${env:SystemDrive}\Python27\python.exe" -m pip install -r ..\requirements.txt
        }
        else {
            py -m pip install -r ..\requirements.txt
        }

        Write-Output "Done$nl"
    }
    else {
        Write-Output "Not found$nl"
    }
}
```

#### <a name="modify-launchworkerps1"></a>Изменение файла LaunchWorker.ps1
> [!NOTE]
> В проекте **рабочей роли** файл **LauncherWorker.ps1** необходим для выполнения файла запуска. В проекте **веб-роли** файл запуска определяется в свойствах проекта.
> 
> 

Изначально файл **bin\LaunchWorker.ps1** создавался для выполнения множества подготовительных задач. Но в действительности он этого не делает. Замените содержимое этого файла на приведенный ниже скрипт.

Этот скрипт вызывает файл **worker.py** из проекта Python. Если для переменной среды **PYTHON2** задано значение **on**, будет использоваться Python 2.7. В противном случае будет использоваться Python 3.5.

```powershell
$is_emulated = $env:EMULATED -eq "true"
$is_python2 = $env:PYTHON2 -eq "on"
$nl = [Environment]::NewLine

if (-not $is_emulated)
{
    Write-Output "Running worker.py$nl"

    if ($is_python2) {
        cd..
        iex "$env:PYPATH\python.exe worker.py"
    }
    else {
        cd..
        iex "py worker.py"
    }
}
else
{
    Write-Output "Running (EMULATED) worker.py$nl"

    # Customize to your local dev environment

    if ($is_python2) {
        cd..
        iex "$env:PYPATH\python.exe worker.py"
    }
    else {
        cd..
        iex "py worker.py"
    }
}
```

#### <a name="pscmd"></a>Файл ps.cmd
При выполнении шаблонов Visual Studio в папке **./bin** должен быть создан файл **ps.cmd**. Этот скрипт оболочки вызывает приведенные выше скрипты оболочки PowerShell и обеспечивает ведение журнала на основе имени вызванной оболочки PowerShell. Если этот файл не создан, вот данные, которые должны в нем содержаться: 

```bat
@echo off

cd /D %~dp0

if not exist "%DiagnosticStore%\LogFiles" mkdir "%DiagnosticStore%\LogFiles"
%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe -ExecutionPolicy Unrestricted -File %* >> "%DiagnosticStore%\LogFiles\%~n1.txt" 2>> "%DiagnosticStore%\LogFiles\%~n1.err.txt"
```



## <a name="run-locally"></a>Локальный запуск
Если настроить проект облачной службы в качестве запускаемого проекта и нажать клавишу F5, эта облачная служба запустится в локальном эмуляторе Azure.

Хотя PTVS поддерживает возможность запуска в эмуляторе, процедура отладки (например, установка точек останова) в этом режиме не работает.

Чтобы отладить веб-роли и рабочие роли, необходимо установить в настройках проекта роли параметр, определяющий его запуск при включении, и выполнить отладку таким образом.  В качестве запускаемых при включении можно указать несколько проектов.  Щелкните решение правой кнопкой мыши и выберите пункт **Назначить запускаемые проекты**.

![Свойства запускаемого при включении проекта в решении](./media/cloud-services-python-ptvs/startup.png)

## <a name="publish-to-azure"></a>Публикация в Azure
Чтобы опубликовать, щелкните правой кнопкой мыши проект облачной службы в решении и выберите пункт **Опубликовать**.

![Вход для публикации в Microsoft Azure](./media/cloud-services-python-ptvs/publish-sign-in.png)

Следуйте указаниям мастера. При необходимости включите удаленный рабочий стол. Его также удобно использовать, когда нужно выполнить отладку.

После завершения настройки параметров щелкните **Опубликовать**.

В окне вывода можно наблюдать выполнение некоторых операций, а затем появится окно журнала действий Microsoft Azure.

![Окно журнала действий Microsoft Azure](./media/cloud-services-python-ptvs/publish-activity-log.png)

Для развертывания потребуется несколько минут, после чего веб-роли и рабочие роли будут запущены на платформе Azure.

### <a name="investigate-logs"></a>Изучение журналов
После того как виртуальная машина в облачной службе запустится и установит Python, можно просмотреть журналы, чтобы найти сообщения о сбое. Эти журналы расположены в папке **C:\Resources\Directory\\{role}\LogFiles**. В файле **PrepPython.err.txt** содержится по крайней мере одна ошибка, которая возникает при попытке скрипта обнаружить, установлен ли язык Python. В файле **PipInstaller.err.txt** может быть сообщение о том, что существующая версия PIP устарела.

## <a name="next-steps"></a>Дополнительная информация
С дополнительной информацией о работе с веб-ролями и рабочими ролями в средствах Python для Visual Studio можно ознакомиться в документации PTVS:

* [Azure Cloud Service Projects for Python][Cloud Service Projects] (Проекты для облачной службы Azure для Python)

Дополнительные сведения об использовании служб Azure из веб-ролей или рабочих ролей, например об использовании хранилища Azure или служебной шины Azure, см. в следующих статьях:

* [Использование хранилища больших двоичных объектов Azure из Python][Blob Service]
* [Использование табличного хранилища из Python][Table Service]
* [Использование хранилища очередей из Python][Queue Service]
* [Как использовать очереди служебной шины][Service Bus Queues]
* [Как использовать разделы и подписки служебной шины][Service Bus Topics]

<!--Link references-->

[Информация об облачных службах]: cloud-services-choose-me.md
[execution model-web sites]: ../app-service/app-service-web-overview.md
[execution model-vms]:../virtual-machines/windows/overview.md
[execution model-cloud services]: cloud-services-choose-me.md
[Python Developer Center]: /develop/python/

[Blob Service]:../storage/blobs/storage-python-how-to-use-blob-storage.md
[Queue Service]: ../storage/queues/storage-python-how-to-use-queue-storage.md
[Table Service]:../cosmos-db/table-storage-how-to-use-python.md
[Service Bus Queues]: ../service-bus-messaging/service-bus-python-how-to-use-queues.md
[Service Bus Topics]: ../service-bus-messaging/service-bus-python-how-to-use-topics-subscriptions.md


<!--External Link references-->

[Python Tools for Visual Studio]: http://aka.ms/ptvs
[Python Tools for Visual Studio Documentation]: http://aka.ms/ptvsdocs
[Cloud Service Projects]: http://go.microsoft.com/fwlink/?LinkId=624028
[Azure SDK Tools for VS 2013]: http://go.microsoft.com/fwlink/?LinkId=746482
[Azure SDK Tools for VS 2015]: http://go.microsoft.com/fwlink/?LinkId=746481
[Azure SDK Tools for VS 2017]: http://go.microsoft.com/fwlink/?LinkId=746483
[Python 2.7 32-bit]: https://www.python.org/downloads/
[Python 3.5 32-bit]: https://www.python.org/downloads/
