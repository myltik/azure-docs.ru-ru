<properties
   pageTitle="Руководство по преобразованию рабочих ролей и веб-ролей в службы без отслеживания состояния Service Fabric | Microsoft Azure"
   description="В этом руководстве веб-роли и рабочие роли облачных служб сравниваются со службами без отслеживания состояния Service Fabric в плане переноса из облачных служб на платформу Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/29/2016"
   ms.author="vturecek"/>
 
# Руководство по преобразованию рабочих ролей и веб-ролей в службы без отслеживания состояния Service Fabric

В этой статье описано, как переносить рабочие роли и веб-роли облачных служб в службы без отслеживания состояния Service Fabric. Это самый простой способ переноса из облачных служб в службы Service Fabric при работе с приложениями, общая архитектура которых почти не изменяется.

## Перенос из проекта облачной службы в проект приложения Service Fabric
 
 Проект облачной службы и проект приложения Service Fabric имеют сходную структуру и оба являются единицами развертывания приложения. Иными словами, каждый из этих проектов задает полноценный пакет компонентов, которые развертываются для запуска приложения. Проект облачной службы содержит веб-роли или рабочие роли (одну или несколько). Подобным образом содержит несколько служб и проект приложения Service Fabric.

Различие между ними в том, что проект облачной службы совмещает развертывание приложения с развертыванием виртуальных машин, тогда как проект приложения Service Fabric только задает приложение, которое предстоит развернуть на нескольких существующих виртуальных машинах в кластере Service Fabric. Сам же кластер Service Fabric развертывается только один раз — на основании шаблона ARM или с помощью портала Azure, — и в нем можно развернуть несколько приложений Service Fabric.

![Сравнение проекта облачных служб и проекта Service Fabric][3]
 
## Преобразование рабочей роли в службу без отслеживания состояния 

По существу рабочая роль представляет собой рабочую нагрузку без отслеживания состояния (все экземпляры такой рабочей нагрузки идентичны, а запросы могут передаваться на любой экземпляр в любое время). Ни один из экземпляров обычно не помнит предыдущие запросы. Состоянием, в котором функционирует рабочая нагрузка, управляет внешнее хранилище состояний, например хранилище таблиц Azure или Azure Document DB. В платформе Service Fabric рабочей нагрузке этого типа соответствует служба без отслеживания состояния. Перенести рабочую роль на платформу Service Fabric проще всего так: следует преобразовать код рабочей роли в код службы без отслеживания состояния.

![Преобразование рабочей роли в службу без отслеживания состояния][4]

## Преобразование веб-роли в службу без отслеживания состояния

Как и рабочая роль, веб-роль тоже является рабочей нагрузкой без отслеживания состояния. Поэтому и ее можно сопоставить со службой без отслеживания состояния Service Fabric. В отличие от веб-ролей, платформа Service Fabric не поддерживает IIS. Чтобы перенести веб-приложение из веб-роли в службу без отслеживания состояния, сначала следует переместить автономно размещаемую веб-платформу, которая не зависит от IIS или System.Web (например, ASP.NET Core 1).

**** Приложение ** | **Поддерживается** | **Путь переноса**
--- | --- | ---
Веб-формы ASP.NET | Нет | Преобразование в ASP.NET Core 1 MVC
ASP.NET MVC 3 | С переносом | Обновление до ASP.NET Core 1
Веб-API ASP.NET | С переносом | Использование автономно размещаемого сервера или компонента ASP.NET Core 1
ASP.NET Core 1 | Да | Недоступно

## Жизненный цикл и интерфейс API точки входа

Рабочая роль и интерфейсы API службы Service Fabric дают возможность использовать похожие точки входа.

**Точка входа** | **Рабочая роль** | **Служба Service Fabric**
--- | --- | ---
Обработка | `Run()` | `RunAsync()`
Запуск виртуальной машины | `OnStart()` | Недоступно
Остановка виртуальной машины | `OnStop()` | Недоступно
Открытие прослушивателя для запросов клиентов | Недоступно | <ul><li> `CreateServiceInstanceListener()` для служб без отслеживания состояния</li><li>`CreateServiceReplicaListener()` для служб с отслеживанием состояния</li></ul>

### Рабочая роль

```C#

using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRole1
{
    public class WorkerRole : RoleEntryPoint
    {
        public override void Run()
        {
        }

        public override bool OnStart()
        {
        }

        public override void OnStop()
        {
        }
    }
}

```

### Служба без отслеживания состояния Service Fabric

```C#

using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

namespace Stateless1
{
    public class Stateless1 : StatelessService
    {
        protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
        {
        }

        protected override Task RunAsync(CancellationToken cancelServiceInstance)
        {
        }
    }
}

```

И роль, и служба имеют основной компонент переопределения Run, с которого и начинается обработка. Службы Service Fabric объединяют `Run`, `Start` и `Stop` в одну точку входа, `RunAsync`. Ваша служба должна начать работу после начала работы `RunAsync`. Она должна завершить работу, когда маркер CancellationToken метода `RunAsync` получает оповещение.

