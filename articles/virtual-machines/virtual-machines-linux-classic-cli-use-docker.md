<properties
	pageTitle="Использование расширения виртуальных машин Docker для Linux на Azure"
	description="Описание механизма Docker и расширений Виртуальных машин Azure, а также программного создания виртуальных машин в Azure как узлов Docker с помощью интерфейса CLI Azure."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="01/04/2016"
	ms.author="rasquill"/>

# Использование расширения виртуальных машин Docker в интерфейсе командной строки Azure (CLI Azure)

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Модель диспетчера ресурсов.



В этом разделе показано, как создать виртуальную машину с расширением виртуальных машин Docker в режиме управления службами (asm) в CLI Azure на любой платформе. [Docker](https://www.docker.com/) — один из самых популярных подходов к виртуализации, использующий [контейнеры Linux](http://en.wikipedia.org/wiki/LXC) вместо виртуальных машин как способ изоляции данных и вычислений при использовании общих ресурсов. Можно использовать расширение Docker в [агенте Linux для Azure](virtual-machines-linux-agent-user-guide.md) для создания виртуальной машины, в которой будет размещено любое количество контейнеров с приложениями Azure. Обзорное обсуждение контейнеров и их преимуществ см. на [доске по Docker](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard).


##Использование расширения виртуальных машин Docker в Azure
Чтобы использовать расширения виртуальных машин Docker в Azure, необходимо установить версию [интерфейса командной строки Azure](https://github.com/Azure/azure-sdk-tools-xplat) (CLI Azure) более позднюю, чем 0.8.6 (на момент написания статьи текущей версией является 0.8.10). Вы можете установить CLI Azure на компьютерах под управлением Mac, Linux и Windows.


Процесс использования Docker в Azure прост.

+ Установите CLI Azure и его зависимости на компьютере, с которого вы хотите управлять Azure (в случае Windows это будет дистрибутив Linux, запущенный в виде виртуальной машины).
+ Используйте команды Docker в CLI Azure, чтобы создать узел виртуальных машин Docker в Azure.
+ Используйте команды локального Docker для управления своим контейнерами на узле виртуальных машин Docker в Azure.


### Установка интерфейса командной строки Azure (Azure CLI)

Сведения об установке и настройке Azure CLI см. в статье [Установка интерфейса командной строки Azure](../xplat-cli-install.md). Чтобы подтвердить установку, введите `azure` в командной строке, и через некоторое время отобразится рисунок ASCII интерфейса командной строки Azure, в котором перечислены основные доступные вам команды. Если установка прошла без ошибок, вы можете ввести `azure help vm` и увидеть в списке команд docker.

> [AZURE.NOTE] В Docker имеется [Boot2Docker](https://docs.docker.com/installation/windows/) — программа установки для Windows, позволяющая автоматизировать создание клиента Docker, который вы можете использовать для работы с виртуальными машинами Azure в качестве узлов Docker.

### Подключение CLI Azure к учетной записи Azure
Чтобы вы смогли использовать CLI Azure, свяжите учетные данные учетной записи Azure с CLI Azure для вашей платформы. В разделе [Подключение к подписке Azure](../xplat-cli-connect.md) описано, как скачать и импортировать **PUBLISHSETTINGS**-файл либо связать интерфейс CLI Azure с идентификатором организации.

> [AZURE.NOTE] Есть некоторые различия в действиях при использовании первого или второго способа аутентификации, так что не забудьте прочитать вышеуказанный документ, чтобы понять различие в функциональности.

### Установка Docker и использование расширения виртуальных машин Docker для Azure
Следуйте [инструкции по установке Docker](https://docs.docker.com/installation/#installation) для того, чтобы установить Docker локально на вашем компьютере.

Для использования Docker на виртуальной машине Azure в образе Linux, который используется для нее, необходимо установить [Агент виртуальных машин Linux для Azure](virtual-machines-linux-agent-user-guide.md). В настоящее время это обеспечивается только двумя типами образов:

+ образ Ubuntu из коллекции образов Azure или

+ пользовательский образ Linux, созданный вами, с установленным и настроенным агентом виртуальных машин Linux для Azure. Дополнительные сведения о создании собственной виртуальной машины с агентом Azure см. в разделе [Агент виртуальных машин Linux для Azure](virtual-machines-linux-agent-user-guide.md).

### Использование коллекции образов Azure

В сеансе Bash или сеансе терминала используйте команду

`azure vm image list | grep Ubuntu-14_04`

в интерфейсе командной строки Azure для поиска последнего образа Ubuntu в коллекции виртуальных машин, выберите одно из имен образов, например `b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04-LTS-amd64-server-20140724-ru-RU-30GB`, и используйте указанную ниже команду для создания виртуальной машины с помощью этого образа.

```
azure vm docker create -e 22 -l "West US" <vm-cloudservice name> "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04-LTS-amd64-server-20140724-ru-RU-30GB" <username> <password>
```

Описание:

+ *&lt;vm-cloudservice name&gt;* — имя виртуальной машины, которая станет главным компьютером для контейнеров Docker в Azure;

+  *&lt;username&gt;* — имя привилегированного пользователя по умолчанию для виртуальной машины;

+ *&lt;password&gt;* — пароль для *имени пользователя* учетной записи, который отвечает требованиям к сложности, предъявляемым для Azure.

> [AZURE.NOTE] В настоящий момент пароль должен включать не менее 8 символов, содержать как минимум один символ в нижнем и в верхнем регистре, цифру и один из следующих специальных символов: `!@#$%^&+=`. Точка в конце предыдущего выражения НЕ является специальным символом.

Если команда прошла успешно, вы увидите нечто вроде следующего, в зависимости от конкретных аргументов и параметров, используемых вами:

![](./media/virtual-machines-linux-classic-cli-use-docker/dockercreateresults.png)

> [AZURE.NOTE] Создание виртуальной машины может занять несколько минут, но после ее подготовки (значение состояния — `ReadyRole`) будет запущена управляющая программа Docker (служба Docker), и вы сможете подключиться к узлу контейнера Docker.

Для проверки виртуальной машины Docker, только что созданной в Azure, введите

`docker --tls -H tcp://<vm-name-you-used>.cloudapp.net:2376 info`

Где *&lt;vm-name-you-used&gt;* — имя виртуальной машины, которое было использовано при вызове к `azure vm docker create`. Вы должны увидеть вывод, похожий на пример ниже, который указывает, что виртуальная машина узла Docker в Azure работает и ожидает ваших команд.

Теперь можно попытаться подключиться с помощью клиента Docker, чтобы получить информацию (в некоторых экземплярах клиента Docker, например в ОС Mac, может потребоваться использовать `sudo`):

	sudo docker --tls -H tcp://testsshasm.cloudapp.net:2376 info
	Password:
	Containers: 0
	Images: 0
	Storage Driver: devicemapper
	Pool Name: docker-8:1-131781-pool
	Pool Blocksize: 65.54 kB
	Backing Filesystem: extfs
	Data file: /dev/loop0
	Metadata file: /dev/loop1
	Data Space Used: 1.821 GB
	Data Space Total: 107.4 GB
	Data Space Available: 28 GB
	Metadata Space Used: 1.479 MB
	Metadata Space Total: 2.147 GB
	Metadata Space Available: 2.146 GB
	Udev Sync Supported: true
	Deferred Removal Enabled: false
	Data loop file: /var/lib/docker/devicemapper/devicemapper/data
	Metadata loop file: /var/lib/docker/devicemapper/devicemapper/metadata
	Library Version: 1.02.77 (2012-10-15)
	Execution Driver: native-0.2
	Logging Driver: json-file
	Kernel Version: 3.19.0-28-generic
	Operating System: Ubuntu 14.04.3 LTS
	CPUs: 1
	Total Memory: 1.637 GiB
	Name: testsshasm
	WARNING: No swap limit support

Чтобы убедиться, что все работает, можно проверить виртуальную машину для расширения Docker:

	azure vm extension get testsshasm
	info: Executing command vm extension get
	+ Getting virtual machines
	data: Publisher Extension name ReferenceName Version State
	data: -------------------- --------------- ------------------------- ------- ------
	data: Microsoft.Azure.E... DockerExtension DockerExtension 1.* Enable
	info: vm extension get command OK

### Проверка подлинности виртуальной машины узла Docker

Помимо создания виртуальной машины Docker, команда `azure vm docker create` также автоматически создает необходимые сертификаты, чтобы разрешить подключение клиентского компьютера Docker к узлу контейнера Azure с помощью HTTPS. Сертификаты хранятся как на клиентских компьютерах, так и на хост-компьютерах, в зависимости от обстоятельств. При последующих попытках существующие сертификаты используются повторно и совместно с новым узлом.

По умолчанию сертификаты помещаются в `~/.docker`, а Docker настраивается для запуска с использованием порта **2376**. Если вы хотите использовать другой порт или каталог, то вы можете использовать один из следующих параметров командной строки `azure vm docker create` для настройки виртуальной машины, в которой размещены контейнеры Docker, на использование другого порта или других сертификатов для подключения клиентов:

```
-dp, --docker-port [port]              Port to use for docker [2376]
-dc, --docker-cert-dir [dir]           Directory containing docker certs [.docker/]
```

Управляющая программа Docker на узле настроена на прослушивание и проверку подлинности клиентских подключений на указанном порту с использованием сертификатов, созданных с помощью команды `azure vm docker create`. Клиентские компьютеры должны иметь эти сертификаты, чтобы получить доступ к узлу Docker.

> [AZURE.NOTE] Сетевой узел, работающий без этих сертификатов, будет уязвим для всех, кто может подключиться к компьютеру. Перед тем как изменить конфигурацию по умолчанию, убедитесь, что вы понимаете все риски, которым могут быть подвержены компьютеры и приложения.

## Дальнейшие действия

Переходите к [Руководству пользователя Docker] и использованию виртуальной машины Docker. Для создания виртуальной машины с поддержкой Docker на новом портале см. раздел [Использование расширения виртуальных машин Docker на портале].

<!--Anchors-->
[Subheading 1]: #subheading-1
[Subheading 2]: #subheading-2
[Subheading 3]: #subheading-3
[Next steps]: #next-steps

[How to use the Docker VM Extension with Azure]: #How-to-use-the-Docker-VM-Extension-with-Azure
[Virtual Machine Extensions for Linux and Windows]: #Virtual-Machine-Extensions-For-Linux-and-Windows
[Container and Container Management Resources for Azure]: #Container-and-Container-Management-Resources-for-Azure



<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines-windows-hero-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: ../web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: ../storage-whatis-account.md
[Использование расширения виртуальных машин Docker на портале]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-portal/

[Руководству пользователя Docker]: https://docs.docker.com/userguide/
 

<!---HONumber=AcomDC_0323_2016-->