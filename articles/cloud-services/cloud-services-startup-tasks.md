---
title: Выполнение задач запуска в облачных службах Azure | Документация Майкрософт
description: Задачи запуска помогают подготовить среду облачной службы для приложения. Вы узнаете, как работают задачи запуска и как их можно создать.
services: cloud-services
documentationcenter: ''
author: Thraka
manager: timlt
editor: ''
ms.assetid: 886939be-4b5b-49cc-9a6e-2172e3c133e9
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: adegeo
ms.openlocfilehash: 1c1b3aa86dc8211de0c07c9fb68da5685c86f551
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
ms.locfileid: "22999139"
---
# <a name="how-to-configure-and-run-startup-tasks-for-a-cloud-service"></a>Как настроить и выполнить задачи запуска для облачной службы
С помощью задач запуска вы можете выполнять различные операции перед запуском роли. Это может быть установка компонента, регистрация компонентов COM, установка разделов реестра или запуск длительного процесса.

> [!NOTE]
> Задачи запуска неприменимы к виртуальным машинам, они подходят только для веб-ролей и рабочих ролей облачной службы.
> 
> 

## <a name="how-startup-tasks-work"></a>Как работают задачи запуска
Задачи запуска — это действия, выполняемые до запуска роли и определяемые в файле [ServiceDefinition.csdef] с помощью элемента [Task] в элементе [Startup]. Часто задачи запуска — это пакетные файлы, но они также могут быть консольными приложениями или пакетными файлами, запускающими сценарии PowerShell.

Переменные среды передают информацию в задачу запуска, а локальное хранилище можно использовать для передачи информации из задачи запуска. Например, в переменной среды можно указать путь к программе, которую вы хотите установить, и файлы могут быть записаны в локальное хранилище, чтобы позже их могла считать роль.

Задача запуска может записывать информацию и ошибки в журнал в каталоге, заданном переменной среды **TEMP** . Во время выполнения задачи запуска в облаке переменная среды **TEMP** сопоставляется с каталогом *C:\\Resources\\temp\\[guid].[имя_роли]\\RoleTemp*.

Кроме того, задачи запуска могут выполняться несколько раз между перезагрузками. Например, задача запуска будет выполняться при каждом перезапуске роли, что не всегда подразумевает перезагрузку. Задачи запуска следует составлять таким способом, чтобы их можно было без проблем запускать несколько раз.

Задачи запуска должны заканчиваться **errorlevel** (или кодом выхода), равным 0. Тогда процесс запуска будет завершен. Если задача запуска завершается ненулевым **errorlevel**, роль не запускается.

## <a name="role-startup-order"></a>Порядок запуска роли
Ниже приведена процедура запуска роли в Azure.

1. Экземпляр помечается как **Запуск** и не получает трафик.
2. Все задачи запуска выполняются в соответствии с их атрибутом **taskType** .
   
   * **Простые** задачи (simple) выполняются синхронно, по одной.
   * **Фоновые** задачи (background) и задачи **переднего плана** (foreground) запускаются асинхронно, параллельно с задачей запуска.  
     
     > [!WARNING]
     > Службы IIS могут не быть полностью настроены на этапе выполнения задач запуска в процессе запуска, поэтому данные, относящиеся к роли, могут быть недоступны. Для задач запуска, требующих данные, относящиеся к роли, следует использовать [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx).
     > 
     > 
3. Запускается процесс размещения роли и в IIS создается сайт.
4. Вызывается метод [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx) .
5. Экземпляр помечается как **Готов** , и трафик перенаправляется в него.
6. Вызывается метод [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.Run](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) .

## <a name="example-of-a-startup-task"></a>Пример задачи запуска
Задачи запуска определяются в файле [ServiceDefinition.csdef], в элементе **Task**. Атрибут **commandLine** задает имя и параметры пакетного файла запуска или команды консоли. Атрибут **executionContext** задает уровень привилегий задачи запуска, а атрибут **taskType** указывает, каким образом будет выполняться задача.

В этом примере для задачи запуска создается переменная среды **MyVersionNumber**, которой присваивается значение **1.0.0.0**.

**ServiceDefinition.csdef**:

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" >
        <Environment>
            <Variable name="MyVersionNumber" value="1.0.0.0" />
        </Environment>
    </Task>