Существует несколько ключевых различий между жизненным циклом и временем существования рабочих ролей и служб Service Fabric.

 - **Жизненный цикл:** главное отличие заключается в том, что рабочая роль — это виртуальная машина, поэтому ее жизненный цикл привязан к виртуальной машине (например, в нем учитываются события запуска и остановки виртуальной машины). Жизненный цикл службы Service Fabric отделен от жизненного цикла виртуальной машины и поэтому не включает в себя события запуска и остановки (основной) виртуальной машины.

 - **Время существования**: если метод `Run` завершает работу, выполняется повторный запуск экземпляра рабочей роли. Тем не менее метод `RunAsync` в службе Service Fabric может продолжать работать столько, сколько нужно, и экземпляр службы выключаться не будет.

Службам, которые прослушивают запросы клиента, платформа Service Fabric дает возможность использовать пользовательскую точку входа настройки связи. И RunAsync, и точка входа связи являются пользовательскими компонентами переопределения в службах Service Fabric. Ваша служба может, например, только прослушивать запросы клиента, или только выполнять цикл обработки, или делать и то и другое вместе. Именно поэтому при завершении работы метода RunAsync не требуется перезапуск службы, потому что он может продолжить прослушивать запросы клиента.

## Интерфейс API и среда приложения

Интерфейс API среды облачных служб предоставляет сведения и функции для текущего экземпляра виртуальной машины, а также сведения о других экземплярах роли виртуальной машины. Платформа Service Fabric предоставляет информацию о своей среде выполнения, а также информацию об узле, на котором работает служба.

**Задача среды** | **Облачные службы** | **Service Fabric**
--- | --- | ---
Параметры конфигурации и изменение уведомления | `RoleEnvironment` | `CodePackageActivationContext`
Локальное хранилище | `RoleEnvironment` | `CodePackageActivationContext`
Сведения о конечной точке | `RoleInstance` <ul><li>Текущий экземпляр: `RoleEnvironment.CurrentRoleInstance`</li><li>Другие роли и экземпляр: `RoleEnvironment.Roles`</li> | <ul><li>`NodeContext` для текущего адреса узла</li><li>`FabricClient` и `ServicePartitionResolver` для обнаружения конечной точки службы</li> 
Эмуляция среды | `RoleEnvironment.IsEmulated` | Недоступно
Событие одновременного изменения | `RoleEnvironment` | Недоступно

## Параметры конфигурации

Параметры конфигурации в облачных службах задаются для роли виртуальной машины и применяются ко всем экземплярам этой роли. Эти параметры представляют собой пары "ключ — значение", заданные в файлах ServiceConfiguration.*.cscfg. Получить к ним прямой доступ можно через RoleEnvironment. На платформе Service Fabric параметры применяются отдельно к каждой службе и каждому приложению, а не к каждой виртуальной машине. Это обусловлено тем, что на виртуальной машине может быть размещено несколько служб и приложений. Служба состоит из трех пакетов:

 - **Код:** содержит исполняемые файлы службы, двоичные файлы, библиотеки DLL и другие файлы, необходимые для запуска службы.
 - **Конфигурация:** все файлы конфигурации и параметры для службы.
 - **Данные:** файлы статических данных, связанные со службой.

Каждый из этих пакетов можно отдельно обновить, в том числе до определенной версии. Как и к облачным службам, к пакету конфигурации можно получить доступ программным путем через интерфейс API. Как и при работе с облачными службами, для уведомления службы об изменении пакета конфигурации используются события. С помощью файла Settings.xml можно настроить пару "ключ-значение" и программный доступ так же, как в разделе параметров файла App.config. Однако, в отличие от облачных служб, пакет конфигурации Service Fabric может содержать любые файлы конфигурации в любом формате, будь то XML, JSON, YAML или пользовательский двоичный формат.


### Получение доступа к конфигурации
#### Облачные службы

Доступ к параметрам конфигурации из файла ServiceConfiguration.*.cscfg можно получить с помощью атрибута `RoleEnvironment`. Эти параметры доступны глобально всем экземплярам ролей в одном развертывании облачной службы.

```C#

string value = RoleEnvironment.GetConfigurationSettingValue("Key");

```

#### ServiceFabic

Каждая служба имеет отдельный пакет конфигурации. Не существует встроенного механизма, который предназначен для глобальных параметров конфигурации, доступных всем приложениям кластера. Если в пакете конфигурации используется специальный файл конфигурации Service Fabric, который называется Settings.xml, значения в этом файле могут перезаписываться на уровне приложения. Это позволяет настраивать параметры конфигурации на этом уровне.

Доступ к параметрам конфигурации можно получить в пределах каждого экземпляра службы через атрибут `CodePackageActivationContext` службы.

