<properties title="How to Use CoreOS on Azure" pageTitle="Как использовать CoreOS в Azure" description="Описывается ОС CoreOS, порядок создания виртуальной машины CoreOS в Azure и ее основное использование." metaKeywords="linux, virtual machines, vm, azure, CoreOS, linux containers,  lxc, virtualization" services="virtual-machines" solutions="dev-test" documentationCenter="virtual-machines" authors="rasquill" videoId="" scriptId="" manager="timlt" />

<tags ms.service="virtual-machines" ms.devlang="multiple" ms.topic="article" ms.tgt_pltfrm="vm-linux" ms.workload="infrastructure-services" ms.date="10/27/2014" ms.author="rasquill" />

<!--This is a basic template that shows you how to use mark down to create a topic that includes a TOC, sections with subheadings, links to other azure.microsoft.com topics, links to other sites, bold text, italic text, numbered and bulleted lists, code snippets, and images. For fancier markdown, find a published topic and copy the markdown or HTML you want. For more details about using markdown, see http://sharepoint/sites/azurecontentguidance/wiki/Pages/Content%20Guidance%20Wiki%20Home.aspx.-->

<!--Properties section (above): this is required in all topics. Please fill it out! See http://sharepoint/sites/azurecontentguidance/wiki/Pages/Markdown%20tagging%20-%20add%20a%20properties%20section%20to%20your%20markdown%20file%20to%20specify%20metadata%20values.aspx for details. -->

<!-- Tags section (above): this is required in all topics. Please fill it out! See http://sharepoint/sites/azurecontentguidance/wiki/Pages/Markdown%20tagging%20-%20add%20a%20tags%20section%20to%20your%20markdown%20file%20to%20specify%20metadata%20for%20reporting.aspx for details. -->

<!--The next line, with one pound sign at the beginning, is the page title--> 
# Использование CoreOS в Azure

В этом разделе описывается ОС [CoreOS], а также создание кластера из трех виртуальных машин CoreOS в Azure для быстрого ознакомления с CoreOS. Используются основные элементы развертываний CoreOS и примеры из статей [CoreOS в Azure], [учебник Тима Парка (Tim Park) по CoreOS] и [учебник Патрика Чейзона (Patrick Chanezon) по CoreOS] для демонстрации минимальных требований для понимания базовой структуры развертывания CoreOS и успешного создания кластера из трех виртуальных машин. 

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->
В этой статье содержатся следующие разделы:

+ [CoreOS, кластеры и контейнеры Linux]
+ [Вопросы безопасности]
+ [Использование CoreOS в Azure]
+ [Дальнейшие действия]


## <a id='intro'>CoreOS, кластеры и контейнеры Linux</a>

CoreOS - это облегченная версия Linux, разработанная для поддержки быстрого создания потенциально очень крупных кластеров виртуальных машин, в которых в качестве единственного механизма упаковки используются контейнеры Linux, включая контейнеры [Docker]. CoreOS предназначена для поддержки:

+ очень высокого уровня автоматизации;
+ более удобного и согласованного развертывания приложений;
+ масштабируемости на уровне приложений и системы.

В общих чертах, эти цели поддерживаются следующими возможностями CoreOS:

1. Система, состоящая из одного пакета: В CoreOS выполняются только образы контейнеров Linux, которые работают в контейнерах Linux. Это обеспечивает быстродействие, единообразие и удобство развертывания.
2. Обновления операционной системы выполняются автоматически. Поэтому операционные системы обновляются как одна сущность, и можно с легкостью выполнить откат к предыдущему известному состоянию.
3. Встроенные управляющие программы (службы) [etcd](https://github.com/coreos/etcd) и [fleet](https://github.com/coreos/fleet) для динамического подключения виртуальных машин и кластеров и управления ими

Это самое общее описание ОС CoreOS и ее возможностей. Более подробную информацию об ОС CoreOS см. в статье [Обзор CoreOS].

## <a id='security'>Вопросы безопасности</a>
В настоящее время в CoreOS предполагается, что пользователи с правами подключения к кластеру по SSH имеют права на управление им. В результате кластеры CoreOS отлично подходят для сред тестирования и разработки без каких-либо изменений, однако в производственной среде необходимо внедрить дополнительные меры безопасности. 

## <a id='usingcoreos'>Использование CoreOS в Azure</a>

В этом разделе описывается создание облачной службы Azure с тремя виртуальными машинами CoreOS в ней с помощью [кроссплатформенного интерфейса Azure (xplat-cli)]. Основные этапы

1. Создайте сертификаты и ключи SSH для безопасного взаимодействия с виртуальной машиной CoreOS
2. Получите идентификатор etcd кластера для внутренних взаимодействий
3. Создайте файл cloud-config в формате [YAML]
4. Используйте интерфейс xplat-cli для создания новой облачной службы Azure и трех виртуальных машин CoreOS
5. Протестируйте кластер CoreOS из виртуальной машины Azure
6. Протестируйте кластер CoreOS из локального узла 

### Создание открытых и закрытых ключей для взаимодействия
 
Воспользуйтесь указаниями, приведенными в разделе [Использование SSH с Linux в Azure](http://azure.microsoft.com/ru-ru/documentation/articles/virtual-machines-linux-use-ssh-key/) , чтобы создать открытый и закрытый ключи для SSH. (Основные действия описаны ниже.) Вы будете использовать эти ключи для подключения к виртуальным машинам в кластере для проверки их работоспособности и взаимодействия друг с другом.

> [WACOM.NOTE] В этом разделе предполагается, что у вас нет этих ключей. Вы должны создать файлы **myPrivateKey.pem** и **myCert.pem** для ясности. Если у вас уже есть открытый и закрытый ключи, сохраненные в **`~/.ssh/id_rsa`**, вы можете просто ввести openssl req -x509 -key ~/.ssh/id_rsa -nodes -days 365 -newkey rsa:2048 -out myCert.pem, чтобы получить PEM-файл, который вам необходим для передачи в Azure.

1. В рабочем каталоге введите openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem, чтобы создать закрытый ключ и сертификат X.509, связанный с ним. 

2. Для подтверждения прав владельца закрытого ключа на чтение и запись в этот файл введите chmod 600 myPrivateKey.key. 

Теперь в вашем рабочем каталоге должны находиться файлы **myPrivateKey.key** и **myCert.pem**. 


### Получение идентификатора etcd кластера

Управляющей программе **etcd** в CoreOS необходим идентификатор обнаружения для автоматического запроса всех узлов в кластере. Чтобы получить идентификатор обнаружения и сохранить его в файле **etcdid**, введите

```
curl https://discovery.etcd.io/new | grep ^http.* > etcdid
```

### Создание файла cloud-config

В рабочем каталоге с помощью текстового редактора создайте файл со следующим текстом и сохраните его как **cloud-config.yaml**. (Вы можете сохранить файл под любым именем, однако во время создания виртуальных машин на следующем этапе вы должны будете указать это имя файла в параметре **--custom-data** команды **azure create vm**.)

> [WACOM.NOTE] Не забудьте ввести cat etcdid, чтобы извлечь идентификатор обнаружения etcd из файла etcdid, который вы создали ранее, и замените **<token>** в следующем файле **cloud-config.yaml** на сгенерированный номер из файла etcdid. Если в конце вы не сможете проверить работу вашего кластера, возможно, именно этот шаг вы упустили!

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

(Более подробную информацию о файле cloud-config см. в разделе [Использование Cloud-Config](https://coreos.com/docs/cluster-management/setup/cloudinit-cloud-config/) в документации по CoreOS.)

### Использование интерфейса xplat-cli для создания новой виртуальной машины CoreOS
<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

1. Установите [кроссплатформенный интерфейс Azure (xplat-cli)], если вы еще не сделали это, и либо войдите в систему, используя идентификатор организации или школы, либо загрузите PUBLISHSETTINGS-файл и импортируйте его в вашу учетную запись.
2. Найдите образ CoreOS. В настоящее время есть только один образ - **2b171e93f07c4903bcad35bda10acf22__CoreOS-Alpha-475.1.0**. Однако чтобы в любой момент найти доступные образы, введите: azure vm image list | grep .*CoreOS.* Вы увидите один или несколько результатов, например:
data:    2b171e93f07c4903bcad35bda10acf22__CoreOS-Alpha-475.1.0              Public    Linux
3. Создайте облачную службу для своего базового кластера, введя
azure service create <cloud-service-name>, где cloud-service-name - это имя вашей облачной службы CoreOS. В этом примере используется имя **coreos-cluster**; вам необходимо использовать имя, выбранное при создании экземпляров виртуальных машин CoreOS в облачной службе. 

Замечание. Если вы посмотрите на текущее состояние вашей работы в [новом портале](https://portal.azure.com), вы увидите, что имя вашей облачной службы является одновременно группой ресурсов и доменом, как показано на следующем изображении:

![][CloudServiceInNewPortal]  
4. Подключитесь к вашей облачной службе и создайте в ней новую виртуальную машину CoreOS с помощью команды **azure vm create**. Вы передадите расположение сертификата X.509 в параметре **--ssh-cert**. Создайте первый образ виртуальной машины. Для этого введите следующее и замените **coreos-cluster** именем созданной облачной службы:

```
azure vm create --custom-data=cloud-config.yaml --ssh=22 --ssh-cert=./myCert.pem --no-ssh-password --vm-name=node-1 --connect=coreos-cluster --location='West US' 2b171e93f07c4903bcad35bda10acf22__CoreOS-Alpha-475.1.0 core
```

5. Создайте второй узел, повторив команду из шага 4. Замените значение **--vm-name** на **node-2**, а значение порта **--ssh** на 2022. 
 
6. Создайте третий узел, повторив команду из шага 4. Замените значение **--vm-name** на **node-3**, а значение порта **--ssh** на 3022. 
 
На снимке экрана ниже показан вид кластера CoreOS на новом портале.

![][EmptyCoreOSCluster]

### Тестирование кластера CoreOS из виртуальной машины Azure

Чтобы протестировать кластер, перейдите в ваш рабочий каталог и подключитесь к **node-1** с помощью **ssh**, передав закрытый ключ. Для этого введите следующее:

`ssh core@coreos-cluster.cloudapp.net -p 22 -i ./myPrivateKey.key`

После подключения введите sudo fleetctl list-machines, чтобы определить, идентифицировал ли кластер все виртуальные машины в нем. Вы должны получить ответ, аналогичный показанному ниже:

```
core@node-1 ~ $ sudo fleetctl list-machines
MACHINE		IP		METADATA
442e6cfb...	100.71.168.115	-
a05e2d7c...	100.71.168.87	-
f7de6717...	100.71.188.96	-
```

### Тестирование кластера CoreOS из локального узла

Наконец, протестируйте свой кластер CoreOS из локального клиента Linux, установив **fleet**. Для **fleet** необходим **golang**, поэтому сначала может потребоваться установить его, введя:

`sudo apt-get install golang` 

Клонируйте репозиторий **fleet** из github. Для этого введите следующее:

`git clone https://github.com/coreos/fleet.git`

Создайте **fleet**, введя

`./build`

Разместите **fleet** для легкости использования (в зависимости от конфигурации, вам может потребоваться **sudo**):

`cp bin/fleetctl /usr/local/bin`

Убедитесь, что у **fleet** есть доступ к myPrivateKey.key в рабочем каталоге. Для этого введите следующее:

`ssh-add ./myPrivateKey.key`

> [WACOM.NOTE] Если вы уже используете ключ **~/.ss h/id_rsa**, добавьте его с помощью ssh-add ~/.ssh/id_rsa.

Теперь вы готовы к удаленному тестированию с помощью той же команды **fleetctl**, которую использовали из узла **node-1**, но с передачей удаленных аргументов:

`fleetctl --tunnel coreos-cluster.cloudapp.net:22 list-machines`

Результаты должны быть следующими:

```
MACHINE		IP		METADATA
442e6cfb...	100.71.168.115	-
a05e2d7c...	100.71.168.87	-
f7de6717...	100.71.188.96	-
```

## Дальнейшие действия

Теперь у вас должен быть работающий трехузловой кластер CoreOS в Azure. Далее вы можете узнать, как создавать более сложные кластеры и использовать Docker, а также создавать более интересные приложения. Для этого прочитайте [учебник Тима Парка (Tim Park) по CoreOS], [учебник Патрика Чейзона (Patrick Chanezon) по CoreOS], документацию по [Docker] и [Обзор CoreOS].

<!--Anchors-->
[CoreOS, кластеры и контейнеры Linux]: #intro
[Вопросы безопасности]: #security
[Как использовать CoreOS в Azure]: #usingcoreos
[Подзаголовок 3]: #subheading-3
[Дальнейшие действия]: #next-steps

<!--Image references-->
[CloudServiceInNewPortal]: ./media/virtual-machines-linux-coreos-how-to/cloudservicefromnewportal.png
[EmptyCoreOSCluster]: ./media/virtual-machines-linux-coreos-how-to/endresultemptycluster.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[Кроссплатформенный интерфейс Azure (xplat-cli)]: ../xplat-cli/

[CoreOS]: https://coreos.com/
[Обзор CoreOS]: https://coreos.com/using-coreos/
[CoreOS в Azure]: https://coreos.com/docs/running-coreos/cloud-providers/azure/
[Учебник Тима Парка (Tim Park) по CoreOS]: https://github.com/timfpark/coreos-azure
[Учебник Патрика Чейзона (Patrick Chanezon) по CoreOS]: https://github.com/chanezon/azure-linux/tree/master/coreos/cloud-init
[Docker]: http://docker.io
[YAML]: http://yaml.org/

<!--HONumber=35_1-->
