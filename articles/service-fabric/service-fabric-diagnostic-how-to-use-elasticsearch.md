---
title: "Использование Elasticsearch в качестве хранилища данных трассировки приложения Service Fabric | Документация Майкрософт"
description: "В этой статье описывается, как приложения Service Fabric используют ElasticSearch и Kibana для хранения, индексирования и поиска данных с помощью трассировки (журналов)."
services: service-fabric
documentationcenter: .net
author: karolz-ms
manager: adegeo
editor: 
ms.assetid: e59b0c39-e468-4d9e-b453-d5f2a8ad20d8
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/07/2017
ms.author: karolz@microsoft.com
redirect_url: /azure/service-fabric/service-fabric-diagnostic-collect-logs-without-an-agent
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 86c1b4dd0cc8833404bd7b66804fa67bbf0d119a
ms.lasthandoff: 12/08/2016


---
# <a name="use-elasticsearch-as-a-service-fabric-application-trace-store"></a>Использование ElasticSearch в качестве хранилища данных трассировки приложения Service Fabric
## <a name="introduction"></a>Введение
В этой статье описывается, как приложения [Azure Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric/) используют **ElasticSearch** и **Kibana** для хранения данных трассировки приложений, индексирования и поиска. [ElasticSearch](https://www.elastic.co/guide/index.html) — это распределенная и масштабируемая система с открытым кодом, предназначенная для поиска и анализа данных в режиме реального времени. Она хорошо подходит для описанной здесь задачи. Ее можно установить на виртуальные машины Windows или Linux, запущенные в Microsoft Azure. ElasticSearch может эффективно обрабатывать *структурированные* трассировки, созданные с помощью таких технологий, как **трассировка событий Windows (ETW)**.

Трассировка событий Windows используется средой выполнения Service Fabric в качестве источника диагностических сведений (трассировки). Этот способ сбора диагностических сведений также рекомендуется и для приложений Service Fabric. Точно такой же механизм позволяет коррелировать трассировки в среде выполнения и в приложении, а также облегчает устранение неполадок. Шаблоны проектов Service Fabric в Visual Studio включают в себя API ведения журнала (в зависимости от класса **EventSource** .NET), который выдает трассировки ETW по умолчанию. Общие сведения о трассировке приложений Service Fabric с помощью ETW см. в разделе [Мониторинг и диагностика состояния служб в локальной среде разработки](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).

Чтобы данные трассировки отображались в ElasticSearch, их необходимо собирать на узлах кластера Service Fabric в реальном времени (во время работы приложения) и затем отправлять в конечную точку ElasticSearch. Есть два основных способа записи данных трассировки.

* **Внутрипроцессная запись данных трассировки.**  
   Приложение или, точнее, процесс службы, отвечает за отправку диагностических данных в хранилище данных трассировки (ElasticSearch).
* **Внепроцессная запись данных трассировки.**  
   Отдельный агент записывает данные трассировки из процессов службы и отправляет их в хранилище данных трассировки.

Ниже мы опишем способ настройки ElasticSearch в Azure, обсудим преимущества и недостатки записи данных двумя указанными выше способами, а также объясним, как настроить службу Service Fabric для отправки данных в ElasticSearch.

## <a name="set-up-elasticsearch-on-azure"></a>Настройка ElasticSearch в Azure
Самый простой способ настройки службы ElasticSearch в Azure — с помощью [**шаблонов Azure Resource Manager**](../azure-resource-manager/resource-group-overview.md). Полнофункциональный [шаблон быстрого запуска диспетчера ресурсов Azure для ElasticSearch](https://github.com/Azure/azure-quickstart-templates/tree/master/elasticsearch) можно получить из репозитория шаблонов быстрого запуска Azure. В этом шаблоне используются отдельные учетные записи хранения для единиц масштабирования (групп узлов). Он также может подготовить отдельные узлы клиента и сервера с различными конфигурациями и различным количеством подключенных дисков данных.

Здесь мы используем другой шаблон — **ES-MultiNode** из [репозитория инструментов диагностики Azure](https://github.com/Azure/azure-diagnostics-tools). Этот шаблон проще в использовании. Он создает кластер ElasticSearch, защищенный обычной проверкой подлинности HTTP. Прежде чем продолжить, скачайте репозиторий с сайта GitHub на свой компьютер (с помощью клонирования репозитория или скачав ZIP-файл). Шаблон ES-MultiNode находится в одноименной папке.

### <a name="prepare-a-machine-to-run-elasticsearch-installation-scripts"></a>Подготовка компьютера к выполнению скриптов установки ElasticSearch
Самый простой способ использования шаблона ES-MultiNode — с помощью готового скрипта Azure PowerShell с именем `CreateElasticSearchCluster`. Чтобы использовать этот скрипт, нужно установить модули PowerShell и средство **openssl**. Это средство необходимо для создания SSH-ключа, который может использоваться для удаленного администрирования кластера ElasticSearch.

`CreateElasticSearchCluster` предназначен для удобства использования шаблона ES-MultiNode на компьютере с Windows. Шаблон можно использовать на компьютерах под управлением других ОС (не Windows), но в этой статье подобные случаи не рассматриваются.

1. Установите [**модули Azure PowerShell**](http://aka.ms/webpi-azps) (если они еще не установлены). При появлении запроса щелкните **Запуск**, а затем — **Установить**. Требуется Azure PowerShell 1.3 или более поздней версии.
2. Инструмент **openssl** входит в дистрибутив [**Git для Windows**](http://www.git-scm.com/downloads). Установите [Git для Windows](http://www.git-scm.com/downloads) , если вы этого еще не сделали. (Параметры установки по умолчанию изменять не нужно.)
3. Далее предполагается, что компонент Git установлен, но не включен в системный путь. Откройте окно Microsoft Azure PowerShell и выполните следующие команды:
   
    ```powershell
    $ENV:PATH += ";<Git installation folder>\usr\bin"
    $ENV:OPENSSL_CONF = "<Git installation folder>\usr\ssl\openssl.cnf"
    ```
   
    Замените `<Git installation folder>` расположением Git на компьютере (по умолчанию это **C:\Program Files\Git**). Обратите внимание на точку с запятой в начале первого пути.
4. Обязательно войдите в Azure (с помощью командлета [`Add-AzureRmAccount`](https://msdn.microsoft.com/library/mt619267.aspx) ) и выберите подписку, которую необходимо использовать для создания кластера Elastic Search. Проверить, правильно ли выбрана подписка, можно с помощью командлетов `Get-AzureRmContext` и `Get-AzureRmSubscription`.
5. Измените текущий каталог на папку ES-MultiNode (если это еще не сделано).

### <a name="run-the-createelasticsearchcluster-script"></a>Запуск скрипта CreateElasticSearchCluster
Прежде чем выполнять скрипт, откройте файл `azuredeploy-parameters.json` и проверьте значения параметров скрипта или введите их. Необходимо указать следующие параметры:

| Имя параметра | Описание |
| --- | --- |
| dnsNameForLoadBalancerIP |Это имя будет использоваться для создания общедоступного DNS-имени кластера Elastic Search (посредством добавления домена региона Azure к указанному имени). Например, если этот параметр имеет значение myBigCluster и выбран регион Azure "Запад США", то DNS-имя кластера будет выглядеть так: myBigCluster.westus.cloudapp.azure.com. <br /><br /> Это имя также будет использоваться в качестве корневого для имен многих артефактов, связанных с кластером Elastic Search, например имен узлов данных. |
| adminUsername |Имя учетной записи администратора для управления кластером Elastic Search (соответствующие ключи SSH будут сформированы автоматически). |
| dataNodeCount |Количество узлов в кластере Elastic Search. Текущая версия сценария не различает узлы данных и запросов. Все узлы будут выполнять обе роли. Значение по умолчанию — 3. |
| dataDiskSize |Размер дисков данных (в ГБ), выделяемый для каждого узла данных. Каждый узел получит по 4 диска данных, предназначенных исключительно для службы Elastic Search. |
| region |Имя региона Azure, в котором будет размещаться кластер Elastic Search. |
| esUserName |Имя пользователя, для которого будет настроен доступ к кластеру ES (с помощью обычной проверки подлинности HTTP). Пароль не хранится в файле параметров и указывается при вызове скрипта `CreateElasticSearchCluster` . |
| vmSizeDataNodes |Размер виртуальной машины Azure для узлов кластера Elastic Search. Значение по умолчанию — Standard_D2. |

Теперь все готово к запуску скрипта. Введите следующую команду:

```powershell
CreateElasticSearchCluster -ResourceGroupName <es-group-name> -Region <azure-region> -EsPassword <es-password>
```

где: 

| Имя параметра скрипта | Описание |
| --- | --- |
| `<es-group-name>` |Имя группы ресурсов Azure, в которую будут входить все ресурсы кластера Elastic Search. |
| `<azure-region>` |Имя региона Azure, в котором будет размещаться кластер Elastic Search. |
| `<es-password>` |Пароль для пользователя Elastic Search. |

> [!NOTE]
> Если при запуске командлета Test-AzureResourceGroup отображается исключение NullReferenceException, значит, вы не выполнили вход в Azure (`Add-AzureRmAccount`).
> 
> 

Если возникла ошибка выполнения скрипта и вы определили, что она вызвана неправильным значением параметра шаблона, исправьте файл параметров и снова запустите скрипт с другим именем группы ресурсов. Также можно повторно использовать то же имя группы ресурсов и очистить старое с помощью скрипта. Для этого следует добавить параметр `-RemoveExistingResourceGroup` к вызову скрипта.

### <a name="result-of-running-the-createelasticsearchcluster-script"></a>Результат выполнения скрипта CreateElasticSearchCluster
После выполнения скрипта `CreateElasticSearchCluster` будут созданы следующие основные артефакты. В данном примере предположим, что для параметра `dnsNameForLoadBalancerIP` указано значение myBigCluster, а кластер создан в регионе "Западная часть США".

| Артефакт | Имя, расположение и примечания |
| --- | --- |
| Ключ SSH для удаленного администрирования |Файл myBigCluster.key (в каталоге, из которого запущен скрипт CreateElasticSearchCluster). <br /><br />Этот ключ может использоваться для подключения к узлу администрирования и (через узел администрирования) к узлам данных в кластере. |
| Узел администрирования |myBigCluster-admin.westus.cloudapp.azure.com <br /><br />Выделенная виртуальная машина для удаленного администрирования кластера ElasticSearch. Только для нее разрешены внешние подключения по протоколу SSH. Эта виртуальная машина работает в той же виртуальной сети, что и остальные узлы кластера ElasticSearch, но не запускает какие-либо службы ElasticSearch. |
| Узлы данных |myBigCluster1– myBigCluster*N* <br /><br />Узлы данных, на которых работают службы ElasticSearch и Kibana. К каждому узлу можно подключиться по протоколу SSH, но только через узел администрирования. |
| Кластер Elasticsearch |http://myBigCluster.westus.cloudapp.azure.com/es/ <br /><br />Основная конечная точка кластера ElasticSearch (обратите внимание на суффикс /es). Кластер защищен с помощью базовой проверки подлинности HTTP (учетные данные указаны в параметрах esUserName/esPassword шаблона ES-MultiNode). Кроме того, в кластере установлен подключаемый модуль head (http://myBigCluster.westus.cloudapp.azure.com/es/_plugin/head) для выполнения базовых задач администрирования кластера. |
| Служба Kibana |http://myBigCluster.westus.cloudapp.azure.com <br /><br /> Служба Kibana настраивается для отображения данных из созданного кластера ElasticSearch. Она защищена теми же учетными данными проверки подлинности, что и сам кластер. |

## <a name="in-process-versus-out-of-process-trace-capturing"></a>Внутрипроцессная и внепроцессная запись данных трассировки
Во введении мы упомянули два основных способа сбора диагностических данных: внутрипроцессный и внепроцессный. Каждый имеет свои преимущества и недостатки.

**Внутрипроцессная запись данных трассировки** имеет следующие преимущества.

1. *Простая настройка и развертывание.*
   
   * Конфигурация сбора диагностических данных является лишь частью конфигурации приложения. Ее легко сохранять "синхронизированной" с остальной частью приложения.
   * Настройка каждого приложения или службы также является несложной задачей.
   * Внепроцессная запись данных трассировки обычно требует отдельного развертывания и настройки агента диагностики. Как правило, это прибавляет работы администратору и может являться источником ошибок. Технология определенного агента часто позволяет использовать только один экземпляр агента на каждую виртуальную машину (узел). Это означает, что конфигурация для коллекции конфигурации диагностики является общей для всех приложений и служб, работающих на этом узле.
2. *Гибкость*
   
   * Приложение может отправлять данные куда угодно при наличии клиентской библиотеки, поддерживающей систему хранения целевых данных. При необходимости можно добавлять новые приемники.
   * Можно реализовать сложные правила сбора, фильтрации и статистической обработки данных.
   * Внепроцессная запись данных трассировки часто ограничивается приемниками данных, которые поддерживает агент. Некоторые агенты являются расширяемыми.
3. *Доступ к внутренним данным приложения и контексту*
   
   * В подсистему диагностики, которая работает внутри процесса приложения или службы, можно легко добавить трассировку с помощью контекстной информации.
   * В случае внепроцессной записи данные должны отправляться агенту с помощью механизма межпроцессного взаимодействия, например трассировки событий Windows. Однако при таком механизме могут возникнуть дополнительные ограничения.

**Внепроцессная запись данных трассировки** имеет следующие преимущества.

1. *Возможность отслеживания приложения и сбора аварийных дампов*
   
   * Внутрипроцессная запись данных трассировки может быть неудачной, если приложение не удается запустить или оно прекращает работу из-за сбоя. Независимый агент имеет гораздо больше шансов собрать важные сведения для устранения неполадок.<br /><br />
2. *Зрелость, надежность и высокая производительность*
   
   * Агент, разработанный поставщиком платформы (например, агент системы диагностики Microsoft Azure), прошел тщательное тестирование и испытан в рабочих условиях.
   * Будьте осторожны, выбирая внутрипроцессную запись данных трассировки. Следите за тем, чтобы отправка диагностических данных из процесса приложения не повлияла на его основные задачи и не привела к проблемам со своевременным выполнением задач или производительностью. Независимый агент менее подвержен подобным проблемам и специально разработан для минимального влияния на работу системы.

Преимущества обоих подходов можно объединить. Это действительно может оказаться наилучшим решением для многих приложений.

Здесь мы используем **библиотеку Microsoft.Diagnostic.Listeners** и внутрипроцессную запись данных трассировки для отправки данных из приложения Service Fabric в кластер ElasticSearch.

## <a name="use-the-listeners-library-to-send-diagnostic-data-to-elasticsearch"></a>Использование библиотеки прослушивателей для передачи диагностических данных в ElasticSearch
Библиотека Microsoft.Diagnostic.Listeners входит в пример приложения Service Fabric с именем PartyCluster. Чтобы ее использовать, сделайте следующее:

1. Скачайте [пример PartyCluster](https://github.com/Azure-Samples/service-fabric-dotnet-management-party-cluster) с сайта GitHub.
2. Скопируйте проекты Microsoft.Diagnostics.Listeners и Microsoft.Diagnostics.Listeners.Fabric (папки полностью) из каталога примера приложения Party Cluster в папку решения приложения, которое должно отправлять данные в ElasticSearch.
3. Откройте это решение, щелкните правой кнопкой мыши узел решения в обозревателе решений и выберите **Добавить существующий проект**. Добавьте в решение проект Microsoft.Diagnostics.Listeners. Повторите те же действия с проектом Microsoft.Diagnostics.Listeners.Fabric.
4. Добавьте ссылку на проект из ваших проектов служб в два добавленных проекта. (Каждая служба, которая будет отправлять данные в ElasticSearch, должна ссылаться на Microsoft.Diagnostics.EventListeners и Microsoft.Diagnostics.EventListeners.Fabric.)
   
    ![Проект ссылается на библиотеки Microsoft.Diagnostics.EventListeners и Microsoft.Diagnostics.EventListeners.Fabric][1]

### <a name="service-fabric-general-availability-release-and-microsoftdiagnosticstracing-nuget-package"></a>Общедоступная версия Service Fabric и пакет Microsoft.Diagnostics.Tracing NuGet
Приложения, основанные на общедоступной версии Service Fabric (версия 2.0.135 от 31 марта 2016 года), нацелены на платформу **.NET Framework 4.5.2**. Это самая новая версия .NET Framework, поддерживаемая Azure на этапе общедоступной версии. К сожалению, в этой версии платформы отсутствуют некоторые API-интерфейсы EventListener, необходимые для библиотеки Microsoft.Diagnostics.Listeners. EventSource (компонент, на котором строятся API-интерфейсы ведения журналов в приложениях Fabric) и EventListener тесно связаны. Поэтому все проекты, использующие библиотеку Microsoft.Diagnostics.Listeners, должны использовать альтернативную реализацию EventSource. Эта реализация доступна в **пакете NuGet Microsoft.Diagnostics.Tracing**, разработанном корпорацией Майкрософт. Этот пакет обратно совместим с компонентом EventSource, который входит в платформу. Следовательно, никакие правки в код вносить не нужно, разве что могут потребоваться изменения указанного пространства имен.

Чтобы начать работу с Microsoft.Diagnostics.Tracing, реализацией класса EventSource, выполните следующие действия для каждого проекта службы, который должен отправлять данные в ElasticSearch.

1. Щелкните проект службы правой кнопкой мыши и выберите **Управление пакетами NuGet**.
2. Перейдите к источнику пакета nuget.org (если он еще не выбран) и введите**Microsoft.Diagnostics.Tracing**в строке поиска.
3. Установите пакет `Microsoft.Diagnostics.Tracing.EventSource` (и его зависимости).
4. Откройте файл **ServiceEventSource.cs** или **ActorEventSource.cs** в проекте службы и замените директиву `using System.Diagnostics.Tracing` в начале файла директивой `using Microsoft.Diagnostics.Tracing`.

Эти действия будут не нужны, когда в Microsoft Azure появится поддержка **.NET Framework 4.6** .

### <a name="elasticsearch-listener-instantiation-and-configuration"></a>Создание и настройка экземпляра прослушивателя ElasticSearch
Последним шагом, необходимым для отправки диагностических данных в ElasticSearch, является создание экземпляра `ElasticSearchListener` и его настройка с помощью данных подключения ElasticSearch. Прослушиватель будет автоматически записывать все события, вызванные через классы EventSource, заданные в проекте службы. Он должен быть активным в течение всего времени существования службы, поэтому лучше всего создать его в коде инициализации службы. Вот как может выглядеть код инициализации службы без отслеживания состояния после внесения необходимых изменений (добавлений, указанных в комментариях, начиная с `****`):

```csharp
using System;
using System.Diagnostics;
using System.Fabric;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceFabric.Services.Runtime;

// **** Add the following directives
using Microsoft.Diagnostics.EventListeners;
using Microsoft.Diagnostics.EventListeners.Fabric;

namespace Stateless1
{
    internal static class Program
    {
        /// <summary>
        /// This is the entry point of the service host process.
        /// </summary>        
        private static void Main()
        {
            try
            {
                // **** Instantiate ElasticSearchListener
                var configProvider = new FabricConfigurationProvider("ElasticSearchEventListener");
                ElasticSearchListener esListener = null;
                if (configProvider.HasConfiguration)
                {
                    esListener = new ElasticSearchListener(configProvider, new FabricHealthReporter("ElasticSearchEventListener"));
                }

                // The ServiceManifest.XML file defines one or more service type names.
                // Registering a service maps a service type name to a .NET type.
                // When Service Fabric creates an instance of this service type,
                // an instance of the class is created in this host process.

                ServiceRuntime.RegisterServiceAsync("Stateless1Type", 
                    context => new Stateless1(context)).GetAwaiter().GetResult();

                ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(Stateless1).Name);

                // Prevents this host process from terminating so services keep running.
                Thread.Sleep(Timeout.Infinite);

                // **** Ensure that the ElasticSearchListner instance is not garbage-collected prematurely
                GC.KeepAlive(esListener);
            }
            catch (Exception e)
            {
                ServiceEventSource.Current.ServiceHostInitializationFailed(e);
                throw;
            }
        }
    }
}
```

Данные подключения ElasticSearch необходимо поместить в отдельный раздел файла конфигурации службы (**PackageRoot\Config\Settings.xml**). Имя раздела должно соответствовать значению, которое передается конструктору `FabricConfigurationProvider` , например:

```xml
<Section Name="ElasticSearchEventListener">
  <Parameter Name="serviceUri" Value="http://myBigCluster.westus.cloudapp.azure.com/es/" />
  <Parameter Name="userName" Value="(ES user name)" />
  <Parameter Name="password" Value="(ES user password)" />
  <Parameter Name="indexNamePrefix" Value="myapp" />
</Section>
```
Значения параметров `serviceUri`, `userName` и `password` — это адрес конечной точки кластера ElasticSearch, имя пользователя ElasticSearch и его пароль, соответственно. `indexNamePrefix` — префикс для индексов ElasticSearch. Библиотека Microsoft.Diagnostics.Listeners ежедневно создает новый индекс для своих данных.

### <a name="verification"></a>Проверка
Вот и все! Теперь при запуске служба будет отправлять данные трассировки в службу ElasticSearch, указанную в конфигурации. Это можно проверить, открыв пользовательский интерфейс Kibana, связанный с целевым экземпляром ElasticSearch. В нашем примере адрес страницы — http://myBigCluster.westus.cloudapp.azure.com/. Убедитесь, что индексы с префиксом имени, выбранным для экземпляра `ElasticSearchListener` , действительно были созданы и заполнены данными.

![Отображение событий приложения PartyCluster в службе Kibana][2]

## <a name="next-steps"></a>Дальнейшие действия
* [Дополнительные сведения о диагностике и мониторинге службы Service Fabric](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

<!--Image references-->
[1]: ./media/service-fabric-diagnostics-how-to-use-elasticsearch/listener-lib-references.png
[2]: ./media/service-fabric-diagnostics-how-to-use-elasticsearch/kibana.png