```C#

ConfigurationPackage configPackage = this.ServiceInitializationParameters.CodePackageActivationContext.GetConfigurationPackageObject("Config");

// Access Settings.xml
KeyedCollection<string, ConfigurationProperty> parameters = configPackage.Settings.Sections["MyConfigSection"].Parameters;

string value = parameters["Key"]?.Value;

// Access custom configuration file:
using (StreamReader reader = new StreamReader(Path.Combine(configPackage.Path, "CustomConfig.json")))
{
    MySettings settings = JsonConvert.DeserializeObject<MySettings>(reader.ReadToEnd());
}

```

### События обновления конфигурации
#### Облачные службы

Событие `RoleEnvironment.Changed` оповещает все экземпляры ролей, когда в среде происходит изменение, например изменение конфигурации. Это позволяет интегрировать обновления конфигурации без перезапуска экземпляров ролей и рабочих процессов.

```C#

RoleEnvironment.Changed += RoleEnvironmentChanged;

private void RoleEnvironmentChanged(object sender, RoleEnvironmentChangedEventArgs e)
{
   // Get the list of configuration changes
   var settingChanges = e.Changes.OfType<RoleEnvironmentConfigurationSettingChange>();
foreach (var settingChange in settingChanges) 
   {
      Trace.WriteLine("Setting: " + settingChange.ConfigurationSettingName, "Information");
   }
}

```

#### ServiceFabic

В каждом из трех типов пакетов в службе — "код", "конфигурация" и "данные" — есть события, которые уведомляют экземпляр службы, когда пакет обновляется, добавляется или удаляется. Служба может содержать несколько пакетов каждого типа. Например, служба может иметь несколько пакетов конфигурации, для каждого из которых можно задать версию и каждый из которых можно обновлять.

Эти события позволяют интегрировать изменения, которые содержит пакет обновления, без перезапуска экземпляра службы.
 
```C#

this.ServiceInitializationParameters.CodePackageActivationContext.ConfigurationPackageModifiedEvent +=
                    this.CodePackageActivationContext_ConfigurationPackageModifiedEvent;

private void CodePackageActivationContext_ConfigurationPackageModifiedEvent(object sender, PackageModifiedEventArgs<ConfigurationPackage> e)
{
    this.UpdateCustomConfig(e.NewPackage.Path);
    this.UpdateSettings(e.NewPackage.Settings);
}

```

## Задачи при запуске

Задачи при запуске — это действия, выполняемые перед запуском приложения. Обычно такая задача запускает скрипты настройки, используя повышенные права. Эти задачи поддерживаются и облачными службами, и платформой Service Fabric. Основное различие состоит в том, что в облачных службах задача при запуске связана с виртуальной машиной, которая является частью экземпляра роли, тогда как в платформе Service Fabric задача при запуске связана со службой, которая не связана с какой-либо виртуальной машиной.

 | Облачные службы | Service Fabric
--- | --- | ---
Расположение конфигурации | ServiceDefinition.csdef | ServiceManifest.xml
Привилегии | Ограниченные или повышенные | Любая учетная запись пользователя или компьютера
Виртуализация | Простая, фоновая, на переднем плане | Выполнение задачи запуска должно успешно завершиться до начала обновления

### Облачные службы
В облачных службах точка входа для запуска настраивается для каждой роли в файле ServiceDefintion.csdef.

```xml

<ServiceDefinition>
    <Startup>
        <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" >
            <Environment>
                <Variable name="MyVersionNumber" value="1.0.0.0" />
            </Environment>
        </Task>
    </Startup>
    ...
</ServiceDefinition>

```

### Service Fabric

В Service Fabric точка входа для запуска настраивается для каждой роли в файле ServiceManifest.xml.

```xml

<ServiceManifest>
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>Startup.bat</Program>
      </ExeHost>
    </SetupEntryPoint>
    ...
</ServiceManifest>

``` 

## Примечание о среде разработки

Облачные службы и платформа Service Fabric интегрированы с шаблонами проектов в Visual Studio и поддерживают (локально и в Azure) отладку, настройку и развертывание. Кроме того, облачные службы и платформа Service Fabric дают возможность пользоваться локальной средой выполнения разработки. Различие заключается в том, что среда выполнения разработки облачных служб эмулирует среду Azure, в которой она запущена, тогда как платформа Service Fabric использует не эмулятор, а полноценную среду Service Fabric. Среда Service Fabric, в которой вы работаете на компьютере локальной разработки, — это та же среда, которая используется как рабочая.

##Дальнейшие действия

Чтобы узнать, как эффективно пользоваться всеми функциями Service Fabric, ознакомьтесь с дополнительными сведениями о службах Reliable Services платформы Service Fabric и фундаментальных различиях между облачными службами и архитектурой приложения Service Fabric.

 - [Начало работы со службами Reliable Services в Service Fabric](./service-fabric-reliable-services-quick-start.md)

 - [Conceptual guide to the differences between Cloud Services and Service Fabric](./service-fabric-cloud-services-migration-differences.md) (Руководство: концептуальные различия между облачными службами и платформой Service Fabric)
 
<!--Image references-->
[3]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/service-fabric-cloud-service-projects.png
[4]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/worker-role-to-stateless-service.png

<!---HONumber=AcomDC_0406_2016-->