</Startup>
```

В следующем примере пакетный файл **Startup.cmd** записывает строку «The current version is 1.0.0.0» в файл StartupLog.txt в каталоге, указанном в переменной среды TEMP. Строка `EXIT /B 0` гарантирует, что задача запуска завершается **errorlevel** , равным 0.

```cmd
ECHO The current version is %MyVersionNumber% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT /B 0
```

> [!NOTE]
> В Visual Studio свойству **Копировать в выходной каталог** для пакетного файла запуска должно быть присвоено значение **Всегда копировать**, чтобы пакетный файл запуска был должным образом развернут в проекте Azure (**approot\\bin** для веб-ролей и **approot** для рабочих ролей).
> 
> 

## <a name="description-of-task-attributes"></a>Описание атрибутов задачи
Ниже описаны атрибуты элемента **Task** в файле [ServiceDefinition.csdef] .

**commandLine** — задает командную строку для задачи запуска:

* Команда с необязательными параметрами командной строки, с которой начинается задача запуска.
* Часто это имя пакетного CMD- или BAT-файла.
* Задача задается относительно папки AppRoot\\bin развертывания. Переменные среды не разворачиваются при определении пути и файла задачи. Если требуется расширение среды, можно создать небольшой CMD-файл сценария, который вызывает задачу запуска.
* Может быть консольным приложением или пакетным файлом, который запускает [сценарий PowerShell](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task).

**executionContext** — задает уровень привилегий для задачи запуска. Уровень привилегий может быть ограничен или повышен:

* **limited**  
  Задача запуска выполняется с теми же привилегиями, что и роль. Если атрибут **executionContext** элемента [Runtime] также имеет значение **limited**, то используются привилегии пользователя.
* **elevated**  
  Задача запуска выполняется с привилегиями администратора. Это позволяет задачам запуска устанавливать программы, вносить изменения в конфигурацию IIS, вносить изменения в реестр и выполнять другие административные задачи без увеличения уровня привилегий самой роли.  

> [!NOTE]
> Уровень привилегий задачи запуска не обязательно должен совпадать с уровнем привилегий роли.
> 
> 

**taskType** — указывает способ выполнения задачи запуска.

* **Простые**  
  Задачи выполняются синхронно, поочередно и в порядке, указанном в файле [ServiceDefinition.csdef] . Если одна задача запуска **simple** завершается **errorlevel**, равным нулю, выполняется следующая задача запуска **simple**. Если больше нет задач запуска **simple** для выполнения, запускается сама роль.   
  
  > [!NOTE]
  > Если задача **simple** завершается ненулевым **errorlevel**, экземпляр будет заблокирован. Последующие задачи запуска **simple** не запустятся, как и сама роль.
  > 
  > 
  
    Чтобы убедиться, что пакетный файл завершается нулевым **errorlevel**, выполните команду `EXIT /B 0` в конце обработки пакетного файла.
* **background**  
  Задачи выполняются асинхронно, параллельно с запуском роли.
* **переднего плана**  
  Задачи выполняются асинхронно, параллельно с запуском роли. Основное различие между задачами **foreground** и **background** состоит в том, что задача **foreground** не разрешает перезапуск или завершение работы роли до завершения задачи. Задачи **background** не имеют этого ограничения.

## <a name="environment-variables"></a>Переменные среды
Переменные среды — это способ передачи информации в задачу запуска. Например, можно передать путь к большому двоичному объекту, содержащему программу для установки, или номера портов, которые будет использовать роль, или параметры для управления функциями задачи запуска.

Существует два типа переменных среды для задачи запуска: статические переменные среды и переменные среды на основе членов класса [RoleEnvironment] . Оба они находятся в разделе [Environment] файла [ServiceDefinition.csdef] и используют элемент [Variable] и атрибут **name**.

Статические переменные среды используют атрибут **value** элемента [Variable] . В примере выше создается переменная среды **MyVersionNumber**, которая имеет статическое значение **1.0.0.0**. Другим примером может служить создание переменной среды **StagingOrProduction**, которой можно вручную присвоить значение **staging** или **production**, чтобы выполнять различные действия запуска в зависимости от значения переменной среды **StagingOrProduction**.

Переменные среды на основе членов класса RoleEnvironment не используют атрибут **value** элемента [Variable] . Вместо этого дочерний элемент [RoleInstanceValue] с соответствующим значением атрибута **XPath** используется для создания переменной среды для конкретного члена класса [RoleEnvironment]. Значения атрибута **XPath** для доступа к различным значениям [RoleEnvironment] можно найти [здесь](cloud-services-role-config-xpath.md).

Например, чтобы создать переменную среды, имеющую значение **true** при выполнении в эмуляторе вычислений и **false** — при выполнении в облаке, используйте следующие элементы [Variable] и [RoleInstanceValue]:

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>

            <!-- Create the environment variable that informs the startup task whether it is running
                in the Compute Emulator or in the cloud. "%ComputeEmulatorRunning%"=="true" when
                running in the Compute Emulator, "%ComputeEmulatorRunning%"=="false" when running
                in the cloud. -->

            <Variable name="ComputeEmulatorRunning">
                <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
            </Variable>

        </Environment>
    </Task>
</Startup>
```

## <a name="next-steps"></a>Дополнительная информация
Узнайте, как выполнять некоторые [стандартные задачи запуска](cloud-services-startup-tasks-common.md) с помощью облачной службы.

[Упакуйте](cloud-services-model-and-package.md) облачную службу.  

[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[Task]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[Startup]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Runtime]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[Environment]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[Variable]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx
