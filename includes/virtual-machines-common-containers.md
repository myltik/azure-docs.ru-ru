Облачные решения Azure на основе виртуальных машин(эмуляции оборудования физического компьютера) обеспечивают гибкую упаковку развертывания программного обеспечения и большую эффективность консолидации ресурсов, чем в случае использования физического оборудования. Контейнеры [Docker](https://www.docker.com) и экосистема docker значительно расширили способы разработки, поставки и администрирования распределенного программного обеспечения. Код приложения в контейнере изолирован от виртуальной машины узла и других контейнеров в той же виртуальной машине. Такая изоляция обеспечивает большую гибкость при разработке и развертывании.

Azure предлагает следующие значения Docker:

* [много](../articles/virtual-machines/virtual-machines-linux-docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) [разных](../articles/virtual-machines/virtual-machines-linux-dockerextension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) способов создания узлов Docker для контейнеров в соответствии с конкретной ситуацией;
* [Служба контейнеров Azure](https://azure.microsoft.com/documentation/services/container-service/) создает кластеры узлов контейнеров с помощью таких оркестраторов, как **Marathon** и **Swarm**;
* [Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md) и [шаблоны групп ресурсов](../articles/resource-group-authoring-templates.md) упрощают развертывание и обновление сложных распределенных приложений;
* интеграцию с большим массивом частных инструментов управления конфигурациями и инструментов управления конфигурациями с открытым исходным кодом.

Так как вы можете программно создавать виртуальные машины и контейнеры Linux в Azure, то можете также использовать инструменты *оркестрации* виртуальных машин и контейнеров для создания групп виртуальных машин и развертывания приложений в контейнерах Linux, а теперь и в [контейнерах Windows](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview).

В этой статье не только рассматриваются общие черты этих понятий, но и содержится множество ссылок на дополнительную информацию, учебники и продукты, связанные с использованием контейнеров и кластеров в Azure. Если вы все это знаете и вам нужны только ссылки, их можно найти в разделе об [инструментах для работы с контейнерами](#tools-for-working-with-azure-vms-and-containers).

## <a name="the-difference-between-virtual-machines-and-containers"></a>Разница между виртуальными машинами и контейнерами
Виртуальные машины работают в изолированной аппаратной среде виртуализации, предоставляемой [гипервизором](http://en.wikipedia.org/wiki/Hypervisor). В Azure служба [виртуальных машин](https://azure.microsoft.com/services/virtual-machines/) делает все за вас: вы только создаете виртуальные машины, выбрав операционную систему и настроив ее для запуска или загрузив собственный пользовательский образ виртуальной машины. Виртуальные машины — это проверенная временем, "закаленная" технология, и для нее уже существует множество различных инструментов по управлению ОС и приложениями, которые они содержат.  Приложения на виртуальной машине скрыты для ОС узла. С точки зрения приложения или пользователя, который работает на виртуальной машине, виртуальная машина является автономным физическим компьютером.

[Контейнеры Linux](http://en.wikipedia.org/wiki/LXC), в том числе созданные и размещенные с помощью инструментов Docker, не используют гипервизор для изоляции. Вместо этого узел контейнера использует изоляцию процессов и файловой системы в ядре Linux для получения контейнером (и его приложением) доступа к определенным компонентам ядра и собственной изолированной файловой системе. С точки зрения приложения, работающего в контейнере, этот контейнер является экземпляром уникальной ОС. Приложения в контейнере не могут видеть процессы или другие ресурсы за пределами своего контейнера.

В отличие от виртуальной машины, в контейнере Docker используется гораздо меньше ресурсов. Контейнеры Docker используют модели изоляции и выполнения приложения, которые не используют ядро узла Docker совместно. Так как контейнер не содержит целую ОС, он занимает намного меньше дискового пространства. Кроме того, он использует гораздо меньше места на диске и требует меньше времени для запуска, чем виртуальная машина.
Контейнеры Windows предоставляют те же преимущества, что и контейнеры Linux для приложений, работающих в Windows. Контейнеры Windows поддерживают формат образов Docker и API Docker, но ими можно также управлять с помощью PowerShell. Для работы с контейнерами Windows, Windows Server и Hyper-V доступны две среды выполнения. Контейнеры Hyper-V обеспечивают дополнительный уровень защиты, так как каждый контейнер размещается на сверхоптимизированной виртуальной машине. Дополнительные сведения о контейнерах Windows см. в разделе [Контейнеры Windows](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview). Чтобы начать работу с контейнерами Windows в Azure, ознакомьтесь со статьей [Развертывание кластера службы контейнеров Azure](/articles/container-service/container-service-deployment.md).

## <a name="what-are-containers-good-for"></a>Для чего же контейнеры подходят лучше всего?

Контейнеры могут улучшить следующие показатели:

* скорость разработки и распространения кода приложения;
* скорость и надежность тестирования приложения;
* скорость и надежность развертывания приложения.

Контейнеры выполняются на узле контейнеров (в операционной системе), и в Azure это означает виртуальную машину Azure. Даже если вам уже нравится идея контейнеров, вам все еще требуется инфраструктура виртуальных машин для размещения контейнеров, но преимущество этого состоит в том, что для контейнеров не важно, в каких виртуальных машинах они работают (хотя для контейнера имеет значение среда выполнения — Linux или Windows, например).


## <a name="what-are-containers-good-for"></a>Для чего же контейнеры подходят лучше всего?
Они отлично подходят для многого, но мы советуем использовать их (в случае [облачных служб Azure](https://azure.microsoft.com/services/cloud-services/) и [Azure Service Fabric](../articles/service-fabric/service-fabric-overview.md)) для создания ориентированных на микрослужбы распределенных приложений с одной службой, когда структура приложения состоит из нескольких небольших составных частей, а не компонентов большего размера, которые сильнее связаны друг с другом.

Это особенно важно в общедоступном облаке, например в Azure, где можно сдавать виртуальные машины там, где нужно, и тогда, когда нужно. Вы получаете не только изоляцию, быстрое развертывание и инструменты оркестрации, но и можете создавать более эффективные решения для инфраструктуры приложений.

Например, у вас есть развертывание, состоящее из 9 виртуальных машин Azure большого размера для высокодоступного распределенного приложения. Если компоненты этого приложения можно развернуть в контейнерах, можно использовать только 4 виртуальных машины и развернуть компоненты приложения в 20 контейнерах для обеспечения избыточности и балансировки нагрузки.

Это лишь пример, конечно же, но если реализовать это в вашем сценарии, то на случай пиков использования можно добавить дополнительные контейнеры, а не дополнительные виртуальные машины Azure, и использовать оставшуюся общую нагрузку на ЦП гораздо эффективнее, чем ранее.

Кроме того, существует множество сценариев, которые не подходят для реализации микрослужб. Вы будете лучше знать, помогут ли вам микрослужбы и контейнеры.

### <a name="container-benefits-for-developers"></a>Преимущества контейнеров для разработчиков
Как правило, нетрудно увидеть, что технология контейнеров — это шаг вперед, но здесь кроются также и более специфичные преимущества. Рассмотрим пример контейнеров Docker. В этом разделе мы не будем углубляться в возможности Docker (дополнительные сведения можно найти в разделе [What is Docker?](https://www.docker.com/whatisdocker/) (Возможности Docker) или на соответствующей странице [Википедии](http://wikipedia.org/wiki/Docker_%28software%29)), но Docker и его экосистема предоставляют значительные преимущества разработчиками и ИТ-специалистам.

Разработчики могут быстро перейти на контейнеры Docker, так как в первую очередь это упрощает использование контейнеров Linux и Windows.

* Они могут использовать простые, составные команды для создания основного образа, который можно легко развернуть, и автоматизировать создание таких образов с помощью dockerfile.
* Они могут совместно использовать эти образы с помощью простых команд запроса и передачи в стиле [Git](https://git-scm.com/) для [открытых](https://registry.hub.docker.com/) или [закрытых реестров Docker](../articles/virtual-machines/virtual-machines-linux-docker-registry-in-blob-storage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Они могут использовать изолированные компоненты приложений вместо компьютеров.
* Они могут использовать большое количество инструментов, которые понимают контейнеры Docker и различные основные образы.

### <a name="container-benefits-for-operations-and-it-professionals"></a>Преимущества контейнеров для производственных и ИТ-специалистов
Производственные и ИТ-специалисты также получают преимущества, сочетая контейнеры и виртуальные машины.

* Службы в контейнерах изолируются от среды выполнения узла виртуальной машины.
* Код в контейнерах идентичен, и это можно проверить.
* Службы в контейнерах можно запускать, останавливать и быстро перемещать между средами разработки, тестирования и рабочей средой.

Эти и другие функции крайне актуальны для развитых предприятий, на которых профильным ИТ-организациям приходится корректировать ресурсы, включая чисто производственные мощности, для задач, которые должны не только обеспечивать сохранение функциональности, но и повышение удовлетворенности и доступности заказчиков. Малый бизнес, независимые поставщики программного обеспечения и стартапы — их требования мало отличаются, но они могут описываться по-разному.

## <a name="what-are-virtual-machines-good-for"></a>Какие виртуальные машины подходят лучше всего?
Виртуальные машины, бесспорно, являются фундаментом облачных вычислений. Если виртуальные машины запускаются медленнее, занимают больше дискового пространства и не сопоставляются непосредственно с архитектурой микрослужб, они имеют ряд очень важных преимуществ:

1. по умолчанию они обладают гораздо более надежной защитой для узла;
2. они поддерживают все основные операционные системы и конфигурации приложений;
3. они оснащены проверенными временем экосистемами инструментов управления;
4. они предоставляют среду выполнения для размещения контейнеров.

Последний элемент важен, так как автономные приложения по-прежнему требуют определенной операционной системы и типа ЦП, в зависимости от вызовов, осуществляемых приложением. Важно помнить, что контейнеры устанавливаются на виртуальных машинах, так как они содержат приложения, которые требуется развернуть. Контейнеры не являются заменой операционных систем или виртуальных машин.

## <a name="high-level-feature-comparison-of-vms-and-containers"></a>Сравнение основных функций виртуальных машин и контейнеров
В следующей таблице описаны основные различия между функциями (не вдаваясь в подробности) виртуальных машин и контейнеров Linux. Обратите внимание, что некоторые функции могут быть более или менее желательными в зависимости от требований приложения, и, как и с любым другим ПО, при незначительных усилиях можно получить поддержку дополнительных функций, особенно с точки зрения безопасности.

| Функция | Виртуальные машины | Контейнеры |
|:--- | --- | --- |
| Поддержка безопасности «по умолчанию» |В большей степени |Немного в меньшей степени |
| Требуется память на диске |Полная версия ОС и приложения |Только требования приложений |
| Время для запуска |Значительно дольше: загрузка ОС и приложений |Значительно быстрее: необходим только запуск приложений, так как ядро уже работает |
| Переносимость |Переносимость при правильной подготовке |Переносимость в формате образа, обычно ниже |
| Автоматизация образов |Зависит от ОС и приложений |[Реестр Docker](https://registry.hub.docker.com/)и другие |

## <a name="creating-and-managing-groups-of-vms-and-containers"></a>Создание групп виртуальных машин и контейнеров и управление ими
На этом этапе любой архитектор, разработчик, производственный или ИТ-специалист может подумать, что можно ВСЕ это автоматизировать — ведь это НАСТОЯЩИЙ «ЦОД как служба».

И это действительно так. Во множестве систем, многие из которых, вероятно, вы уже используете, можно управлять группами виртуальных машин Azure и внедрять пользовательский код с помощью скриптов, в частности [CustomScriptingExtension для Windows](https://msdn.microsoft.com/library/azure/dn781373.aspx) или [CustomScriptingExtension для Linux](https://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/). Вы можете автоматизировать развертывание Azure (и возможно, вы уже сделали это) с помощью сценариев PowerShell или интерфейса командной строки Azure.

Эти возможности часто затем переносятся в такие инструменты, как [Puppet](https://puppetlabs.com/) и [Chef](https://www.chef.io/), для автоматизации создания и настройки виртуальных машин с возможностью масштабирования. (Ссылки на эти инструменты в Azure см. [здесь](#tools-for-working-with-containers).)

### <a name="azure-resource-group-templates"></a>Шаблоны групп ресурсов Azure
Недавно в Azure выпущен интерфейс REST API [управления ресурсами Azure](../articles/resource-manager-deployment-model.md), а также обновленные инструменты интерфейса командной строки Azure и PowerShell, которыми легко пользоваться. С помощью [шаблонов диспетчера ресурсов Azure](../articles/resource-group-authoring-templates.md) и API управления ресурсами Azure можно выполнять развертывание, изменение или повторное развертывание топологий всего приложения, используя:

* [портал Azure и шаблоны](https://github.com/Azure/azure-quickstart-templates)&mdash; (примечание: используйте кнопку DeployToAzure);
* [интерфейс командной строки Azure](../articles/virtual-machines/virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json);
* [модули Azure PowerShell](../articles/virtual-machines/virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="deployment-and-management-of-entire-groups-of-azure-vms-and-containers"></a>Развертывание всей группы виртуальных машин Azure и контейнеров и управление ими
Существует несколько популярных систем, которые позволяют развертывать целые группы виртуальных машин и устанавливать Docker (или другие узлы контейнеров Linux) в виде автоматизируемой группы. Прямые ссылки см. в разделе [контейнеров и инструментов](#containers-and-vm-technologies) ниже. Существует несколько систем, которые в большей или меньшей степени позволяют сделать это, и этот список не является исчерпывающим. В зависимости от доступных навыков и сценариев, они могут быть или не быть полезными.

У Docker есть собственный набор инструментов для создания виртуальных машин ([Docker machine](../articles/virtual-machines/virtual-machines-linux-docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) и инструмент управления кластерами контейнеров Docker с балансировкой нагрузки ([Swarm](../articles/virtual-machines/virtual-machines-linux-docker-swarm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Кроме того, [расширение для виртуальных машин Docker для Azure](https://github.com/Azure/azure-docker-extension/blob/master/README.md) по умолчанию поддерживает [`docker-compose`](https://docs.docker.com/compose/), который позволяет развертывать настроенные контейнеры приложений в нескольких контейнерах.

Вы также можете попробовать ОС [Data Center Operating System (DCOS)](http://docs.mesosphere.com/install/azurecluster/)от Mesosphere. DCOS основана на проекте с открытым исходным кодом [mesos](http://mesos.apache.org/) для ядра распределенных систем, который позволяет реализовать центр обработки данных как одну адресуемую службу. DCOS оснащена встроенными пакетами для нескольких важных систем, таких как [Spark](http://spark.apache.org/) и [Kafka](http://kafka.apache.org/) (и др.), а также встроенными службами, такими как [Marathon](https://mesosphere.github.io/marathon/) (система управления контейнерами) и [Chronos](https://mesos.github.io/chronos/) (распределенный планировщик). Mesos создан с учетом уроков, полученных при развитии Twitter, AirBnb и других веб-масштабируемых компаний. Можно также использовать **swarm** в качестве механизма оркестрации.

Кроме того, [kubernetes](https://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure/) — это система для управления группами виртуальных машин и контейнеров с открытым исходным кодом, созданная с учетом опыта Google. Вы даже можете использовать [kubernetes совместно с weave для обеспечения поддержки сети](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave).

[Deis](http://deis.io/overview/) является проектом с открытым исходным кодом класса "Платформа как услуга" (PaaS), который упрощает развертывание приложений и управление ими на собственных серверах. Deis собран на основе Docker и CoreOS для предоставления упрощенной PaaS и рабочего процесса в стиле Heroku. Вы можете легко [создать 3-узловую группу виртуальных машин Azure и установить Deis](../articles/virtual-machines/virtual-machines-linux-deis-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) в Azure, а затем [установить приложение Hello World Go](../articles/virtual-machines/virtual-machines-linux-deis-cluster.md#deploy-and-scale-a-hello-world-application).

[CoreOS](https://coreos.com/os/docs/latest/booting-on-azure.html) — это оптимизированный по размеру дистрибутив Linux с поддержкой Docker, собственной системой контейнеров [rkt](https://github.com/coreos/rkt) и инструментом управления группами контейнеров под названием [fleet](https://coreos.com/using-coreos/clustering/).

Ubuntu — другой популярный дистрибутив Linux, который очень хорошо поддерживает Docker и при этом также поддерживает [кластеры Linux (LXC)](https://help.ubuntu.com/lts/serverguide/lxc.html).

## <a name="tools-for-working-with-azure-vms-and-containers"></a>Инструменты для работы с виртуальными машинами и контейнерами Azure
Для работы с контейнерами и виртуальными машинами в Azure используются определенные инструменты. В этом разделе содержится перечень лишь некоторых из наиболее полезных или важных основных понятий и инструментов для работы с контейнерами, группами и крупными конфигурациями, а также используемые с ними инструменты оркестрации.

> [!NOTE]
> В этой области изменения происходят невероятно быстро, и хотя мы сделаем все возможное для сохранения актуальности этого раздела и ссылок в нем, это может оказаться непосильной задачей. Чтобы всегда быть в курсе происходящего, воспользуйтесь поиском интересующих вас тем!
>
>

### <a name="containers-and-vm-technologies"></a>Технологии контейнеров и виртуальных машин
Некоторые технологии контейнеров Linux:

* [Docker](https://www.docker.com)
* [LXC](https://linuxcontainers.org/)
* [CoreOS и rkt](https://github.com/coreos/rkt)
* [Open Container Project](http://opencontainers.org/)
* [RancherOS](http://rancher.com/rancher-os/)

Ссылки на контейнеры Windows:

* [Контейнеры Windows](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview)

Ссылки на Visual Studio Docker:

* [Инструменты Visual Studio 2015 RC для Docker. Версия для предварительного просмотра](https://visualstudiogallery.msdn.microsoft.com/6f638067-027d-4817-bcc7-aa94163338f0)

Инструменты Docker:

* [Управляющая программа Docker](https://docs.docker.com/installation/#installation)
* Клиенты Docker
  * [Клиент Windows Docker на Chocolatey](https://chocolatey.org/packages/docker)
  * [Указания по установке Docker](https://docs.docker.com/installation/#installation)

Docker в Microsoft Azure:

* [Расширение виртуальных машин Docker для Linux в Azure](../articles/virtual-machines/virtual-machines-linux-dockerextension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Руководство пользователя для Azure Docker VM Extension](https://github.com/Azure/azure-docker-extension/blob/master/README.md)
* [Использование расширения виртуальных машин Docker в интерфейсе командной строки Azure (CLI Azure)](../articles/virtual-machines/virtual-machines-linux-classic-cli-use-docker.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Использование расширения виртуальных машин Docker на портале Azure](../articles/virtual-machines/virtual-machines-linux-classic-portal-use-docker.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Использование docker-machine в Azure](../articles/virtual-machines/virtual-machines-linux-docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Как использовать Docker со Swarm в Azure](../articles/virtual-machines/virtual-machines-linux-docker-swarm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Начало работы с решениями Docker и Compose в Azure](../articles/virtual-machines/virtual-machines-linux-docker-compose-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Использование шаблона группы ресурсов Azure для быстрого создания узла Docker в Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu)
* [Встроенная поддержка `compose`](https://github.com/Azure/azure-docker-extension#11-public-configuration-keys) для приложений в контейнерах
* [Реализация закрытого реестра Docker в Azure](../articles/virtual-machines/virtual-machines-linux-docker-registry-in-blob-storage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Дистрибутивы Linux и примеры для Azure:

* [CoreOS](https://coreos.com/os/docs/latest/booting-on-azure.html)

настройка, управление кластером и оркестрация контейнеров:

* [Fleet в CoreOS](https://coreos.com/using-coreos/clustering/)
* Deis

  * [Создание 3-узловой группы виртуальных машин Azure, установка Deis и запуск приложения Hello World Go](../articles/virtual-machines/virtual-machines-linux-deis-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* kubernetes

  * [Полное руководство по автоматизированному развертыванию кластера Kubernetes с помощью CoreOS и Weave](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave)
  * [Kubernetes Visualizer](https://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure/)
* [Mesos](http://mesos.apache.org/)

  * [Mesosphere Data Center Operating System (DCOS)](http://beta-docs.mesosphere.com/install/azurecluster/)
* [Jenkins](https://jenkins-ci.org/) и [Hudson](http://hudson-ci.org/)

  * [Блог. Подключаемый модуль ведомого режима Jenkins для Azure](http://msopentech.com/blog/2014/09/23/announcing-jenkins-slave-plugin-azure/)
  * [Репозиторий GitHub. Подключаемый модуль хранилища Jenkins для Azure](https://github.com/jenkinsci/windows-azure-storage-plugin)
  * [Сторонние решения. Подключаемый модуль ведомого режима Hudson для Azure](http://wiki.hudson-ci.org/display/HUDSON/Azure+Slave+Plugin)
  * [Сторонние решения. Подключаемый модуль хранилища Hudson для Azure](https://github.com/hudson3-plugins/windows-azure-storage-plugin)
* [Служба автоматизации Azure](https://azure.microsoft.com/services/automation/)

  * [Видео. Использование службы автоматизации Azure для виртуальных машин Linux](http://channel9.msdn.com/Shows/Azure-Friday/Azure-Automation-104-managing-Linux-and-creating-Modules-with-Joe-Levy)
* Powershell DSC для Linux

  * [Блог. Как сделать Powershell DSC для Linux](http://blogs.technet.com/b/privatecloud/archive/2014/05/19/powershell-dsc-for-linux-step-by-step.aspx)
  * [Github. DSC клиента Docker](https://github.com/anweiss/DockerClientDSC)

## <a name="next-steps"></a>Дальнейшие действия
См. документацию по [Docker](https://www.docker.com) и [контейнерам Windows](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview).

<!--Anchors-->
[microservices]: http://martinfowler.com/articles/microservices.html
[microservice]: http://martinfowler.com/articles/microservices.html
<!--Image references-->


<!--HONumber=Feb17_HO3-->


