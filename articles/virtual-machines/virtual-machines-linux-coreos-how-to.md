<properties
	pageTitle="Использование CoreOS | Microsoft Azure"
	description="Описывает CoreOS, создание кластера виртуальных машин CoreOS в Azure по классической модели развертывания и основы его использования."
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="10/21/2015"
	ms.author="rasquill"/>

# Использование CoreOS в Azure

В этом разделе описывается [CoreOS] и показывается, как создать кластер из трех виртуальных машин CoreOS в Azure, чтобы быстро освоить эту операционную систему. Используются основные элементы развертываний CoreOS и примеры из статей [CoreOS в Azure], [учебник Тима Парка (Tim Park) по CoreOS] и [учебник Патрика Чейзона (Patrick Chanezon) по CoreOS] для демонстрации минимальных требований для понимания базовой структуры развертывания CoreOS и успешного создания кластера из трех виртуальных машин.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager deployment model](https://azure.microsoft.com/documentation/templates/coreos-with-fleet-multivm/).


## CoreOS, кластеры и контейнеры Linux

CoreOS — это облегченная версия Linux, разработанная для поддержки быстрого создания потенциально очень крупных кластеров виртуальных машин, в которых в качестве единственного механизма упаковки используются контейнеры Linux, включая контейнеры [Docker]. CoreOS предназначена для поддержки:

+ очень высокого уровня автоматизации;
+ более удобного и согласованного развертывания приложений;
+ масштабируемости на уровне приложений и системы.

В общих чертах, эти цели поддерживаются следующими возможностями CoreOS:

1. Система, состоящая из одного пакета. В CoreOS выполняются только образы контейнеров Linux, которые работают в контейнерах Linux. Это обеспечивает быстродействие, единообразие и удобство развертывания.
2. Обновления операционной системы выполняются автоматически. Поэтому операционные системы обновляются как одна сущность, и можно с легкостью выполнить откат к предыдущему известному состоянию.
3. Встроенные демоны (службы) [etcd](https://github.com/coreos/etcd) и [fleet](https://github.com/coreos/fleet) для динамического подключения виртуальных машин и кластеров и управления ими.

Это самое общее описание ОС CoreOS и ее возможностей. Более подробную информацию об ОС CoreOS см. в статье [Обзор CoreOS].

## Вопросы безопасности
В настоящее время в CoreOS предполагается, что пользователи с правами подключения к кластеру по SSH имеют права на управление им. В результате кластеры CoreOS отлично подходят для сред тестирования и разработки без каких-либо изменений, однако в производственной среде необходимо внедрить дополнительные меры безопасности.

## Использование CoreOS в Azure

В этом разделе описывается создание облачной службы Azure с тремя виртуальными машинами CoreOS с помощью [интерфейса командной строки Azure (Azure CLI)]. Основные этапы:

1. Создайте сертификаты и ключи SSH для безопасного взаимодействия с виртуальной машиной CoreOS
2. Получите идентификатор etcd кластера для внутренних взаимодействий
3. Создайте файл cloud-config в формате [YAML]
4. Используйте Azure CLI для создания новой облачной службы Azure и трех виртуальных машин CoreOS.
5. Тестирование кластера CoreOS из виртуальной машины Azure.
6. Протестируйте кластер CoreOS из локального узла

### Создайте открытый и закрытый ключи для обмена данными.

Выполните инструкции из раздела [Использование SSH с Linux в Azure](virtual-machines-linux-use-ssh-key.md) для создания открытого и закрытого ключей для SSH. (Основные действия описаны ниже.) Вы будете использовать эти ключи для подключения к виртуальным машинам в кластере для проверки их работоспособности и взаимодействия друг с другом.

> [AZURE.NOTE] В этом разделе предполагается, что у вас нет этих ключей и вы должны создать файлы `myPrivateKey.pem` и `myCert.pem`. Если у вас уже есть открытый и закрытый ключи, сохраненные в `~/.ssh/id_rsa`, вы можете просто ввести `openssl req -x509 -key ~/.ssh/id_rsa -nodes -days 365 -newkey rsa:2048 -out myCert.pem`, чтобы получить PEM-файл, который необходимо передать в Azure.

1. В рабочем каталоге введите `openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem`, чтобы создать закрытый ключ и связанный с ним сертификат X.509.

2. Чтобы подтвердить, что владелец закрытого ключа может выполнить чтение и запись файла, введите `chmod 600 myPrivateKey.key`.

В рабочем каталоге должны быть оба файла, `myPrivateKey.key` и `myCert.pem`.


### Получение идентификатора etcd кластера

Управляющей программе CoreOS `etcd` требуется идентификатор обнаружения для автоматического запроса всех узлов в кластере. Чтобы получить идентификатор обнаружения и сохранить его в файл `etcdid`, введите

```
curl https://discovery.etcd.io/new | grep ^http.* > etcdid
```

### Создание файла конфигурации облака

В том же рабочем каталоге с помощью любого текстового редактора создайте файл, содержащий следующий текст, и сохраните его как `cloud-config.yaml`. (Можно сохранить его с другим именем файла на ваш выбор, но при создании виртуальных машин на следующем шаге необходимо будет указать имя этого файла в параметре **--custom-data** команды **azure vm create**.)

> [AZURE.NOTE] Не забудьте ввести `cat etcdid` для получения идентификатора обнаружения etcd из файла `etcdid`, созданного ранее, и заменить `<token>` в следующем файле `cloud-config.yaml` номером, созданным из вашего файла `etcdid`. Если в конце вы не сможете проверить работу вашего кластера, возможно, именно этот шаг вы упустили!

```
#cloud-config

coreos:
  etcd:
    # generate a new token for each unique cluster from https://discovery.etcd.io/new
    discovery: https://discovery.etcd.io/<token>
    # deployments across multiple cloud services will need to use $public_ipv4
    addr: $private_ipv4:4001
    peer-addr: $private_ipv4:7001
  units:
    - name: etcd.service
      command: start
    - name: fleet.service
      command: start
```

(Дополнительную информацию о файле cloud-config см. в разделе [Использование Cloud-Config](https://coreos.com/docs/cluster-management/setup/cloudinit-cloud-config/) документации по CoreOS.)

### Использование интерфейса командной строки Azure для создания новой виртуальной машины CoreOS
<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

1. Установите [интерфейс командной строки Azure (CLI Azure)], если он еще не установлен, и либо выполните вход с помощью рабочего или учебного идентификатора, либо скачайте PUBLISHSETTINGS-файл и импортируйте его в свою учетную запись.
2. Найдите образ CoreOS. Чтобы в любой момент найти доступные образы, введите `azure vm image list | grep CoreOS`, и вы увидите список результатов примерно такого вида:

	data: 2b171e93f07c4903bcad35bda10acf22\_\_CoreOS-Stable-522.6.0 Public Linux

3. Создайте облачную службу для базового кластера, введя `azure service create <cloud-service-name>`, где <*cloud-service-name*> — это имя для облачной службы CoreOS. В этом примере используется имя **`coreos-cluster`**. Необходимо будет повторно использовать имя, которое вы выбрали, для создания экземпляров виртуальной машины CoreOS в облачной службе.

	Примечание. Если вы посмотрите на текущее состояние вашей работы на [портале Azure](https://portal.azure.com), то увидите, что имя вашей облачной службы является одновременно группой ресурсов и доменом, как показано на следующем рисунке:

	![][CloudServiceInNewPortal]

4. Подключите свою облачную службу и создайте новую виртуальную машину CoreOS внутри с помощью команды **azure vm create**. Вы передадите расположение сертификата X.509 в параметре **--ssh-cert**. Создайте первый образ виртуальной машины. Для этого введите следующее и замените **coreos-cluster** именем созданной облачной службы:

	```
azure vm create --custom-data=cloud-config.yaml --ssh=22 --ssh-cert=./myCert.pem --no-ssh-password --vm-name=node-1 --connect=coreos-cluster --location="West US" 2b171e93f07c4903bcad35bda10acf22__CoreOS-Stable-522.6.0 core
```

5. Создайте второй узел, повторив команду из шага 4. Замените значение **--vm-name** на **node-2**, а значение порта **--ssh** на 2022.

6. Создайте третий узел, повторив команду из шага 4. Замените значение **--vm-name** на **node-3**, а значение порта **--ssh** на 3022.

На снимке экрана ниже видно, как кластер CoreOS отображается на портале.

![][EmptyCoreOSCluster]

### Тестирование кластера CoreOS из виртуальной машины Azure.

Чтобы протестировать кластер, перейдите в ваш рабочий каталог и подключитесь к **node-1** с помощью **ssh**, передав закрытый ключ. Для этого введите следующее:

	ssh core@coreos-cluster.cloudapp.net -p 22 -i ./myPrivateKey.key

После подключения введите `sudo fleetctl list-machines`, чтобы узнать, определил ли уже кластер все виртуальные машины, входящие в него. Вы должны получить ответ, аналогичный показанному ниже:


	core@node-1 ~ $ sudo fleetctl list-machines
	MACHINE		IP		METADATA
	442e6cfb...	100.71.168.115	-
	a05e2d7c...	100.71.168.87	-
	f7de6717...	100.71.188.96	-


### Протестируйте кластер CoreOS из локального узла

Наконец, давайте протестируем кластер CoreOS из локального клиента Linux. Вы можете установить **fleetctl** с помощью **npm** или установить **fleetctl** и самостоятельно выполнить сборку **fleetctl** в локальном клиенте. Для **fleetctl** требуется **golang**,поэтому этот компонент необходимо установить первым, введя:

`sudo apt-get install golang`

Клонируйте репозиторий **fleet** из github. Для этого введите следующее:

`git clone https://github.com/coreos/fleet.git`

Выполните сборку **fleetctl**, заменив каталог и тип `fleet`.

`./build`

Разместите **fleet** для легкости использования (в зависимости от конфигурации, вам может потребоваться **sudo**):

`cp bin/fleetctl /usr/local/bin`

Убедитесь, что у **fleetctl** есть доступ к `myPrivateKey.key` в рабочем каталоге, введя:

`ssh-add ./myPrivateKey.key`

> [AZURE.NOTE] Если вы уже используете ключ `~/.ssh/id_rsa`, то добавьте его посредством `ssh-add ~/.ssh/id_rsa`.

Теперь вы готовы к удаленному тестированию с помощью той же команды **fleetctl**, которую использовали из узла **node-1**, но с передачей удаленных аргументов:

`fleetctl --tunnel coreos-cluster.cloudapp.net:22 list-machines`

Результаты должны быть следующими:


	MACHINE		IP		METADATA
	442e6cfb...	100.71.168.115	-
	a05e2d7c...	100.71.168.87	-
	f7de6717...	100.71.188.96	-

## Дальнейшие действия

Теперь у вас должен быть работающий трехузловой кластер CoreOS в Azure. Далее вы можете узнать, как создавать более сложные кластеры и использовать Docker, а также создавать более интересные приложения. Чтобы ознакомиться с несколькими краткими примерами, см. раздел [Начало работы с Fleet на CoreOS в Azure].

<!--Anchors-->
[CoreOS, Clusters, and Linux Containers]: #intro
[Security Considerations]: #security
[How to use CoreOS on Azure]: #usingcoreos
[Subheading 3]: #subheading-3
[Next steps]: #next-steps

<!--Image references-->
[CloudServiceInNewPortal]: ./media/virtual-machines-linux-coreos-how-to/cloudservicefromnewportal.png
[EmptyCoreOSCluster]: ./media/virtual-machines-linux-coreos-how-to/endresultemptycluster.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[интерфейс командной строки Azure (CLI Azure)]: ../xplat-cli-install.md
[интерфейса командной строки Azure (Azure CLI)]: ../xplat-cli-install.md
[CoreOS]: https://coreos.com/
[Обзор CoreOS]: https://coreos.com/using-coreos/
[CoreOS в Azure]: https://coreos.com/docs/running-coreos/cloud-providers/azure/
[учебник Тима Парка (Tim Park) по CoreOS]: https://github.com/timfpark/coreos-azure
[учебник Патрика Чейзона (Patrick Chanezon) по CoreOS]: https://github.com/chanezon/azure-linux/tree/master/coreos/cloud-init
[Docker]: http://docker.io
[YAML]: http://yaml.org/
[Начало работы с Fleet на CoreOS в Azure]: virtual-machines-linux-coreos-fleet-get-started.md

<!---HONumber=AcomDC_0204_2016-->