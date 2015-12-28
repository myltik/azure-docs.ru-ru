<properties
   pageTitle="Использование ElasticSearch в качестве хранилища данных трассировки приложения Service Fabric | Microsoft Azure"
   description="В этой статье описывается, как приложения Service Fabric используют ElasticSearch и Kibana для хранения, индексирования и поиска данных с помощью трассировки (журналов)."
   services="service-fabric"
   documentationCenter=".net"
   authors="karolz-ms"
   manager="adegeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/02/2015"
   ms.author="karolz@microsoft.com"/>

# Использование ElasticSearch в качестве хранилища данных трассировки приложения Service Fabric
## Введение
В этой статье описывается, как приложения [Service Fabric](http://azure.microsoft.com/documentation/services/service-fabric/) используют **ElasticSearch** и **Kibana** для хранения данных трассировки приложений, индексирования и поиска. [ElasticSearch](https://www.elastic.co/guide/index.html) — это распределенная и масштабируемая система с открытым кодом, предназначенная для поиска и анализа данных в режиме реального времени. Она хорошо подходит для описанной здесь задачи, и ее можно установить на виртуальные машины Windows или Linux, запущенные в Microsoft Azure. ElasticSearch может очень эффективно обрабатывать *структурированные* трассировки, созданные с помощью таких технологий, как **трассировка событий Windows (ETW)**.

Трассировка событий Windows используется средой выполнения Service Fabric для получения диагностических сведений (трассировки). Рекомендуем использовать ее для получения диагностических сведений о приложениях Service Fabric. Это позволяет коррелировать трассировки в среде выполнения и в приложении, а также облегчает устранение неполадок. Шаблоны проектов Service Fabric в Visual Studio включают API-интерфейс ведения журналов (в зависимости от класса **EventSource** .NET), который выдает трассировки ETW по умолчанию. Общие сведения о трассировке приложения Service Fabric с использованием ETW см. в [этой статье](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).

Чтобы данные трассировки отображались в ElasticSearch, их необходимо собирать на узлах кластера Service Fabric в реальном времени (во время работы приложения) и затем отправлять в конечную точку ElasticSearch. Есть два основных способа записи данных трассировки.

+ **Внутрипроцессная запись данных трассировки.** Приложение, или, точнее, процесс службы, отвечает за отправку диагностических данных в хранилище данных трассировки (ElasticSearch).

+ **Внепроцессная запись данных трассировки.** Отдельный агент записывает данные трассировки из процессов службы и отправляет их в хранилище данных трассировки.

Далее в этой статье описывается способ настройки ElasticSearch в Azure, рассматриваются преимущества и недостатки записи данных двумя указанными выше способами, а также объясняется, как настроить службу Fabric для отправки данных в ElasticSearch.


## Настройка ElasticSearch в Azure
Самый простой способ настройки службы ElasticSearch в Azure — с помощью [**шаблонов ARM Azure**](../resource-group-overview.md). Полнофункциональный [шаблон ARM быстрого запуска для ElasticSearch](https://github.com/Azure/azure-quickstart-templates/tree/master/elasticsearch) можно получить из репозитория шаблонов быстрого запуска Azure. Этот шаблон использует отдельные учетные записи хранения для единиц масштабирования (групп узлов) и может подготавливать отдельные узлы клиента и сервера с различными конфигурациями и различным количеством подключенных дисков с данными.

В этой статье мы будем использовать другой шаблон, который называется **ES-MultiNode** от подразделения [Microsoft Patterns & Practices ELK](https://github.com/mspnp/semantic-logging/tree/elk/). Этот шаблон немного проще в использовании. Он создает кластер ElasticSearch, защищенный обычной проверкой подлинности HTTP по умолчанию. Прежде чем продолжать, скачайте [репозиторий «elk» Microsoft P&P](https://github.com/mspnp/semantic-logging/tree/elk/) с сайта GitHub на свой компьютер (путем клонирования репозитория или загрузки ZIP-файла). Шаблон ES-MultiNode находится в одноименной папке.
>[AZURE.NOTE]Сейчас шаблон ES-MultiNode и связанные скрипты поддерживают выпуск ElasticSearch 1.7. Поддержка ElasticSearch 2.0 будет добавлена позже.

### Подготовка компьютера к выполнению скриптов установки ElasticSearch
Самый простой способ использования шаблона ES-MultiNode — с помощью готового скрипта PowerShell с именем `CreateElasticSearchCluster`. Чтобы использовать этот скрипт, нужно установить модули Azure PowerShell и средство openssl. Последний необходим для создания SSH-ключа, который может использоваться для удаленного администрирования кластера ElasticSearch.

Примечание. Скрипт `CreateElasticSearchCluster` предназначен для удобства использования шаблона ES-MultiNode на компьютере Windows. Шаблон можно использовать на компьютерах под управлением других ОС (не Windows), но в этой статье подобные случаи не рассматриваются.

1. Установите [**модули Azure PowerSell**](http://go.microsoft.com/fwlink/p/?linkid=320376) (если они еще не установлены). При появлении запроса щелкните «Запуск», а затем — «Установить».
>[AZURE.NOTE]В выпуске Azure PowerShell 1.0 готовятся значительные изменения. Скрипт CreateElasticSearchCluster работает только с Azure PowerShell 0.9.8 и не поддерживает предварительную версию Azure PowerShell 1.0. Скрипт, совместимый с Azure PowerShell 1.0, выйдет позже.

2. Средство **openssl** входит в дистрибутив [**Git для Windows**](http://www.git-scm.com/downloads). Установите [Git для Windows](http://www.git-scm.com/downloads) (параметры установки по умолчанию изменять не нужно), если вы этого еще не сделали.

3. Предположим, что компонент Git установлен, но не включен в системный путь. Откройте окно Microsoft Azure PowerShell и выполните следующие команды:

    ```powershell
    $ENV:PATH += ";<Git installation folder>\usr\bin"
    $ENV:OPENSSL_CONF = "<Git installation folder>\usr\ssl\openssl.cnf"
    ```

    Замените `<Git installation folder>` расположением Git на компьютере (значение по умолчанию — *C:\\Program Files\\Git*). Обратите внимание на точку с запятой в начале первого пути.

4. Обязательно войдите в Azure (с помощью командлета [*Add-AzureAccount*](https://msdn.microsoft.com/library/azure/dn790372.aspx)) и выберите подписку, которую необходимо использовать для создания кластера ElasticSearch ([*Select-AzureSubscription*](https://msdn.microsoft.com/library/azure/dn790367.aspx)).

5. Измените текущий каталог на папку ES-MultiNode (если это еще не сделано).

### Выполнение скрипта CreateElasticSearchCluster
Прежде чем выполнять скрипт, откройте файл `azuredeploy-parameters.json` и проверьте значения параметров скрипта или введите их. Необходимо указать следующие параметры:

|Имя параметра |Описание|
|-----------------------  |--------------------------|
|dnsNameForLoadBalancerIP |Это имя будет использоваться для создания общедоступного DNS-имени кластера ElasticSearch (посредством добавления домена региона Azure к указанному имени). Например, если этот параметр имеет значение «myBigCluster» и выбран регион Azure «Запад США», DNS-имя кластера будет выглядеть так: myBigCluster.westus.cloudapp.azure.com. <br /><br />Это имя также будет использоваться в качестве корневого для имен многих артефактов, связанных с кластером ElasticSearch, например имен узлов данных.|
|storageAccountPrefix |Префикс для учетных записей хранения, которые будут созданы для кластера ElasticSearch. <br /><br /> В текущей версии шаблона используется одна общая учетная запись хранения, но в будущем это может измениться.|
|adminUsername |Имя учетной записи администратора для управления кластером ElasticSearch (соответствующие ключи SSH будут формироваться автоматически).|
|dataNodeCount |Количество узлов в кластере ElasticSearch. Текущая версия скрипта не различает узлы данных и запросов. Все узлы будут выполнять обе роли.|
|dataDiskSize |Размер дисков данных (в ГБ), выделяемый для каждого узла данных. Каждый узел получит по 4 диска данных, предназначенных исключительно для службы ElasticSearch.|
|region |Имя региона Azure, в котором будет размещаться кластер ElasticSearch.|
|esClusterName |Внутреннее имя кластера ElasticSearch. <br /><br />Это значение по умолчанию не нужно изменять, если вы не планируете запускать несколько кластеров ElasticSearch в одной и той же виртуальной сети. Сейчас шаблон ES-MultiNode не поддерживает такую возможность.|
|esUserName esPassword |Учетные данные пользователя, для которого будет настроен доступ к кластеру ES (с помощью обычной проверки подлинности HTTP).|

Теперь все готово к запуску скрипта. Выполните команду ```powershell
CreateElasticSearchCluster -ResourceGroupName <es-group-name>
```, где `<es-group-name>` — имя группы ресурсов Azure, в которую будут входить все ресурсы кластера.

>[AZURE.NOTE]Если отображается исключение NullReferenceException от командлета Test-AzureResourceGroup, вы не выполнили вход в Azure (`Add-AzureAccount`).

Если возникла ошибка выполнения скрипта и вы определили, что она вызвана неправильным значением параметра шаблона, исправьте файл параметров и снова запустите скрипт с другим именем группы ресурсов. Также можно повторно использовать то же имя группы ресурсов и очистить старое с помощью скрипта. Для этого следует добавить параметр `-RemoveExistingResourceGroup` к вызову скрипта.

### Результат выполнения скрипта CreateElasticSearchCluster
После выполнения скрипта `CreateElasticSearchCluster` будут созданы следующие основные артефакты. Для простоты предположим, что для параметра `dnsNameForLoadBalancerIP` указано значение «myBigCluster», а кластер создан в регионе «Запад США».

|Артефакт|Имя, расположение и примечания|
|----------------------------------|----------------------------------|
|Ключ SSH для удаленного администрирования |Файл myBigCluster.key (в каталоге, из которого запущен скрипт CreateElasticSearchCluster). <br /><br />Это ключ, который может использоваться для подключения к узлу администрирования и (через узел администрирования) к узлам данных в кластере.|
|Узел администрирования |myBigCluster-admin.westus.cloudapp.azure.com <br /><br />Это специальная виртуальная машина для удаленного администрирования кластера ElasticSearch. Только она может разрешать внешние подключения по протоколу SSH. Эта VM работает в той же виртуальной сети, что и остальные узлы кластера ElasticSearch, но не запускает службы ElasticSearch.|
|Узлы данных |myBigCluster1 — myBigCluster*N* <br /><br />Узлы данных, на которых работают службы ElasticSearch и Kibana. К каждому узлу можно подключиться по протоколу SSH, но только через узел администрирования.|
|Кластер ElasticSearch |http://myBigCluster.westus.cloudapp.azure.com/es/ <br /><br />Выше указана основная конечная точка кластера ElasticSearch (обратите внимание на суффикс /es). Кластер защищен с помощью обычной проверки подлинности HTTP (учетные данные указаны в параметрах esUserName/esPassword шаблона ES-MultiNode). Кроме того, в кластере установлен подключаемый модуль head (http://myBigCluster.westus.cloudapp.azure.com/es/_plugin/head) для выполнения основных задач администрирования.|
|Служба Kibana |http://myBigCluster.westus.cloudapp.azure.com <br /><br />Служба Kibana отображает данные из созданного кластера ElasticSearch. Для проверки подлинности она использует те же учетные данные, что и сам кластер.|

## Внутрипроцессная и внепроцессная запись данных трассировки
Во введении мы упомянули два основных способа сбора диагностических данных: внутрипроцессный и внепроцессный. Каждый имеет свои преимущества и недостатки.

**Внутрипроцессная запись данных трассировки** имеет следующие преимущества.

1. *Простая настройка и развертывание*.

    * Настройка сбора диагностических данных является лишь частью настройки приложения. Регулярно синхронизировать ее с остальным приложением довольно просто.

    * Настройка каждого приложения или службы также является несложной задачей.

    * Внепроцессная запись данных трассировки обычно требует отдельного развертывания и настройки агента диагностики. Как правило, это прибавляет работы администратору и является источником ошибок. Часто технология определенного агента разрешает использовать только один его экземпляр на виртуальной машине (узле). Это означает, что конфигурация для сбора настроек диагностики является общей для всех приложений и служб, запущенных на этом узле.

2. *Гибкость*

    * Приложение может отправлять данные куда угодно при наличии клиентской библиотеки, поддерживающей систему хранения целевых данных. При необходимости можно добавлять новые приемники.

    * Можно реализовать сложные правила сбора, фильтрации и объединения данных.

    * Внепроцессная запись данных трассировки часто ограничивается приемниками данных, которые поддерживает агент. Некоторые агенты являются расширяемыми.

3. *Доступ к внутренним данным и контексту приложения*.

    * В подсистему диагностики, которая работает внутри процесса приложения или службы, можно легко добавить трассировку с помощью контекстной информации.

    * В случае внепроцессной записи данные должны отправляться агенту с помощью механизма межпроцессного взаимодействия, к примеру трассировки событий Windows. Однако при этом могут возникнуть дополнительные ограничения.

**Внепроцессная запись данных трассировки** имеет следующие преимущества.

1. *Возможность отслеживания приложения и сбора аварийных дампов*.

    * Внутрипроцессная запись данных трассировки может быть неудачной, если приложение не удается запустить или оно прекращает работу из-за сбоя. Независимый агент имеет гораздо больше шансов собрать важные сведения для устранения неполадок.<br /><br />

2. *Зрелость, надежность и высокая производительность*.

    * Агент, разработанный поставщиком платформы (например, агент системы диагностики Microsoft Azure), прошел тщательное тестирование и испытан в рабочих условиях.

    * Будьте осторожны, выбирая внутрипроцессную запись данных трассировки. Следите за тем, чтобы отправка диагностических данных из процесса приложения не повлияла на его основные задачи и не привела к проблемам со своевременным выполнением задач или производительностью. Независимый агент менее подвержен подобным проблемам и, как правило, специально разработан с минимальным влиянием на систему.

Безусловно, можно объединить и использовать преимущества обоих методов. Во многих случаях это будет лучшим решением.

В этой статье мы будем использовать **библиотеку Microsoft.Diagnostic.Listeners** и внутрипроцессную запись данных трассировки для отправки данных из приложения Service Fabric в кластер ElasticSearch.

## Использование библиотеки Listeners для передачи диагностических данных в ElasticSearch
Библиотека Microsoft.Diagnostic.Listeners входит в пример приложения Fabric с именем Party Cluster. Чтобы ее использовать, сделайте следующее:

1. Скачайте [пример Party Cluster](https://github.com/Azure-Samples/service-fabric-dotnet-management-party-cluster) с сайта GitHub.

2. Скопируйте проекты Microsoft.Diagnostics.Listeners и Microsoft.Diagnostics.Listeners.Fabric (папки полностью) из каталога примера приложения Party Cluster в папку решения приложения, которое должно отправлять данные в ElasticSearch.

3. Откройте это решение, щелкните правой кнопкой мыши узел решения в обозревателе решений и выберите «Добавить существующий проект». Добавьте в решение проект Microsoft.Diagnostics.Listeners. Повторите те же действия с проектом Microsoft.Diagnostics.Listeners.Fabric.

4. Добавьте из проектов своей службы ссылку на два добавленных проекта (каждая служба, которая будет отправлять данные в ElasticSearch, должна ссылаться на Microsoft.Diagnostics.EventListeners и Microsoft.Diagnostics.EventListeners.Fabric).

    ![Проект ссылается на библиотеки Microsoft.Diagnostics.EventListeners и Microsoft.Diagnostics.EventListeners.Fabric][1]

### Предварительная версия Service Fabric и пакета Microsoft.Diagnostics.Tracing NuGet (ноябрь 2015 г.)
Приложения, созданные с помощью предварительной версии Service Fabric, выпущенной в ноябре 2015 г., предназначены для платформы **.NET Framework 4.5.1**. Это самая последняя версия .NET Framework, которая поддерживается Azure на момент выхода предварительной версии. К сожалению, в этой версии платформы отсутствуют некоторые API-интерфейсы EventListener, необходимые для библиотеки Microsoft.Diagnostics.Listeners. EventSource (компонент, на котором строятся API-интерфейсы ведения журналов в приложениях Fabric) и EventListener тесно связаны. Поэтому все проекты, использующие библиотеку Microsoft.Diagnostics.Listeners, должны использовать альтернативную реализацию EventSource. Она входит в **пакет NuGet Microsoft.Diagnostics.Tracing**, разработанный Майкрософт. Этот пакет обратно совместим с компонентом EventSource, который входит в платформу. Следовательно, никакие правки в код вносить не нужно, разве что могут потребоваться изменения указанного пространства имен.

Чтобы начать работу с Microsoft.Diagnostics.Tracing, реализацией класса EventSource, выполните следующие действия для каждого проекта службы, который должен отправлять данные в ElasticSearch.

1. Щелкните проект службы правой кнопкой мыши и выберите «Управление пакетами NuGet».

2. Перейдите в источник пакета nuget.org (если он еще не выбран) и найдите «Microsoft.Diagnostics.Tracing».

3. Установите пакет `Microsoft.Diagnostics.Tracing.EventSource` (и его зависимости).

4. Откройте файл ServiceEventSource.cs или ActorEventSource.cs в проекте службы и замените директиву `using System.Diagnostics.Tracing` в верхней части файла директивой `using Microsoft.Diagnostics.Tracing`.

Эти действия будут не нужны, когда в Microsoft Azure появится поддержка **.NET Framework 4.6**.

### Создание и настройка экземпляра прослушивателя ElasticSearch
Последним шагом, необходимым для отправки диагностических данных в ElasticSearch, является создание экземпляра `ElasticSearchListener` и его настройка с помощью данных подключения ElasticSearch. Прослушиватель будет автоматически записывать все события, вызванные через классы EventSource, заданные в проекте службы. Он должен быть активным в течение всего времени существования службы, поэтому лучше всего создать его в коде инициализации службы. Вот как может выглядеть код инициализации службы без отслеживания состояния после внесения необходимых изменений (добавлений, указанных в комментариях, начиная с `****`):

```csharp
using System;
using System.Diagnostics;
using System.Fabric;
using System.Threading;

// **** Add the following directives
using Microsoft.Diagnostics.EventListeners;
using Microsoft.Diagnostics.EventListeners.Fabric;

namespace Stateless1
{
    public class Program
    {
        public static void Main(string[] args)
        {
            try
            {
                using (FabricRuntime fabricRuntime = FabricRuntime.Create())
                {

                    // **** Instantiate ElasticSearchListener
                    var configProvider = new FabricConfigurationProvider("ElasticSearchEventListener");
                    ElasticSearchListener esListener = null;
                    if (configProvider.HasConfiguration)
                    {
                        esListener = new ElasticSearchListener(configProvider);
                    }

                    // This is the name of the ServiceType that is registered with FabricRuntime.
                    // This name must match the name defined in the ServiceManifest. If you change
                    // this name, please change the name of the ServiceType in the ServiceManifest.
                    fabricRuntime.RegisterServiceType("Stateless1Type", typeof(Stateless1));

                    ServiceEventSource.Current.ServiceTypeRegistered(
						Process.GetCurrentProcess().Id,
						typeof(Stateless1).Name);

                    Thread.Sleep(Timeout.Infinite);

                    // **** Ensure that the ElasticSearchListner instance is not garbage-collected prematurely
                    GC.KeepAlive(esListener);

                }
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

Данные подключения ElasticSearch необходимо поместить в отдельный раздел файла конфигурации службы (PackageRoot\\Config\\Settings.xml). Имя раздела должно соответствовать значению, которое передается конструктору `FabricConfigurationProvider`, например:

```xml
<Section Name="ElasticSearchEventListener">
  <Parameter Name="serviceUri" Value="http://myBigCluster.westus.cloudapp.azure.com/es/" />
  <Parameter Name="userName" Value="(ES user name)" />
  <Parameter Name="password" Value="(ES user password)" />
  <Parameter Name="indexNamePrefix" Value="myapp" />
</Section>
```
Значения `serviceUri`, `userName` и `password` соответствуют адресу конечной точки кластера ElasticSearch, имени пользователя и паролю ElasticSearch соответственно. `indexNamePrefix` является префиксом для индексов ElasticSearch. Библиотека Microsoft.Diagnostics.Listeners ежедневно создает новые индексы для своих данных.

### Проверка
Вот и все. Теперь при запуске служба будет отправлять данные трассировки в службу ElasticSearch, указанную в конфигурации. Это можно проверить, открыв интерфейс Kibana, связанный с целевым экземпляром ElasticSearch (в нашем примере адрес страницы будет таким: http://myBigCluster.westus.cloudapp.azure.com/). Он поможет удостовериться, что индексы с префиксом имени, выбранным для экземпляра `ElasticSearchListener`, действительно создаются и заполняются данными.

![Отображение событий приложения PartyCluster в службе Kibana][2]

## Дальнейшие действия
- [Дополнительные сведения о диагностике и мониторинге службы Service Fabric](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

<!--Image references-->
[1]: ./media/service-fabric-diagnostics-how-to-use-elasticsearch/listener-lib-references.png
[2]: ./media/service-fabric-diagnostics-how-to-use-elasticsearch/kibana.png

<!---HONumber=AcomDC_1217_2015-->