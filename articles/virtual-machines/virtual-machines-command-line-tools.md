<properties
	pageTitle="Управление службами с помощью Azure CLI | Microsoft Azure"
	description="Узнайте, как использовать программы командной строки для Mac, Linux и Windows, чтобы управлять Azure с помощью Azure CLI в классическом режиме развертывания (с использованием управления службами)."
	services="virtual-machines, mobile-services, cloud-services"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/30/2015"
	ms.author="danlep"/>

# Управление службами Azure с помощью интерфейса командной строки Azure на компьютерах Mac, а также на компьютерах с ОС Linux и Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]В этой статье описывается процесс создания ресурсов с помощью классической модели развертывания. Для создания ресурса вы также можете использовать [модель развертывания диспетчера ресурсов](virtual-machines-deploy-rmtemplates-azure-cli.md).

В этом разделе рассказывается, как с помощью интерфейса командной строки Azure в режиме **asm** создавать и удалять службы, а также управлять ими на компьютерах Mac и компьютерах с ОС Linux и Windows. Эти функциональные возможности аналогичны предоставляемым командлетами управления службами Windows PowerShell, которые устанавливаются вместе с пакетами SDK для Azure для .NET, Node.JS и PHP.

> [AZURE.NOTE]Принципы использования служб Azure в режиме **asm** аналогичны принципам работы с основными компонентами и службами Azure, например с веб-сайтами, виртуальными машинами, виртуальными сетями, службой хранилища и т. д. При использовании режима **asm** в командной строке доступно большее количество функций, а также логически сгруппированная иерархическая модель ресурсов. Сведения о том, как переключиться в этот режим, см. в разделе [Использование интерфейса командной строки Azure с диспетчером ресурсов](xplat-cli-azure-resource-manager.md).

Инструкции по установке см. в разделе[Установка и настройка интерфейса командной строки Azure](../xplat-cli-install.md).

Необязательные параметры заключены в квадратные скобки (например, [параметр]). Все остальные параметры являются обязательными.

Помимо описанных здесь необязательных параметров для конкретных команд существуют три необязательных параметра, которые можно использовать для отображения подробных выходных данных, например параметров запроса и кодов состояния. Параметр -v предоставляет подробные выходные данные, а параметр -vv — еще более подробные выходные данные. Параметр --json будет выводить результат в необработанном JSON-формате.

## Настройка режима **asm**

В настоящее время режим управления службами включается по умолчанию при установке CLI. Если необходимо, используйте следующую команду для включения команд управления службами CLI Azure.

	azure config mode asm

>[AZURE.NOTE]Режим диспетчера ресурсов Azure и режим управления службами Azure являются взаимоисключающими. То есть ресурсами, созданными в одном из режимов, нельзя управлять из другого режима.

## Управление информацией об учетной записи и параметрами публикации
Информация о подписке Azure используется инструментом для подключения к учетной записи. Эту информацию вы можете получить через портал Azure в файле параметров публикации, как описано здесь. Вы можете импортировать файл параметров публикации как постоянный локальный параметр конфигурации, который программа будет использовать для последующих операций. Вам необходимо импортировать параметры публикации только один раз.

**account download [параметры]**

Эта команда запускает браузер для скачивания PUBLISHSETTINGS-файла с портала Azure.

	~$ azure account download
	info:   Executing command account download
	info:   Launching browser to https://windows.azure.com/download/publishprofile.aspx
	help:   Save the downloaded file, then execute the command
	help:   account import <file>
	info:   account download command OK

**account import [параметры] &lt;файл>**


Эта команда импортирует PUBLISHSETTINGS-файл или сертификат, чтобы средство могло его использовать в дальнейшем.

	~$ azure account import publishsettings.publishsettings
	info:   Importing publish settings file publishsettings.publishsettings
	info:   Found subscription: 3-Month Free Trial
	info:   Found subscription: Pay-As-You-Go
	info:   Setting default subscription to: 3-Month Free Trial
	warn:   The 'publishsettings.publishsettings' file contains sensitive information.
	warn:   Remember to delete it now that it has been imported.
	info:   Account publish settings imported successfully

> [AZURE.NOTE]PUBLISHSETTINGS-файл может содержать данные (например, имя и идентификатор подписки) о нескольких подписках. При импорте PUBLISHSETTINGS-файла первая подписка используется в качестве описания по умолчанию. Чтобы использовать другую подписку, выполните следующую команду: <code>~$ azure config set subscription &lt;идентификатор\_другой\_подписки&gt;</code>

**account clear [параметры]**

Эта команда удаляет сохраненные параметры публикации, которые были импортированы. Используйте эту команду, если вы закончили работу со средством на этом компьютере и хотите быть уверены, что средство нельзя будет использовать с вашей учетной записью в дальнейшем.

	~$ azure account clear
	Clearing account info.
	info:   OK

**account list [параметры]**

Вывод списка импортированных подписок

	~$ azure account list
	info:    Executing command account list
	data:    Name                                    Id
	       Current
	data:    --------------------------------------  -------------------------------
	-----  -------
	data:    Forums Subscription                     8679c8be-3b05-49d9-b8fb  true
	data:    Evangelism Team Subscription            9e672699-1055-41ae-9c36  false
	data:    MSOpenTech-Prod                         c13e6a92-706e-4cf5-94b6  false

**account set [параметры] &lt;подписка&gt;**

Задание текущей подписки

###Команды для управления территориальными группами

**account affinity-group list [параметры]**

Эта команда выводит список ваших территориальных групп Azure.

Территориальные группы можно задать, если группа виртуальных машин охватывает несколько физических компьютеров. Территориальная группа указывает, что физические компьютеры должны находиться как можно ближе друг к другу, чтобы уменьшить сетевую задержку.

	~$ azure account affinity-group list
	+ Fetching affinity groups
	data:   Name                                  Label   Location
	data:   ------------------------------------  ------  --------
	data:   535EBAED-BF8B-4B18-A2E9-8755FB9D733F  opentec  West US
	info:   account affinity-group list command OK

**account affinity-group create [параметры] &lt;имя&gt;**

Эта команда создает новую территориальную группу.

	~$ azure account affinity-group create opentec -l "West US"
	info:    Executing command account affinity-group create
	+ Creating affinity group
	info:    account affinity-group create command OK

**account affinity-group show [параметры] &lt;имя&gt;**

Эта команда отображает сведения о территориальной группе.

	~$ azure account affinity-group show opentec
	info:    Executing command account affinity-group show
	+ Getting affinity groups
	data:    $ xmlns "http://schemas.microsoft.com/windowsazure"
	data:    $ xmlns:i "http://www.w3.org/2001/XMLSchema-instance"
	data:    Name "opentec"
	data:    Label "b3BlbnRlYw=="
	data:    Description $ i:nil "true"
	data:    Location "West US"
	data:    HostedServices ""
	data:    StorageServices ""
	data:    Capabilities Capability 0 "PersistentVMRole"
	data:    Capabilities Capability 1 "HighMemory"
	info:    account affinity-group show command OK

**account affinity-group delete [параметры] &lt;имя&gt;**

Эта команда удаляет указанную территориальную группу.

	~$ azure account affinity-group delete opentec
	info:    Executing command account affinity-group delete
	Delete affinity group opentec? [y/n] y
	+ Deleting affinity group
	info:    account affinity-group delete command OK

###Команды для управления средой учетной записи

**account env list [параметры]**

Выводит список сред учетной записи.

	C:\windows\system32>azure account env list
	info:    Executing command account env list
	data:    Name
	data:    ---------------
	data:    AzureCloud
	data:    AzureChinaCloud
	info:    account env list command OK

**account env show [параметры] [среда]**

Отображает сведения о среде учетной записи

	~$ azure account env show
	info:    Executing command account env show
	Environment name: AzureCloud
	data:    Environment publishingProfile  http://go.microsoft.com/fwlink/?LinkId=2544
	data:    Environment portal  http://go.microsoft.com/fwlink/?LinkId=2544
	info:    account env show command OK

**account env add [параметры] [среда]**

Эта команда добавляет среду в учетную запись.

**account env set [параметры] [среда]**

Эта команда задает среду учетной записи.

**account env delete [параметры] [среда]**

Эта команда удаляет указанную среду из учетной записи.

## Команды для управления виртуальными машинами Azure
На следующей схеме показано, как виртуальные машины Azure размещаются в рабочей среде развертывания облачных служб Azure.

![Техническая схема Azure](./media/virtual-machines-command-line-tools/architecturediagram.jpg)

**create-new** создает диск в хранилище BLOB-объектов (e:\\ на схеме); **attach** присоединяет уже созданный, но еще неприсоединенный диск к виртуальной машине.

**vm create [параметры] &lt;DNS-имя> &lt;образ> &lt;имя\_пользователя> [пароль]**

Эта команда создает новую виртуальную машину Azure. По умолчанию каждая виртуальная машина создается в собственной облачной службе, однако с помощью параметра -c вы можете указать, что виртуальную машину следует добавить в существующую облачную службу, как описано здесь.

Команда vm create, как и портал Azure, создает виртуальные машины в рабочей среде развертывания. Нет возможности создавать виртуальную машину в промежуточной среде развертывания облачной службы. Если для вашей подписки нет существующей учетной записи хранения Azure, эта команда создаст ее.

Вы можете указать расположение с помощью параметра --location или территориальную группу с помощью параметра --affinity-group. Если ни один из этих параметров не указан, вам будет предложено выбрать значение из списка допустимых расположений.

Введенный пароль должен содержать от 8 до 123 символов и удовлетворять требованиям к сложности пароля, которые предъявляет операционная система, используемая для виртуальной машины.

Если предполагается использовать SSH для управления развернутой виртуальной машиной Linux (как это обычно бывает), необходимо при создании виртуальной машины включить SSH с помощью параметра -e. После создания виртуальной машины включить SSH невозможно.

Для виртуальных машин Windows можно включить RDP позже, добавив порт 3389 в качестве конечной точки.

Для этой команды поддерживаются следующие необязательные параметры:

**-c, --connect** создает виртуальную машину в уже созданном развертывании в службе размещения. Если с этим параметром не используется параметр -vmname, то имя новой виртуальной машины будет создано автоматически.<br /> **-n, --vm-name** Задание имени виртуальной машины. Этот параметр по умолчанию берет имя службы размещения. Если параметр -vmname не указан, имя для новой виртуальной машины будет создано в следующем формате: &lt;имя\_службы>&lt;идентификатор>, где &lt;идентификатор> — это число, равное увеличенному на 1 количеству имеющихся в службе виртуальных машин. Например, если использовать эту команду для добавления новой виртуальной машины в службу размещения MyService, в которой имеется одна виртуальная машина, то новая виртуальная машина получит имя MyService2.<br /> **-u, --blob-url** Задание URL-адреса целевого хранилища больших двоичных объектов, в котором необходимо создать системный диск виртуальной машины. <br /> **-z, --vm-size** Задание размера виртуальной машины. Допустимые значения: ExtraSmall, Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9, A10, A11, Basic\_A0, Basic\_A1, Basic\_A2, Basic\_A3, Basic\_A4, Standard\_D1, Standard\_D2, Standard\_D3, Standard\_D4, Standard\_D11, Standard\_D12, Standard\_D13, Standard\_D14, Standard\_DS1, Standard\_DS2, Standard\_DS3, Standard\_DS4, Standard\_DS11, Standard\_DS12, Standard\_DS13, Standard\_DS14, Standard\_G1, Standard\_G2, Standard\_G3, Standard\_G4, Standard\_G55. По умолчанию используется значение Small. <br /> **-r** Добавление возможности подключения к виртуальной машине с ОС Windows с помощью протокола удаленного рабочего стола. <br /> **-e, --ssh** Добавление возможности подключения к виртуальной машине с ОС Windows с помощью протокола SSH. <br /> **-t, --ssh-cert** Задание сертификата SSH. <br /> **-s** Подписка <br /> **-o, --community** Указанный образ представляет собой образ сообщества <br /> **-w** Имя виртуальной сети <br/> **-l, --location** Задание расположения (например, "Север центральной части США"). <br /> **-a, --affinity-group** Задание территориальной группы.<br /> **-w, --virtual-network-name** Задание виртуальной сети, в которой необходимо добавить виртуальную машину. Можно настраивать виртуальные сети и управлять ими на портале Azure.<br /> **-b, --subnet-names** Задание имен подсетей для назначения виртуальной машине.

В этом примере MSFT\_\_Win2K8R2SP1-120514-1520-141205-01-ru-ru-30GB — образ, предоставленный платформой. Дополнительные сведения об образах операционных систем см. в описании команды vm image list.

	~$ azure vm create my-vm-name MSFT__Windows-Server-2008-R2-SP1.11-29-2011 username --location "West US" -r
	info:   Executing command vm create
	Enter VM 'my-vm-name' password: ************
	info:   vm create command OK

**vm create-from &lt;имя\_DNS> &lt;файл\_ролей>**

Эта команда создает новую виртуальную машину Azure из JSON-файла ролей.

	~$ azure vm create-from my-vm example.json
	info:   OK

**vm list [параметры]**

Эта команда выводит список виртуальных машин Azure. Параметр --json указывает, что результаты возвращаются в необработанном формате JSON.

	~$ azure vm list
	info:   Executing command vm list
	data:   DNS Name                          VM Name      Status
	data:   --------------------------------  -----------  ---------
	data:   my-vm-name.cloudapp-preview.net        my-vm        ReadyRole
	info:   vm list command OK

**vm location list [параметры]**

Эта команда выводит список всех доступных расположений учетной записи Azure.

	~$ azure vm location list
	info:   Executing command vm location list
	data:   Name                   Display Name
	data:   ---------------------  ------------
	data:   Azure Preview  West US
	info:   account location list command OK

**vm show [параметры] &lt;имя>**

Эта команда отображает информацию о виртуальной машине Azure. Параметр --json указывает, что результаты возвращаются в необработанном формате JSON.

	~$ azure vm show my-vm
	info:   Executing command vm show
	data:   {
	data:       InstanceSize: 'Small',
	data:       InstanceStatus: 'ReadyRole',
	data:       DataDisks: [],
	data:       IPAddress: '10.26.192.206',
	data:       DNSName: 'my-vm.cloudapp.net',
	data:       InstanceStateDetails: {},
	data:       VMName: 'my-vm',
	data:       Network: {
	data:           Endpoints: [
	data:               {
	data:                   Protocol: 'tcp',
	data:                   Vip: '65.52.250.250',
	data:                   Port: '63238' ,
	data:                   LocalPort: '3389',
	data:                   Name: 'RemoteDesktop'
	data:               }
	data:           ]
	data:       },
	data:       Image: 'MSFT__Windows-Server-2008-R2-SP1.11-29-2011',
	data:       OSVersion: 'WA-GUEST-OS-1.18_201203-01'
	data:   }
	info:   vm show command OK

**vm delete [параметры] &lt;имя>**

Эта команда удаляет виртуальную машину Azure. По умолчанию эта команда не удаляет большой двоичный объект Azure, из которого созданы диск операционной системы и диск с данными. Для удаления BLOB-объекта вместе с соответствующей виртуальной машиной используйте параметр -b.

	~$ azure vm delete my-vm
	info:   Executing command vm delete
	info:   vm delete command OK

**vm start [параметры] &lt;имя>**

Эта команда запускает виртуальную машину Azure.

	~$ azure vm start my-vm
	info:   Executing command vm start
	info:   vm start command OK

**vm restart [параметры] &lt;имя>**

Эта команда перезагружает виртуальную машину Azure.

	~$ azure vm restart my-vm
	info:   Executing command vm restart
	info:   vm restart command OK

**vm shutdown [параметры] &lt;имя>**

Эта команда завершает работу виртуальной машины Azure. С помощью необязательного параметра -p можно указать, что ресурс среды выполнения приложений не следует освобождать при завершении работы.

```
~$ azure vm shutdown my-vm
info:   Executing command vm shutdown
info:   vm shutdown command OK  
```

**vm capture &lt;имя\_виртуальной\_машины> &lt;имя\_целевого\_образа>**

Эта команда записывает образ виртуальной машины Azure.

Образ виртуальной машины можно создавать только тогда, когда она находится в состоянии **Остановлена**. Завершите работу виртуальной машины, прежде чем продолжить.

	~$ azure.cmd vm capture my-vm mycaptureimagename --delete
	info:   Executing command vm capture
	+ Fetching VMs
	+ Capturing VM
	info:   vm capture command OK

**vm export [параметры] &lt;имя\_виртуальной\_машины> &lt;путь\_к\_файлу>**

Эта команда экспортирует образ виртуальной машины Azure в файл.

	~$ azure vm export "myvm" "C:"
	info:    Executing command vm export
	+ Getting virtual machines
	+ Exporting the VM
	info:   vm export command OK

##  Команды для управления конечными точками виртуальных машин Azure
На следующей схеме показана архитектура типичного развертывания нескольких экземпляров виртуальной машины. Обратите внимание, что в этом примере на каждой виртуальной машине открыт порт 3389 (для доступа по RDP), а также имеется внутренний IP-адрес (например, 168.55.11.1), который используется средством балансировки нагрузки для маршрутизации трафика на виртуальную машину. Этот внутренний IP-адрес также может использоваться для обмена данными между виртуальными машинами.

![azurenetworkdiagram](./media/virtual-machines-command-line-tools/networkdiagram.jpg)

Внешние запросы к виртуальным машинам проходят через подсистему балансировки нагрузки. Таким образом, в развертываниях с несколькими виртуальными машинами невозможно указывать запросы для конкретной виртуальной машины. Для развертываний с несколькими виртуальными машинами необходимо настроить сопоставление портов между виртуальными машинами (порт-виртуальной-машины) и средством балансировки нагрузки (порт-балансировщика-нагрузки).

**vm endpoint create &lt;имя\_виртуальной\_машины> &lt;порт\_балансировщика\_нагрузки> [порт\_виртуальной\_машины]**

Эта команда создает конечную точку виртуальной машины. Можно также с помощью параметра -u или --enable-direct-server-return указать, нужно ли включить отключенную по умолчанию службу Direct Server Return в этой конечной точке.

	~$ azure vm endpoint create my-vm 8888 8888
	azure vm endpoint create my-vm 8888 8888
	info:   Executing command vm endpoint create
	+ Fetching VM
	+ Reading network configuration
	+ Updating network configuration
	info:   vm endpoint create command OK

**vm endpoint create-multiple [параметры] &lt;имя\_виртуальной\_машины> &lt;порт\_балансировщика\_нагрузки>[:&lt;порт\_виртуальной\_машины>[:&lt;протокол>[:&lt;enable-direct-server-return>[:&lt;имя\_набора\_балансировщика\_нагрузки>[:&lt;протокол\_зонда>[:&lt;порт\_зонда>[:&lt;путь\_к\_зонду>[:&lt;внутреннее\_имя\_балансировщика\_нагрузки>]]]]]]]] {1-*}**

Создает несколько конечных точек виртуальной машины.

**vm endpoint delete [параметры] &lt;имя\_виртуальной\_машины> &lt;имя\_конечной\_точки>**

Эта команда удаляет конечную точку виртуальной машины.

	~$ azure vm endpoint delete my-vm http
	azure vm endpoint delete my-vm http
	info:   Executing command vm endpoint delete
	+ Fetching VM
	+ Reading network configuration
	+ Updating network configuration
	info:   vm endpoint delete command OK

**vm endpoint list &lt;имя\_виртуальной\_машины>**

Эта команда выводит список всех конечных точек виртуальной машины. Параметр --json указывает, что результаты возвращаются в необработанном формате JSON.

	~$ azure vm endpoint list my-linux-vm
	data:   Name  External Port  Local Port
	data:   ----  -------------  ----------
	data:   ssh   22             22

**vm endpoint update [параметры] &lt;имя\_виртуальной\_машины> &lt;имя\_конечной\_точки>**

Эта команда обновляет конечную точку виртуальной машины, применяя новые значения с помощью этих параметров.

    -n, --endpoint-name <name>          the new endpoint name
    -lo, --lb-port <port>                the new load balancer port
    -t, --vm-port <port>                the new local port
    -o, --endpoint-protocol <protocol>  the new transport layer protocol for port (tcp or udp)

**vm endpoint show [параметры] &lt;имя\_виртуальной\_машины>**

Эта команда отображает сведения о конечных точках на виртуальной машине.

	~$ azure vm endpoint show "mycouchvm"
	info:    Executing command vm endpoint show
	+ Getting virtual machines
	data:    Network Endpoints 0 LoadBalancedEndpointSetName "CouchDB_EP-5984"
	data:    Network Endpoints 0 LocalPort "5984"
	data:    Network Endpoints 0 Name "CouchDB_EP"
	data:    Network Endpoints 0 Port "5984"
	data:    Network Endpoints 0 Protocol "tcp"
	data:    Network Endpoints 0 Vip "168.61.9.97"
	data:    Network Endpoints 1 LoadBalancedEndpointSetName "CouchEP_2-2020"
	data:    Network Endpoints 1 LocalPort "2020"
	data:    Network Endpoints 1 Name "CouchEP_2"
	data:    Network Endpoints 1 Port "2020"
	data:    Network Endpoints 1 Protocol "tcp"
	data:    Network Endpoints 1 Vip "168.61.9.97"
	data:    Network Endpoints 2 LocalPort "3389"
	data:    Network Endpoints 2 Name "RemoteDesktop"
	data:    Network Endpoints 2 Port "3389"
	data:    Network Endpoints 2 Protocol "tcp"
	data:    Network Endpoints 2 Vip "168.61.9.97"
	info:    vm endpoint show command OK

## Команды для управления образами виртуальных машин Azure

Образы записываются с уже настроенных виртуальных машин, которые могут быть реплицированы надлежащим образом.

**vm image list [параметры]**

Эта команда возвращает список образов виртуальных машин. Существуют три типа образов: образы, созданные корпорацией Майкрософт и имеющие префикс "MSFT", образы, созданные третьими сторонами и имеющие в качестве префикса имя поставщика, и образы, которые создаете вы. Можно записать образ существующей виртуальной машины или создать образ из пользовательского VHD-файла, переданного в хранилище BLOB-объектов. Дополнительные сведения об использовании пользовательских VHD-файлов см. в описании команды vm image create. Параметр --json указывает, что результаты возвращаются в необработанном формате JSON.

	~$ azure vm image list
	data:   Name                                                                   Category   OS
	data:   ---------------------------------------------------------------------  ---------  -------
	data:   CANONICAL__Canonical-Ubuntu-12-04-20120519-2012-05-19-ru-ru-30GB.vhd   Canonical  Linux
	data:   MSFT__Windows-Server-2008-R2-SP1.11-29-2011                            Microsoft  Windows
	data:   MSFT__Windows-Server-2008-R2-SP1-with-SQL-Server-2012-Eval.11-29-2011  Microsoft  Windows
	data:   MSFT__Windows-Server-8-Beta.ru-ru.30GB.2012-03-22                      Microsoft  Windows
	data:   MSFT__Windows-Server-8-Beta.2-17-2012                                  Microsoft  Windows
	data:   MSFT__Windows-Server-2008-R2-SP1.ru-ru.30GB.2012-3-22                  Microsoft  Windows
	data:   OpenLogic__OpenLogic-CentOS-62-20120509-ru-ru-30GB.vhd                 OpenLogic  Linux
	data:   SUSE__SUSE-Linux-Enterprise-Server-11SP2-20120521-ru-ru-30GB.vhd       SUSE       Linux
	data:   SUSE__OpenSUSE64121-03192012-ru-ru-15GB.vhd                            SUSE       Linux
	data:   WIN2K8-R2-WINRM                                                        User       Windows
	info:   vm image list command OK

**vm image show [параметры] &lt;имя>**

Эта команда отображает информацию об образе виртуальной машины.

	~$ azure vm image show MSFT__Windows-Server-2008-R2-SP1.11-29-2011
	+ Fetching VM image
	info:   Executing command vm image show
	data:   {
	data:       Label: 'Windows Server 2008 R2 SP1, Nov 2011',
	data:       Name: 'MSFT__Windows-Server-2008-R2-SP1.11-29-2011',
	data:       Description: 'Microsoft Windows Server 2008 R2 SP1',
	data:       @: { xmlns: 'http://schemas.microsoft.com/windowsazure', xmlns:i: 'http://www.w3.org/2001/XMLSchema-instance' },
	data:       Category: 'Microsoft',
	data:       OS: 'Windows',
	data:       Eula: 'http://www.microsoft.com',
	data:       LogicalSizeInGB: '30'
	data:   }
	info:   vm image show command OK

**vm image delete [параметры] &lt;имя>**

Эта команда удаляет образ виртуальной машины.

	~$ azure vm image delete my-vm-image
	info:   Executing command vm image delete
	info:   VM image deleted: my-vm-image
	info:   vm image delete command OK

**vm image create &lt;имя> [путь\_к\_источнику]**

Эта команда создает образ виртуальной машины. Пользовательские VHD-файлы передаются в хранилище BLOB-объектов, а затем из них создается образ виртуальной машины. Используйте этот образ виртуальной машины для создания виртуальной машины. Такие параметры, как расположение и ОС, не являются обязательными.

В некоторых системах устанавливается ограничение для дескрипторов файлов в каждом процессе. Если это ограничение превышено, появляется ошибка ограничения дескрипторов файлов. Можно выполнить команду еще раз, используя параметр -p &lt;количество>, чтобы уменьшить максимальное количество параллельно выполняющихся отправок. Максимальное число параллельных передач по умолчанию: 96.

	~$ azure vm image create mytestimage ./Sample.vhd -o windows -l "West US"
	info:   Executing command vm image create
	+ Retrieving storage accounts
	info:   VHD size : 13 MB
	info:   Uploading 13312.5 KB
	Requested:100.0% Completed:100.0% Running: 105 Time:    8s Speed:  1721 KB/s
	info:   http://myaccount.blob.core.azure.com/vm-images/Sample.vhd is uploaded successfully
	info:   vm image create command OK

## Команды для управления дисками данных виртуальных машин Azure

Диски данных — это VHD-файлы в хранилище больших двоичных объектов, которые может использовать виртуальная машина. Дополнительную информацию о том, как диски данных развертываются в хранилище больших двоичных объектов, см. на приведенной выше технической схеме Azure.

Команды для присоединения дисков данных (azure vm disk attach и azure vm disk attach-new) назначают номер логического устройства (LUN) присоединяемому диску данных в соответствии с требованиями для протокола SCSI. Первому присоединенному к виртуальной машине диску данных назначается номер LUN 0, следующему — LUN 1 и т. д.

При отключении диска с данными с помощью команды azure vm disk detach используйте параметр &lt;LUN&gt;, чтобы указать, какой именно диск необходимо отключить.

> [AZURE>NOTE] Обратите внимание, что диски с данными всегда необходимо отключать в обратном порядке, начиная с LUN с наибольшим номером. На уровне Linux SCSI не поддерживается отсоединение дисков с меньшими номерами LUN, если все еще присоединены диски с большими номерами LUN. Например, не следует отсоединять LUN 0, если все еще присоединен LUN 1.

**vm disk show [параметры] &lt;имя>**

Эта команда отображает информацию о диске Azure.

	~$ azure vm disk show anucentos-anucentos-0-20120524070008
	info:   Executing command vm disk show
	data:   AttachedTo DeploymentName "mycentos"
	data:   AttachedTo HostedServiceName "myanucentos"
	data:   AttachedTo RoleName "myanucentos"
	data:   OS "Linux"
	data:   Location "Azure Preview"
	data:   LogicalDiskSizeInGB "30"
	data:   MediaLink "http://mystorageaccount.blob.core.azure-preview.com/vhd-store/mycentos-cb39b8223b01f95c.vhd"
	data:   Name "mycentos-mycentos-0-20120524070008"
	data:   SourceImageName "OpenLogic__OpenLogic-CentOS-62-20120509-ru-ru-30GB.vhd"
	info:   vm disk show command OK

**vm disk list [параметры] [имя\_виртуальной\_машины]**

Эта команда выводит список дисков Azure или дисков, подключенных к указанной виртуальной машине. Если в команде в качестве параметра указывается имя виртуальной машины, она возвращает все диски, присоединенные к ней. LUN 1 создан вместе с виртуальной машиной, а все остальные перечисленные диски присоединены отдельно.

	~$ azure vm disk list mycentos
	info:   Executing command vm disk list
	data:   Lun  Size(GB)  Blob-Name
	data:   ---  --------  --------------------------------
	data:   1    30        mycentos-cb39b8223b01f95c.vhd
	data:   2    10        mycentos-e3f0d717950bb78d.vhd
	info:   vm disk list command OK

Если в команде не указано в качестве параметра имя виртуальной машины, возвращаются все диски.

	~$ azure vm disk list
	data:   Name                                        OS
	data:   ------------------------------------------  -------
	data:   mycentos-mycentos-0-20120524070008          Linux
	data:   mycentos-mycentos-2-20120525055052
	data:   mywindows-winvm-20120522223119              Windows
	info:   vm disk list command OK

**vm disk delete [параметры] &lt;имя>**

Эта команда удаляет диск Azure из личного репозитория. Перед удалением диск необходимо отсоединить от виртуальной машины.

	~$ azure vm disk delete mycentos-mycentos-2-20120525055052
	info:   Executing command vm disk delete
	info:   Disk deleted: mycentos-mycentos-2-20120525055052
	info:   vm disk delete command OK

**vm disk create &lt;имя> [путь\_к\_источнику]**

Эта команда отправляет и регистрирует диск Azure. Необходимо указать параметр --blob-url, --location или --affinity-group. Если в этой команде используется параметр [путь-к-источнику], передается указанный VHD-файл и создается новый образ. Затем можно присоединить этот образ к виртуальной машине, используя команду vm disk attach.

В некоторых системах устанавливается ограничение для дескрипторов файлов в каждом процессе. Если это ограничение превышено, появляется ошибка ограничения дескрипторов файлов. Можно выполнить команду еще раз, используя параметр -p &lt;количество>, чтобы уменьшить максимальное количество параллельно выполняющихся отправок. Максимальное число параллельных передач по умолчанию: 96.

	~$ azure vm disk create my-data-disk ~/test.vhd --location "West US"
	info:   Executing command vm disk create
	info:   VHD size : 10 MB
	info:   Uploading 10240.5 KB
	Requested:100.0% Completed:100.0% Running:  81 Time:   11s Speed:   952 KB/s
	info:   http://account.blob.core.azure.com/disks/test.vhd is uploaded successfully
	info:   vm disk create command OK

**vm disk upload [параметры] &lt;путь\_к\_источнику> &lt;URL-адрес\_BLOB-объекта> &lt;ключ\_учетной\_записи\_хранения>**

Эта команда позволяет передать диск виртуальной машины.

	~$ azure vm disk upload "http://sourcestorage.blob.core.windows.net/vhds/sample.vhd" "http://destinationstorage.blob.core.windows.net/vhds/sample.vhd" "DESTINATIONSTORAGEACCOUNTKEY"
	info:   Executing command vm disk upload
	info:   Uploading 12351.5 KB
	info:   vm disk upload command OK

**vm disk attach &lt;имя\_виртуальной\_машины> &lt;имя\_образа\_диска>**

Эта команда присоединяет диск, существующий в хранилище BLOB-объектов, к существующей виртуальной машине, развернутой в облачной службе.

	~$ azure vm disk attach my-vm my-vm-my-vm-2-201242418259
	info:   Executing command vm disk attach
	info:   vm disk attach command OK

**vm disk attach-new &lt;имя\_виртуальной\_машины> &lt;размер\_в\_ГБ> [URL-адрес\_BLOB-объекта]**

Эта команда присоединяет диск данных к виртуальной машине Azure. В этом примере 20 — это размер (в гигабайтах) нового диска, который нужно присоединить. В качестве последнего аргумента при желании можно использовать URL-адрес BLOB-объекта, чтобы явно указать целевой BLOB-объект для создания. Если URL-адрес BLOB-объекта не указан, BLOB-объект создается автоматически.

	~$ azure vm disk attach-new nick-test36 20 http://nghinazz.blob.core.azure-preview.com/vhds/vmdisk1.vhd
	info:   Executing command vm disk attach-new
	info:   vm disk attach-new command OK  

**vm disk detach &lt;имя\_виртуальной\_машины> &lt;LUN>**

Эта команда отключает диск с данными, подключенный к виртуальной машине Azure. Параметр &lt;LUN> указывает диск, который необходимо отключить. Чтобы перед отключением диска получить список сопоставленных с ним дисков, запустите команду vm disk-list &lt;имя\_виртуальной\_машины>.

	~$ azure vm disk detach my-vm 2
	info:   Executing command vm disk detach
	info:   vm disk detach command OK

## Команды для управления облачными службами Azure

Облачные службы Azure — это приложения и службы, размещаемые в веб-ролях и рабочих ролях. Для управления облачными службами Azure можно использовать следующие команды.

**service create [параметры] &lt;имя\_службы>**

Эта команда создает новую облачную службу.

	~$ azure service create newservicemsopentech
	info:    Executing command service create
	+ Getting locations
	help:    Location:
	  1) East Asia
	  2) Southeast Asia
	  3) North Europe
	  4) West Europe
	  5) East US
	  6) West US
	  : 6
	+ Creating cloud service
	data:    Cloud service name newservicemsopentech
	info:    service create command OK

**service show [параметры] &lt;имя\_службы>**

Эта команда отображает информацию об облачной службе Azure.

	~$ azure service show newservicemsopentech
	info:    Executing command service show
	+ Getting cloud service
	data:    Name newservicemsopentech
	data:    Url https://management.core.windows.net/9e672699-1055-41ae-9c36-e85152f2e352/services/hostedservices/newservicemsopentech
	data:    Properties location West US
	data:    Properties label newservicemsopentech
	data:    Properties status Created
	data:    Properties dateCreated
	data:    Properties dateLastModified
	info:    service show command OK

**service list [параметры]**

Эта команда выводит список облачных служб Azure.

	~$ azure service list
	info:   Executing command service list
	data:   Name         Status
	data:   -----------  -------
	data:   service1     Created
	data:   service2     Created
	info:   service list command OK

**service delete [параметры] &lt;имя>**

Эта команда удаляет облачную службу Azure.

	~$ azure service delete myservice
	info:   Executing command service delete myservice
	info:   cloud-service delete command OK

Чтобы выполнить принудительное удаление, используйте параметр `-q`.


## Команды для управления сертификатами Azure

Сертификаты служб Azure — это SSL-сертификаты, подключенные к вашей учетной записи Azure. Дополнительную информацию о сертификатах Azure см. в разделе [Управление сертификатами](http://msdn.microsoft.com/library/azure/gg981929.aspx).

**service cert list [параметры]**

Эта команда выводит список сертификатов Azure.

	~$ azure service cert list
	info:   Executing command service cert list
	+ Fetching cloud services
	+ Fetching certificates
	data:   Service   Thumbprint                                Algorithm
	data:   --------  ----------------------------------------  ---------
	data:   myservice  262DBF95B5E61375FA27F1E74AC7D9EAE842916C  sha1
	info:   service cert list command OK

**service cert create &lt;префикс\_DNS> &lt;файл> [пароль]**

Эта команда передает сертификат. Не указывайте пароль для сертификатов, которые не защищены паролем.

	~$ azure service cert create nghinazz ~/publishSet.pfx
	info:   Executing command service cert create
	Cert password:
	+ Creating certificate
	info:   service cert create command OK

**service cert delete [параметры] &lt;отпечаток>**

Эта команда удаляет сертификат.

	~$ azure service cert delete 262DBF95B5E61375FA27F1E74AC7D9EAE842916C
	info:   Executing command service cert delete
	+ Deleting certificate
	info:   nghinazz : cert deleted
	info:   service cert delete command OK

## Команды для управления веб-приложениями

Веб-приложение Azure — это веб-конфигурация, доступ к которой можно получить с помощью универсального кода ресурса (URI). Веб-приложения размещаются на виртуальных машинах, но вам не нужно вдаваться в подробности, связанные с созданием и развертыванием виртуальных машин. Их для вас обработает Azure.

**site list [параметры]**

Эта команда отображает список веб-приложений.

	~$ azure site list
	info:   Executing command site list
	data:   Name            State    Host names
	data:   --------------  -------  --------------------------------------------------
	data:   mongosite       Running  mongosite.antdf0.antares.windows.net
	data:   myphpsite       Running  myphpsite.antdf0.antares.windows.net
	data:   mydrupalsite36  Running  mydrupalsite36.antdf0.antares.windows.net
	info:   site list command OK

**site set [параметры] [имя]**

Эта команда задает параметры конфигурации для веб-приложения, указанного в качестве аргумента [имя].

	~$ azure site set
	info:    Executing command site set
	Web site name: mydemosite
	+ Getting sites
	+ Updating site config information
	info:    site set command OK

**site deploymentscript [параметры]**

Эта команда создает пользовательский скрипт развертывания.

	~$ azure site deploymentscript --node
	info:    Executing command site deploymentscript
	info:    Generating deployment script for node.js Web Site
	info:    Generated deployment script files
	info:    site deploymentscript command OK

**site create [параметры] [имя]**

Эта команда создает новое веб-приложение и локальный каталог.

	~$ azure site create mysite
	info:   Executing command site create
	info:   Using location northeuropewebspace
	info:   Creating a new web site
	info:   Created web site at  mysite.antdf0.antares.windows.net
	info:   Initializing repository
	info:   Repository initialized
	info:   site create command OK

> [AZURE.NOTE]Имя сайта должно быть уникальным. Нельзя создать сайт, DNS-имя которого совпадает с существующим сайтом.

**site browse [параметры] [имя]**

Эта команда открывает веб-приложение в браузере.

	~$ azure site browse mysite
	info:   Executing command site browse
	info:   Launching browser to http://mysite.antdf0.antares-test.windows-int.net
	info:   site browse command OK

**site show [параметры] [имя]**

Эта команда отображает сведения о веб-приложении.

	~$ azure site show mysite
	info:   Executing command site show
	info:   Showing details for site
	data:   Site AdminEnabled true
	data:   Site HostNames mysite.antdf0.antares-test.windows-int.net
	data:   Site Name mysite
	data:   Site Owner 00060000814EDDEE
	data:   Site RepositorySiteName mysite
	data:   Site SelfLink https://s1.api.antdf0.antares.windows.net:454/subscriptions/444e62ff-4c5f-4116-a695-5c803ed584a5/webspaces/northeuropewebspace/sites/mysite
	data:   Site State Running
	data:   Site UsageState Normal
	data:   Site WebSpace northeuropewebspace
	data:   Config AppSettings
	data:   Config ConnectionStrings
	data:   Config DefaultDocuments 0=Default.htm, 1=Default.asp, 2=index.htm, 3=index.html, 4=iisstart.htm, 5=default.aspx, 6=index.php, 7=hostingstart.aspx
	data:   Config DetailedErrorLoggingEnabled false
	data:   Config HttpLoggingEnabled false
	data:   Config Metadata
	data:   Config NetFrameworkVersion v4.0
	data:   Config NumberOfWorkers 1
	data:   Config PhpVersion 5.3
	data:   Config PublishingPassword rJ}[Er2v[Y]q16B6vTD]n$[C2z}Z.pvgLfRcLnAp%ax]xstiLny};o@vmMAote@d
	data:   Config RequestTracingEnabled false
	data:   Repository https://mysite.scm.antdf0.antares-test.windows-int.net/
	info:   site show command OK

**site delete [параметры] [имя]**

Эта команда удаляет веб-приложение.

	~$ azure site delete mysite
	info:   Executing command site delete
	info:   Deleting site mysite
	info:   Site mysite has been deleted
	info:   site delete command OK

 **site swap [параметры] [имя]**

Эта команда переключает слоты двух веб-приложений.

Эта команда поддерживает следующий дополнительный параметр:

****-q или **--quiet**: не запрашивать подтверждение. Используйте этот параметр в автоматизированных скриптах.


**site start [параметры] [имя]**

Эта команда запускает веб-приложение.

	~$ azure site start mysite
	info:   Executing command site start
	info:   Starting site mysite
	info:   Site mysite has been started
	info:   site start command OK

**site stop [параметры] [имя]**

Эта команда останавливает веб-приложение.

	~$ azure site stop mysite
	info:   Executing command site stop
	info:   Stopping site mysite
	info:   Site mysite has been stopped
	info:   site stop command OK

****site restart [параметры] [имя]

Эта команда останавливает, а затем запускает указанное веб-приложение.

Эта команда поддерживает следующий дополнительный параметр:

**--slot** &lt;слот>: имя слота, который необходимо перезапустить.


**site location list [параметры]**

Эта команда отображает список расположений веб-приложений.

	~$ azure site location list
	info:    Executing command site location list
	+ Getting locations
	data:    Name
	data:    ----------------
	data:    West Europe
	data:    West US
	data:    North Central US
	data:    North Europe
	data:    East Asia
	data:    East US
	info:    site location list command OK

###Команды для управления параметрами веб-приложений

**site appsetting list [параметры] [имя]**

Эта команда отображает параметр приложения, добавленный в веб-приложение.

	~$ azure site appsetting list
	info:    Executing command site appsetting list
	Web site name: mydemosite
	+ Getting sites
	+ Getting site config information
	data:    Name  Value
	data:    ----  -----
	data:    test  value
	info:    site appsetting list command OK

**site appsetting add [параметры] &lt;пара\_ключ/значение> [имя]**

Эта команда добавляет параметр приложения в веб-приложение в качестве пары "ключ-значение".

	~$ azure site appsetting add test=value
	info:    Executing command site appsetting add
	Web site name: mydemosite
	+ Getting sites
	+ Getting site config information
	+ Updating site config information
	info:    site appsetting add command OK

**site appsetting delete [параметры] &lt;ключ> [имя]**

Эта команда удаляет указанный параметр приложения из веб-приложения.

	~$ azure site appsetting delete test
	info:    Executing command site appsetting delete
	Web site name: mydemosite
	+ Getting sites
	+ Getting site config information
	Delete application setting test? [y/n] y
	+ Updating site config information
	info:    site appsetting delete command OK

**site appsetting show [параметры] &lt;ключ> [имя]**

Эта команда отображает сведения об указанном параметре приложения.

	~$ azure site appsetting show test
	info:    Executing command site appsetting show
	Web site name: mydemosite
	+ Getting sites
	+ Getting site config information
	data:    Value:  value
	info:    site appsetting show command OK

###Команды для управления сертификатами веб-приложения

**site cert list [параметры] [имя]**

Эта команда отображает список сертификатов веб-приложения.

	~$ azure site cert list
	info:    Executing command site cert list
	Web site name: mydemosite
	+ Getting sites
	+ Getting site information
	data:    Subject                       Expiration Date	                  Thumbprint
	data:    ----------------------------  -----------------------------------------
	----------------  ----------------------------------------
	data:    *.msopentech.com              Fri Nov 28 2014 09:49:57 GMT-0800 (Pacific Standard Time)  A40E82D3DC0286D1F58650E570ECF8224F69A148
	data:    msopentech.azurewebsites.net  Fri Jun 19 2015 11:57:32 GMT-0700 (Pacific Daylight Time)  CE1CD6538852BF7A5DC32001C2E26A29B541F0E8
	info:    site cert list command OK

**site cert add [параметры] &lt;путь\_к\_сертификату> [имя]**

**site cert delete [параметры] &lt;отпечаток> [имя]**

**site cert show [параметры] &lt;отпечаток> [имя]**

Эта команда отображает сведения о сертификате.

	~$ azure site cert show CE1CD65852B38DC32001C2E0E8F7A526A29B541F
	info:    Executing command site cert show
	Web site name: mydemosite
	+ Getting sites
	+ Getting site information
	data:    Certificate hostNames 0=msopentech.azurewebsites.net
	data:    Certificate expirationDate
	data:    Certificate friendlyName msopentech.azurewebsites.net
	data:    Certificate issueDate
	data:    Certificate issuer CN=MSIT Machine Auth CA 2, DC=redmond, DC=corp, DC=microsoft, DC=com
	data:    Certificate subjectName msopentech.azurewebsites.net
	data:    Certificate thumbprint CE1CD65852B38DC32001C2E0E8F7A526A29B541F
	info:    site cert show command OK

###Команды для управления строками подключения веб-приложения

**site connectionstring list [параметры] [имя]**

**site connectionstring add [параметры] &lt;имя\_подключения> &lt;значение> &lt;тип> [имя]**

**site connectionstring delete [параметры] &lt;имя\_подключения> [имя]**

**site connectionstring show [параметры] &lt;имя\_подключения> [имя]**

###Команды управления документами по умолчанию для веб-приложения

**site defaultdocument list [параметры] [имя]**

**site defaultdocument add [параметры] &lt;документ> [имя]**

**site defaultdocument delete [параметры] &lt;документ> [имя]**

###Команды для управления развертываниями веб-приложения

**site deployment list [параметры] [имя]**

**site deployment show [параметры] &lt;идентификатор\_фиксации> [имя]**

**site deployment redeploy [параметры] &lt;идентификатор\_фиксации> [имя]**

**site deployment github [параметры] [имя]**

**site deployment user set [параметры] [имя\_пользователя] [пароль]**

###Команды для управления доменами веб-приложения

**site domain list [параметры] [имя]**

**site domain add [параметры] &lt;доменное\_имя> [имя]**

**site domain delete [параметры] &lt;доменное\_имя> [имя]**

###Команды для управления сопоставлениями обработчиков веб-приложения

**site handler list [параметры] [имя]**

**site handler add [параметры] &lt;расширение> &lt;обработчик> [имя]**

**site handler delete [параметры] &lt;расширение> [имя]**

###Команды для управления веб-заданиями

**site job list [параметры] [имя]**

Эта команда отображает список всех веб-заданий веб-приложения.

Эта команда поддерживает следующие дополнительные параметры:

+ **--job-type** &lt;тип\_задания>: необязательный параметр. Тип веб-задания. Допустимым является значение triggered или continuous. По умолчанию команда возвращает веб-задания всех типов.
+ **--slot** &lt;слот>: имя слота, который необходимо перезапустить.

**site job show [параметры] &lt;имя\_задания> &lt;тип\_задания> [имя]**

Эта команда отображает информацию о конкретном веб-задании.

Эта команда поддерживает следующие дополнительные параметры:

+ **--job-name** &lt;имя\_задания>: обязательный параметр. Имя веб-задания.
+ **--job-type** &lt;тип\_задания>: обязательный параметр. Тип веб-задания. Допустимым является значение triggered или continuous.
+ **--slot** &lt;слот>: имя слота, который необходимо перезапустить.

**site job delete [параметры] &lt;имя\_задания> &lt;тип\_задания> [имя]**

Эта команда удаляет указанное веб-задание.

Эта команда поддерживает следующие дополнительные параметры:

+ **--job-name** &lt;имя\_задания>: обязательный параметр. Имя веб-задания.
+ **--job-type** &lt;тип\_задания>: обязательный параметр. Тип веб-задания. Допустимым является значение triggered или continuous.
+ **-q** или **--quiet**: не запрашивать подтверждение. Используйте этот параметр в автоматизированных скриптах.
+ **--slot** &lt;слот>: имя слота, который необходимо перезапустить.

**site job upload [параметры] &lt;имя\_задания> &lt;тип\_задания> <jobFile> [имя]**

Эта команда удаляет указанное веб-задание.

Эта команда поддерживает следующие дополнительные параметры:

+ **--job-name** &lt;имя\_задания>: обязательный параметр. Имя веб-задания.
+ **--job-type** &lt;тип\_задания>: обязательный параметр. Тип веб-задания. Допустимым является значение triggered или continuous.
+ **--job-file** &lt;файл\_задания>: обязательный параметр. Файл задания.
+ **--slot** &lt;слот>: имя слота, который необходимо перезапустить.

**site job start [параметры] &lt;имя\_задания> &lt;тип\_задания> [имя]**

Эта команда запускает указанное веб-задание.

Эта команда поддерживает следующие дополнительные параметры:

+ **--job-name** &lt;имя\_задания>: обязательный параметр. Имя веб-задания.
+ **--job-type** &lt;тип\_задания>: обязательный параметр. Тип веб-задания. Допустимым является значение triggered или continuous.
+ **--slot** &lt;слот>: имя слота, который необходимо перезапустить.

**site job stop [параметры] &lt;имя\_задания> &lt;тип\_задания> [имя]**

Эта команда останавливает указанное веб-задание. Остановить можно только непрерывно выполняющиеся задания.

Эта команда поддерживает следующие дополнительные параметры:

+ **--job-name** &lt;имя\_задания>: обязательный параметр. Имя веб-задания.
+ **--slot** &lt;слот>: имя слота, который необходимо перезапустить.

###Команды для управления журналом веб-заданий

**site job history list [параметры] [имя\_задания] [имя]**

Эта команда отображает журнал запусков указанного веб-задания.

Эта команда поддерживает следующие дополнительные параметры:

+ **--job-name** &lt;имя\_задания>: обязательный параметр. Имя веб-задания.
+ **--slot** &lt;слот>: имя слота, который необходимо перезапустить.

**site job history show [параметры] [имя\_задания] [RunID] [имя]**

Эта команда возвращает информацию о запусках задания для указанного веб-задания.

Эта команда поддерживает следующие дополнительные параметры:

+ **--job-name** &lt;имя\_задания>: обязательный параметр. Имя веб-задания.
+ **--run-id** &lt;RunID>: необязательный параметр. Идентификатор журнала запусков. Если не указано, отображается последний запуск.
+ **--slot** &lt;слот>: имя слота, который необходимо перезапустить.

###Команды для управления диагностикой веб-приложения

**site log download [параметры] [имя]**

Скачивает ZIP-файл, содержащий данные диагностики веб-приложения.

	~$ azure site log download
	info:    Executing command site log download
	Web site name: mydemosite
	+ Getting sites
	+ Getting site information
	+ Downloading diagnostic log to diagnostics.zip
	info:    site log download command OK

**site log tail [параметры] [имя]**

Эта команда подключает терминал к службе потоковой передачи журналов.

	~$ azure site log tail
	info:    Executing command site log tail
	Web site name: mydemosite
	+ Getting sites
	+ Getting site information
	2013-11-19T17:24:17  Welcome, you are now connected to log-streaming service.

**site log set [параметры] [имя]**

Эта команда настраивает параметры диагностики для веб-приложения.

	~$ azure site log set -a
	info:    Executing command site log set
	+ Getting output options
	help:    Output:
	  1) file
	  2) storage
	  : 1
	Web site name: mydemosite
	+ Getting locations
	+ Getting sites
	+ Getting site information
	+ Getting diagnostic settings
	+ Updating diagnostic settings
	info:    site log set command OK

###Команды для управления репозиториями веб-приложения

**site repository branch [параметры] &lt;ветвь> [имя]**

**site repository delete [параметры] [имя]**

**site repository sync [параметры] [имя]**

###Команды для управления масштабированием веб-приложения

**site scale mode [параметры] &lt;режим> [имя]**

**site scale instances [параметры] &lt;экземпляры> [имя]**


## Команды для управления мобильными службами Azure

В мобильные службы Azure входит набор служб Azure, обеспечивающих серверные возможности для приложений. Команды мобильных служб делятся на следующие категории:

+ [Команды для управления экземплярами мобильных служб](#Mobile_Services)
+ [Команды для управления конфигурацией мобильных служб](#Mobile_Configuration)
+ [Команды для управления таблицами мобильных служб](#Mobile_Tables)
+ [Команды для управления сценариями мобильных служб](#Mobile_Scripts)
+ [Команды для управления запланированными заданиями](#Mobile_Jobs)
+ [Команды для масштабирования мобильной службы](#Mobile_Scale)

Для большинства команд мобильных служб применяются следующие параметры:

+ **-h** или **--help**: отобразить выходные данные об использовании.
+ **-s `<id>`** или **--subscription `<id>`**: использование определенной подписки, указанной как `<id>`.
+ **-v** или **--verbose**: записывать подробные выходные данные.
+ **--json**: записывать выходные данные в формате JSON.

### <a name="Mobile_Services"></a>Команды для управления экземплярами мобильных служб

**mobile locations [параметры]**

Эта команда выводит список географических расположений, поддерживаемых мобильными службами.

	~$ azure mobile locations
	info:    Executing command mobile locations
	info:    East US (default)
	info:    West US
	info:    North Europe

**mobile create [параметры] [имя\_службы] [имя\_администратора\_SQL] [пароль\_администратора\_SQL]**

Эта команда создает мобильные службы вместе с базой данных и сервером SQL.

	~$ azure mobile create todolist your_login_name Secure$Password
	info:    Executing command mobile create
	+ Creating mobile service
	info:    Overall application state: Healthy
	info:    Mobile service (todolist) state: ProvisionConfigured
	info:    SQL database (todolist_db) state: Provisioned
	info:    SQL server (e96ean1c6v) state: ProvisionConfigured
	info:    mobile create command OK

Эта команда поддерживает следующие дополнительные параметры:

+ **-r `<sqlServer>`** или **--sqlServer `<sqlServer>`**: использовать существующий сервер базы данных SQL, указанный как `<sqlServer>`.
+ **-d `<sqlDb>`** или **--sqlDb `<sqlDb>`**: использовать существующую базу данных SQL, заданную как `<sqlDb>`.
+ **-l `<location>`** или **--location `<location>`**: создать службу в определенном расположении, заданном как `<location>`. Для получения дополнительных расположений выполните команду azure mobile locations.
+ **--sqlLocation `<location>`**: создать сервер SQL в определенном `<location>`; по умолчанию используется расположение мобильной службы.

**mobile delete [параметры] [имя\_службы]**

Эта команда удаляет мобильные службы вместе с соответствующей базой данных и сервером SQL.

	~$ azure mobile delete todolist -a -q
	info:    Executing command mobile delete
	data:    Mobile service todolist
	data:    SQL database todolistAwrhcL60azo1C401
	data:    SQL server fh1kvbc7la
	+ Deleting mobile service
	info:    Deleted mobile service
	+ Deleting SQL server
	info:    Deleted SQL server
	+ Deleting mobile application
	info:    Deleted mobile application
	info:    mobile delete command OK

Эта команда поддерживает следующие дополнительные параметры:

+ **-d** или **--deleteData**: удалить все данные этой мобильной службы из базы данных.
+ **-a** или **--deleteAll**: удалить базу данных и сервер SQL.
+ **-q** или **--quiet**: не запрашивать подтверждение. Используйте этот параметр в автоматизированных скриптах.

**mobile list [параметры]**

Эта команда выводит список мобильных служб.

	~$ azure mobile list
	info:    Executing command mobile list
	data:    Name          State  URL
	data:    ------------  -----  --------------------------------------
	data:    todolist      Ready  https://todolist.azure-mobile.net/
	data:    mymobileapp   Ready  https://mymobileapp.azure-mobile.net/
	info:    mobile list command OK

**mobile show [параметры] [имя\_службы]**

Эта команда отображает сведения о мобильной службе.

	~$ azure mobile show todolist
	info:    Executing command mobile show
	+ Getting information
	info:    Mobile application
	data:    status Healthy
	data:    Mobile service name todolist
	data:    Mobile service status ProvisionConfigured
	data:    SQL database name todolistAwrhcL60azo1C401
	data:    SQL database status Linked
	data:    SQL server name fh1kvbc7la
	data:    SQL server status Linked
	info:    Mobile service
	data:    name todolist
	data:    state Ready
	data:    applicationUrl https://todolist.azure-mobile.net/
	data:    applicationKey XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
	data:    masterKey XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
	data:    webspace WESTUSWEBSPACE
	data:    region West US
	data:    tables TodoItem
	info:    mobile show command OK

**mobile restart [параметры] [имя\_службы]**

Эта команда перезапускает экземпляр мобильной службы.

	~$ azure mobile restart todolist
	info:    Executing command mobile restart
	+ Restarting mobile service
	info:    Service was restarted.
	info:    mobile restart command OK

**mobile log [параметры] [имя\_службы]**

Эта команда возвращает журналы мобильной службы, отфильтровывая все типы журналов, кроме `error`.

	~$ azure mobile log todolist -t error
	info:    Executing command mobile log
	data:
	data:    timeCreated 2013-01-07T16:04:43.351Z
	data:    type error
	data:    source /scheduler/TestingLogs.js
	data:    message This is an error.
	data:
	info:    mobile log command OK

Эта команда поддерживает следующие дополнительные параметры:

+ **-r `<query>`** или **--query `<query>`**: выполнить указанный запрос к журналу.
+ **-t `<type>`** или **--type `<type>`**: отфильтровывает возвращаемые журналы по записи `<type>`, которая может иметь значение `information`, `warning` или `error`.
+ **-k `<skip>`** или **--skip `<skip>`**: пропустить определенное количество строк, заданное значением `<skip>`.
+ **-p `<top>`** или **--top `<top>`**: возвратить определенное количество строк, заданных значением `<top>`.

> [AZURE.NOTE]Параметр **--query** имеет приоритет над параметрами **--type**, **--skip** и **--top**.

**mobile recover [параметры] [имя\_неработоспособной\_службы] [имя\_работоспособной\_службы]**

Эта команда восстанавливает неработоспособную мобильную службу, перенося ее в работоспособную службу в другом регионе.

Эта команда поддерживает следующий дополнительный параметр:

**-q** или **--quiet**: отключает запрос на подтверждение восстановления.

**mobile key regenerate [параметры] [имя\_службы] [тип]**

Эта команда заново создает ключ приложения мобильной службы.

	~$ azure mobile key regenerate todolist application
	info:    Executing command mobile key regenerate
	info:    New application key is SmLorAWVfslMcOKWSsuJvuzdJkfUpt40
	info:    mobile key regenerate command OK

Типы ключей: `master` и `application`.

> [AZURE.NOTE]При повторном создании ключей клиенты, использующие старый ключ, не смогут получить доступ к вашей мобильной службе. При повторном создании ключа приложения следует обновить приложение, используя новое значение ключа.

**mobile key set [параметры] [имя\_службы] [тип] [значение]**

Эта команда задает определенное значение ключу мобильной службы.


### <a name="Mobile_Configuration"></a>Команды для управления конфигурацией мобильных служб

**mobile config list [параметры] [имя службы]**

Эта команда выводит список параметров конфигурации для мобильной службы.

	~$ azure mobile config list todolist
	info:    Executing command mobile config list
	+ Getting mobile service configuration
	data:    dynamicSchemaEnabled true
	data:    microsoftAccountClientSecret Not configured
	data:    microsoftAccountClientId Not configured
	data:    microsoftAccountPackageSID Not configured
	data:    facebookClientId Not configured
	data:    facebookClientSecret Not configured
	data:    twitterClientId Not configured
	data:    twitterClientSecret Not configured
	data:    googleClientId Not configured
	data:    googleClientSecret Not configured
	data:    apnsMode none
	data:    apnsPassword Not configured
	data:    apnsCertifcate Not configured
	info:    mobile config list command OK

**mobile config get [параметры] [имя\_службы] [ключ]**

Эта команда возвращает конкретный параметр конфигурации для мобильной службы, в данном случае динамическую схему.

	~$ azure mobile config get todolist dynamicSchemaEnabled
	info:    Executing command mobile config get
	data:    dynamicSchemaEnabled true
	info:    mobile config get command OK

**mobile config set [параметры] [имя\_службы] [ключ] [значение]**

Эта команда задает конкретный параметр конфигурации для мобильной службы, в данном случае динамическую схему.

	~$ azure mobile config set todolist dynamicSchemaEnabled false
	info:    Executing command mobile config set
	info:    mobile config set command OK


### <a name="Mobile_Tables"></a>Команды для управления таблицами мобильных служб

**mobile table list [параметры] [имя службы]**

Эта команда выводит список всех таблиц в вашей мобильной службе.

	~$azure mobile table list todolist
	info:    Executing command mobile table list
	data:    Name      Indexes  Rows
	data:    --------  -------  ----
	data:    Channel   1        0
	data:    TodoItem  1        0
	info:    mobile table list command OK

**mobile table show [параметры] [имя\_службы] [имя\_таблицы]**

Эта команда возвращает сведения о конкретной таблице.

	~$azure mobile table show todolist
	info:    Executing command mobile table show
	+ Getting table information
	info:    Table statistics:
	data:    Number of records 5
	info:    Table operations:
	data:    Operation  Script       Permissions
	data:    ---------  -----------  -----------
	data:    insert     1900 bytes   user
	data:    read       Not defined  user
	data:    update     Not defined  user
	data:    delete     Not defined  user
	info:    Table columns:
	data:    Name  Type           Indexed
	data:    ----  -------------  -------
	data:    id    bigint(MSSQL)  Yes
	data:    text      string
	data:    complete  boolean
	info:    mobile table show command OK

**mobile table create [параметры] [имя\_службы] [имя\_таблицы]**

Эта команда создает таблицу.

	~$azure mobile table create todolist Channels
	info:    Executing command mobile table create
	+ Creating table
	info:    mobile table create command OK

Эта команда поддерживает следующий дополнительный параметр:

+ **-p `&lt;permissions>`** или **--permissions `&lt;permissions>`**: список (разделитель — запятая) пар `<operation>`=`<permission>`, где `<operation>` принимает значение `insert`, `read`, `update` или `delete`, а `&lt;permissions>` — значение `public`, `application` (по умолчанию), `user` или `admin`.

**mobile data read [параметры] [имя\_службы] [имя\_таблицы] [запрос]**

Эта команда читает данные из таблицы.

	~$azure mobile data read todolist TodoItem
	info:    Executing command mobile data read
	data:    id  text     complete
	data:    --  -------  --------
	data:    1   item #1  false
	data:    2   item #2  true
	data:    3   item #3  false
	data:    4   item #4  true
	info:    mobile data read command OK

Эта команда поддерживает следующие дополнительные параметры:

+ **-k `<skip>`** или **--skip `<skip>`**: пропустить определенное количество строк, заданное значением `<skip>`.
+ **-t `<top>`** или **--top `<top>`**: возвратить определенное количество строк, заданное значением `<top>`.
+ **-l** или **--list**: возвратить данные в формате списка.

**mobile table update [параметры] [имя\_службы] [имя\_таблицы]**

Эта команда изменяет разрешения на удаление таблицы, оставляя это разрешение только администратору.

	~$azure mobile table update todolist Channels -p delete=admin
	info:    Executing command mobile table update
	+ Updating permissions
	info:    Updated permissions
	info:    mobile table update command OK

Эта команда поддерживает следующие дополнительные параметры:

+ **-p `&lt;permissions>`** или **--permissions `&lt;permissions>`**: список (разделитель — запятая) пар `<operation>`=`<permission>`, где `<operation>` принимает значение `insert`, `read`, `update` или `delete`, а `&lt;permissions>` — значение `public`, `application` (по умолчанию), `user` или `admin`.
+ **--deleteColumn `<columns>`**: список (разделитель — запятая) удаляемых столбцов, заданных как `<columns>`.
+ **-q** или **--quiet**: удалить столбцы без запроса подтверждения.
+ **--addIndex `<columns>`**: список (разделитель — запятая) столбцов, включаемых в индекс.
+ **--deleteIndex `<columns>`**: список (разделитель — запятая) столбцов, исключаемых из индекса.

**mobile table delete [параметры] [имя\_службы] [имя\_таблицы]**

Эта команда удаляет таблицу.

	~$azure mobile table delete todolist Channels
	info:    Executing command mobile table delete
	Do you really want to delete the table (yes/no): yes
	+ Deleting table
	info:    mobile table delete command OK

Укажите параметр -q, чтобы удалить таблицу без подтверждения. Это необходимо для предотвращения блокировки скриптов автоматизации.

**mobile data truncate [параметры] [имя\_службы] [имя\_таблицы]**

Эта команда удаляет все строки данных из таблицы.

	~$azure mobile data truncate todolist TodoItem
	info:    Executing command mobile data truncate
	info:    There are 7 data rows in the table.
	Do you really want to delete all data from the table? (y/n): y
	info:    Deleted 7 rows.
	info:    mobile data truncate command OK


### <a name="Mobile_Scripts"></a>Команды для управления сценариями

Описанные в этом разделе команды используются для управления серверными сценариями, которые относятся к мобильной службе. Дополнительную информацию см. в разделе [Работа с серверными сценариями в мобильных службах](../mobile-services/mobile-services-how-to-use-server-scripts.md).

**mobile script list [параметры] [имя\_службы]**

Эта команда выводит список зарегистрированных скриптов, включая и таблицы, и скрипты планировщика.

	~$azure mobile script list todolist
	info:    Executing command mobile script list
	+ Getting script information
	info:    Table scripts
	data:    Name                   Size
	data:    ---------------------  ----
	data:    table/TodoItem.delete  256
	data:    table/Devices.insert   1660
	error:   Unable to get shared scripts
	info:    Scheduler scripts
	data:    Name                 Status     Interval   Last run   Next run
	data:    -------------------  ---------  ---------  ---------  ---------
	data:    scheduler/undefined  undefined  undefined  undefined  undefined
	data:    scheduler/undefined  undefined  undefined  undefined  undefined
	info:    mobile script list command OK

**mobile script download [параметры] [имя\_службы] [имя\_сценария]**

Эта команда скачивает сценарий вставки из таблицы TodoItem в файл с именем `todoitem.insert.js` во вложенной папке `table`.

	~$azure mobile script download todolist table/todoitem.insert.js
	info:    Executing command mobile script download
	info:    Saved script to ./table/todoitem.insert.js
	info:    mobile script download command OK

Эта команда поддерживает следующие дополнительные параметры:

+ **-p `<path>`** или **--path `<path>`**: расположение в файле, в котором необходимо сохранить сценарий; по умолчанию используется текущий рабочий каталог.
+ **-f `<file>`** или **--file `<file>`**: имя файла, в котором необходимо сохранить сценарий.
+ **-o** или **--override**: перезаписать существующий файл.
+ **-c** или **--console**: записать сценарий на консоль, а не в файл.

**mobile script upload [параметры] [имя\_службы] [имя\_сценария]**

Эта команда отправляет новый сценарий с именем `todoitem.insert.js` из вложенной папки `table`.

	~$azure mobile script upload todolist table/todoitem.insert.js
	info:    Executing command mobile script upload
	info:    mobile script upload command OK

Имя файла должно состоять из имен таблицы и операции, и файл должен находиться во вложенной папке таблицы относительно расположения, где выполняется команда. Кроме того, с помощью параметра **-f `<file>`** или **--file `<file>`** можно указать другое имя файла и путь к файлу, содержащему сценарий, который необходимо зарегистрировать.


**mobile script delete [параметры] [имя\_службы] [имя\_сценария]**

Эта команда удаляет существующий скрипт вставки из таблицы TodoItem.

	~$azure mobile script delete todolist table/todoitem.insert.js
	info:    Executing command mobile script delete
	info:    mobile script delete command OK

### <a name="Mobile_Jobs"></a>Команды для управления запланированными заданиями

Описанные в этом разделе команды используются для управления запланированными заданиями, относящимися к мобильной службе. Дополнительные сведения см. в разделе [Расписание заданий](http://msdn.microsoft.com/library/windowsazure/jj860528.aspx).

**mobile job list [параметры] [имя\_службы]**

Эта команда выводит список запланированных заданий.

	~$azure mobile job list todolist
	info:    Executing command mobile job list
	info:    Scheduled jobs
	data:    Job name    Script name           Status    Interval     Last run              Next run
	data:    ----------  --------------------  --------  -----------  --------------------  --------------------
	data:    getUpdates  scheduler/getUpdates  enabled   15 [minute]  2013-01-14T16:15:00Z  2013-01-14T16:30:00Z
	info:    You can manipulate scheduled job scripts using the 'azure mobile script' command.
	info:    mobile job list command OK

**mobile job create [параметры] [имя\_службы] [имя\_задания]**

Эта команда создает новое задание с именем `getUpdates`, которое будет выполняться каждый час согласно расписанию.

	~$azure mobile job create -i 1 -u hour todolist getUpdates
	info:    Executing command mobile job create
	info:    Job was created in disabled state. You can enable the job using the 'azure mobile job update' command.
	info:    You can manipulate the scheduled job script using the 'azure mobile script' command.
	info:    mobile job create command OK

Эта команда поддерживает следующие дополнительные параметры:

+ **-i `<number>`** или **--interval `<number>`**: интервал выполнения задания, целое число; по умолчанию используется значение `15`.
+ **-u `<unit>`** или **--intervalUnit `<unit>`**: единица измерения для параметра _interval_. Может принимать указанные ниже значения.
	+ **minute** (по умолчанию)
	+ **hour**
	+ **day**
	+ **month**
	+ **none** (задания по запросу)
+ **-t `<time>`** **--startTime `<time>`** Время начала первого выполнения сценария в формате ISO; по умолчанию используется значение `now`.

> [AZURE.NOTE]Новые задания создаются в отключенном состоянии, так как необходимо еще передать сценарий. Используйте команду **mobile script upload** для передачи сценария и команду **mobile job update** для включения задания.

**mobile job update [параметры] [имя\_службы] [имя\_задания]**

Указанная ниже команда включает выключенное задание `getUpdates`.

	~$azure mobile job update -a enabled todolist getUpdates
	info:    Executing command mobile job update
	info:    mobile job update command OK

Эта команда поддерживает следующие дополнительные параметры:

+ **-i `<number>`** или **--interval `<number>`**: интервал выполнения задания, целое число; по умолчанию используется значение `15`.
+ **-u `<unit>`** или **--intervalUnit `<unit>`**: единица измерения для параметра _interval_. Может принимать указанные ниже значения.
	+ **minute** (по умолчанию)
	+ **hour**
	+ **day**
	+ **month**
	+ **none** (задания по запросу)
+ **-t `<time>`** **--startTime `<time>`** Время начала первого выполнения сценария в формате ISO; по умолчанию используется значение `now`.
+ **-a `<status>`** или **--status `<status>`**: состояние задания. Может принимать значение `enabled` или `disabled`.

**mobile job delete [параметры] [имя\_службы] [имя\_задания]**

Эта команда удаляет запланированное задание getUpdates с сервера TodoList.

	~$azure mobile job delete todolist getUpdates
	info:    Executing command mobile job delete
	info:    mobile job delete command OK

> [AZURE.NOTE]При удалении задания также удаляется переданный скрипт.

### <a name="Mobile_Scale"></a>Команды для масштабирования мобильной службы

Описанные в этом разделе команды используются для масштабирования мобильной службы. Дополнительную информацию см. в разделе [Масштабирование мобильной службы](http://msdn.microsoft.com/library/windowsazure/jj193178.aspx).

**mobile scale show [параметры] [имя\_службы]**

Эта команда отображает сведения о масштабировании, включая текущий режим среды выполнения и число экземпляров.

	~$azure mobile scale show todolist
	info:    Executing command mobile scale show
	data:    webspace WESTUSWEBSPACE
	data:    computeMode Free
	data:    numberOfInstances 1
	info:    mobile scale show command OK

**mobile scale change [параметры] [имя\_службы]**

Эта команда изменяет масштаб мобильной службы с бесплатного на расширенный.

	~$azure mobile scale change -c Reserved -i 1 todolist
	info:    Executing command mobile scale change
	+ Rescaling the mobile service
	info:    mobile scale change command OK

Эта команда поддерживает следующие дополнительные параметры:

+ **-c `<mode>`** или **--computeMode `<mode>`**: режим среды выполнения должен иметь значение `Free` или `Reserved`.
+ **-i `<count>`** или **--numberOfInstances `<count>`**: количество экземпляров, используемых при работе в режиме "Зарезервировано".

> [AZURE.NOTE]Если для режима среды выполнения задано значение `Reserved`, все ваши мобильные службы в одном и том же регионе будут работать в расширенном режиме.


###Команды для включения предварительных версий компонентов для мобильной службы

**mobile preview list [параметры] [имя\_службы]**

Эта команда отображает предварительные версии компонентов, доступные в указанной службе, а также показывает, включены ли они.

	~$ azure mobile preview list mysite
	info:    Executing command mobile preview list
	+ Getting preview features
	data:    Preview feature  Enabled
	data:    ---------------  -------
	data:    SourceControl    No
	data:    Users            No
	info:    You can enable preview features using the 'azure mobile preview enable' command.
	info:    mobile preview list command OK

**mobile preview enable [параметры] [имя\_службы] [имя\_компонента]**

Эта команда включает указанную предварительную версию компонента для мобильной службы. Обратите внимание, что после включения предварительную версию компонента для мобильной службы нельзя отключить.

###Команды для управления интерфейсами API мобильных служб

**mobile api list [параметры] [имя\_службы]**

Эта команда отображает список настраиваемых интерфейсов API мобильных служб, которые вы создали для своей мобильной службы.

	~$ azure mobile api list mysite
	info:    Executing command mobile api list
	+ Retrieving list of APIs
	info:    APIs
	data:    Name                  Get          Put          Post         Patch        Delete
	data:    --------------------  -----------  -----------  -----------  -----------  -----------
	data:    myCustomRetrieveAPI   application  application  application  application  application
	info:    You can manipulate API scripts using the 'azure mobile script' command.
	info:    mobile api list command OK

**mobile api create [параметры] [имя\_службы] [имя\_API]**

Создает настраиваемый API мобильных служб.

	~$ azure mobile api create mysite myCustomRetrieveAPI
	info:    Executing command mobile api create
	+ Creating custom API: 'myCustomRetrieveAPI'
	info:    API was created successfully. You can modify the API using the 'azure mobile script' command.
	info:    mobile api create command OK

Эта команда поддерживает следующий дополнительный параметр:

**-p** или **--permissions** &lt;разрешения>: список (разделитель — запятая) пар &lt;метод>=&lt;разрешение>.

**mobile api update [параметры] [имя\_службы] [имя\_API]**

Эта команда обновляет указанный настраиваемый API мобильных служб.

Эта команда поддерживает следующий дополнительный параметр:

Эта команда поддерживает следующие дополнительные параметры:

+ **-p** или **--permissions** &lt;разрешения>: список (разделитель — запятая) пар &lt;метод>=&lt;разрешение>.
+ **-f** или **--force**: переопределяет любые пользовательские изменения в файле метаданных разрешений.

**mobile api delete [параметры] [имя\_службы] [имя\_API]**

	~$ azure mobile api delete mysite myCustomRetrieveAPI
	info:    Executing command mobile api delete
	+ Deleting API: 'myCustomRetrieveAPI'
	info:    mobile api delete command OK

Эта команда удаляет указанный настраиваемый API мобильных служб.

###Команды для управления параметрами мобильных приложений

**mobile appsetting list [параметры] [имя\_службы]**

Эта команда отображает параметры мобильных приложений для указанной службы.

	~$ azure mobile appsetting list mysite
	info:    Executing command mobile appsetting list
	+ Retrieving app settings
	data:    Name               Value
	data:    -----------------  -----
	data:    enablebetacontent  true
	info:    mobile appsetting list command OK

**mobile appsetting add [параметры] [имя\_службы] [имя] [значение]**

Эта команда добавляет настраиваемый параметр приложений для мобильной службы.

	~$ azure mobile appsetting add mysite enablebetacontent true
	info:    Executing command mobile appsetting add
	+ Retrieving app settings
	+ Adding app setting
	info:    mobile appsetting add command OK

**mobile appsetting delete [параметры] [имя\_службы] [имя]**

Эта команда удаляет указанный параметр приложений для мобильной службы.

	~$ azure mobile appsetting delete mysite enablebetacontent
	info:    Executing command mobile appsetting delete
	+ Retrieving app settings
	+ Removing app setting 'enablebetacontent'
	info:    mobile appsetting delete command OK

**mobile appsetting show [параметры] [имя\_службы] [имя]**

Эта команда удаляет указанный параметр приложений для мобильной службы.

	~$ azure mobile appsetting show mysite enablebetacontent
	info:    Executing command mobile appsetting show
	+ Retrieving app settings
	info:    enablebetacontent: true
	info:    mobile appsetting show command OK

## Управление локальными параметрами средства

Локальные параметры связаны с вашим идентификатором подписки и именем учетной записи хранения по умолчанию.

**config list [параметры]**

Эта команда отображает параметры конфигурации.

	~$ azure config list
	info:   Displaying config settings
	data:   Setting                Value
	data:   ---------------------  ------------------------------------
	data:   subscription           32-digit-subscription-key
	data:   defaultStorageAccount  name

**config set [параметры] &lt;имя&gt;,&lt;значение&gt;**

Эта команда изменяет параметр конфигурации.

	~$ azure config set defaultStorageAccount myname
	info:   Setting 'defaultStorageAccount' to value 'myname'
	info:   Changes saved.

## Команды для управления службой Service Bus

Для управления учетной записью Service Bus используйте следующие команды:

**sb namespace check [параметры] &lt;имя>**

Проверяет допустимость и доступность пространства имен Service Bus.

**sb namespace create &lt;имя> &lt;расположение>**

Создает новое пространство имен Service Bus.

	~$ azure sb namespace create mysbnamespacea-test "West US"
	info:    Executing command sb namespace create
	+ Creating namespace mysbnamespacea-test in region West US
	data:    Name: mysbnamespacea-test
	data:    Region: West US
	data:    DefaultKey: fBu8nQ9svPIesFfMFVhCFD+/sY0rRbifWMoRpYy0Ynk=
	data:    Status: Activating
	data:    CreatedAt: 2013-11-14T16:23:29.32Z
	data:    AcsManagementEndpoint: https://mysbnamespacea-test-sb.accesscontrol.windows.net/
	data:    ServiceBusEndpoint: https://mysbnamespacea-test.servicebus.windows.net/

	data:    ConnectionString: Endpoint=sb://mysbnamespacea-test.servicebus.windows.
	net/;SharedSecretIssuer=owner;SharedSecretValue=fBu8nQ9svPIesFfMFVhCFD+/sY0rRbif
	WMoRpYy0Ynk=
	data:    SubscriptionId: 8679c8be3b0549d9b8fb4bd232a48931
	data:    Enabled: true
	data:    _: [object Object]
	info:    sb namespace create command OK


**sb namespace delete &lt;имя>**

Удаляет пространство имен.

	~$ azure sb namespace delete mysbnamespacea-test
	info:    Executing command sb namespace delete
	Delete namespace mysbnamespacea-test? [y/n] y
	+ Deleting namespace mysbnamespacea-test
	info:    sb namespace delete command OK

**sb namespace list**

Выводит список всех пространств имен, созданных для вашей учетной записи.

	~$ azure sb namespace list
	info:    Executing command sb namespace list
	+ Getting namespaces
	data:    Name                 Region   Status
	data:    -------------------  -------  ------
	data:    mysbnamespacea-test  West US  Active
	info:    sb namespace list command OK


**sb namespace location list**

Отображает список всех доступных расположений пространств имен.

	~$ azure sb namespace location list
	info:    Executing command sb namespace location list
	+ Getting locations
	data:    Name              Code
	data:    ----------------  ----------------
	data:    East Asia         East Asia
	data:    West Europe       West Europe
	data:    North Europe      North Europe
	data:    East US           East US
	data:    Southeast Asia    Southeast Asia
	data:    North Central US  North Central US
	data:    West US           West US
	data:    South Central US  South Central US
	info:    sb namespace location list command OK

**sb namespace show &lt;имя>**

Отображает информацию о конкретном пространстве имен.

	~$ azure sb namespace show mysbnamespacea-test
	info:    Executing command sb namespace show
	+ Getting namespace
	data:    Name: mysbnamespacea-test
	data:    Region: West US
	data:    DefaultKey: fBu8nQ9svPIesFfMFVhCFD+/sY0rRbifWMoRpYy0Ynk=
	data:    Status: Active
	data:    CreatedAt: 2013-11-14T16:23:29.32Z
	data:    AcsManagementEndpoint: https://mysbnamespacea-test-sb.accesscontrol.windows.net/
	data:    ServiceBusEndpoint: https://mysbnamespacea-test.servicebus.windows.net/

	data:    ConnectionString: Endpoint=sb://mysbnamespacea-test.servicebus.windows.
	net/;SharedSecretIssuer=owner;SharedSecretValue=fBu8nQ9svPIesFfMFVhCFD+/sY0rRbif
	WMoRpYy0Ynk=
	data:    SubscriptionId: 8679c8be3b0549d9b8fb4bd232a48931
	data:    Enabled: true
	data:    UpdatedAt: 2013-11-14T16:25:37.85Z
	info:    sb namespace show command OK

**sb namespace verify &lt;имя>**

Проверяет, доступно ли пространство имен.

## Команды для управления объектами службы хранилища

###Команды для управления учетными записями хранения

**storage account list [параметры]**

Эта команда отображает учетные записи хранения в вашей подписке.

	~$ azure storage account list
	info:    Executing command storage account list
	+ Getting storage accounts
	data:    Name             Label  Location
	data:    ---------------  -----  --------
	data:    mybasestorage           West US
	info:    storage account list command OK

**storage account show [параметры] <name>**

Эта команда отображает информацию об указанной учетной записи хранения, включая универсальный код ресурса (URI) и свойства учетной записи.

**storage account create [параметры] <name>**

Эта команда создает учетную запись хранения на основе предоставленных параметров.

	~$ azure storage account create mybasestorage --label PrimaryStorage --location "West US"
	info:    Executing command storage account create
	+ Creating storage account
	info:    storage account create command OK

Эта команда поддерживает следующие дополнительные параметры:

+ **-e** или **--label** &lt;метка>: метка для учетной записи хранения.
+ **-d** или **--description** &lt;описание>: описание учетной записи хранения.
+ **-l** или **--location** &lt;имя>: географический регион, в котором необходимо создать учетную запись хранения.
+ **-a** или **--affinity-group** &lt;имя>: территориальная группа, с которой необходимо сопоставить учетную запись хранения.
+ **--type**: тип создаваемой учетной записи (либо хранилище класса Standard с возможностью настройки избыточности (LRS/ZRS/GRS/RAGRS), либо хранилище класса Premium (PLRS)).

**storage account set [параметры] <name>**

Эта команда обновляет указанную учетную запись хранения.

	~$ azure storage account set mybasestorage --type GRS
	info:    Executing command storage account set
	+ Updating storage account
	info:    storage account set command OK

Эта команда поддерживает следующие дополнительные параметры:

+ **-e** или **--label** &lt;метка>: метка для учетной записи хранения.
+ **-d** или **--description** &lt;описание>: описание учетной записи хранения.
+ **-l** или **--location** &lt;имя>: географический регион, в котором необходимо создать учетную запись хранения.
+ **--type**: новый тип учетной записи (либо хранилище класса Standard с возможностью настройки избыточности (LRS/ZRS/GRS/RAGRS), либо хранилище класса Premium (PLRS)).

**storage account delete [параметры] <name>**

Эта команда удаляет указанную учетную запись хранения.

Эта команда поддерживает следующий дополнительный параметр:

**-q** или **--quiet**: не запрашивать подтверждение. Используйте этот параметр в автоматизированных скриптах.

###Команды для управления ключами учетной записи хранения

**storage account keys list [параметры] <name>**

Эта команда выводит список первичных и вторичных ключей для указанной учетной записи хранения.

**storage account keys renew [параметры] <name>**

###Команды для управления контейнером службы хранилища

**storage container list [параметры] [префикс]**

Эта команда отображает список контейнеров службы хранилища для указанной учетной записи хранения. Учетная запись хранения указывается как строка подключения или имя учетной записи хранения с ключом учетной записи.

Эта команда поддерживает следующие дополнительные параметры:

+ **-p** или **-prefix** &lt;префикс>: префикс имени контейнера службы хранилища.
+ **-a** или **--account-name** &lt;имя\_учетной\_записи>: имя учетной записи хранения.
+ **-k** или **--account-key** &lt;ключ\_учетной\_записи>: ключ учетной записи хранения.
+ **-c** или **--connection-string** &lt;строка\_подключения>: строка подключения к службе хранилища.
+ **--debug**: запуск команды storage в режиме отладки.

**storage container show [параметры] [контейнер]** **storage container create [параметры] [контейнер]**

Эта команда создает контейнер службы хранилища для указанной учетной записи хранения. Учетная запись хранения указывается как строка подключения или имя учетной записи хранения с ключом учетной записи.

Эта команда поддерживает следующие дополнительные параметры:

+ **--container** &lt;контейнер>: имя контейнера службы хранилища, который необходимо создать.
+ **-p** или **-prefix** &lt;префикс>: префикс имени контейнера службы хранилища.
+ **-a** или **--account-name** &lt;имя\_учетной\_записи>: имя учетной записи хранения.
+ **-k** или **--account-key** &lt;ключ\_учетной\_записи>: ключ учетной записи хранения.
+ **-c** или **--connection-string** &lt;строка\_подключения>: строка подключения к службе хранилища.
+ **--debug**: запуск команды storage в режиме отладки.

**storage container delete [параметры] [контейнер]**

Эта команда удаляет указанный контейнер службы хранилища. Учетная запись хранения указывается как строка подключения или имя учетной записи хранения с ключом учетной записи.

Эта команда поддерживает следующие дополнительные параметры:

+ **--container** &lt;контейнер>: имя контейнера службы хранилища, который необходимо создать.
+ **-p** или **-prefix** &lt;префикс>: префикс имени контейнера службы хранилища.
+ **-a** или **--account-name** &lt;имя\_учетной\_записи>: имя учетной записи хранения.
+ **-k** или **--account-key** &lt;ключ\_учетной\_записи>: ключ учетной записи хранения.
+ **-c** или **--connection-string** &lt;строка\_подключения>: строка подключения к службе хранилища.
+ **--debug**: запуск команды storage в режиме отладки.

**storage container set [параметры] [контейнер]**

Эта команда задает список управления доступом для контейнера службы хранилища. Учетная запись хранения указывается как строка подключения или имя учетной записи хранения с ключом учетной записи.

Эта команда поддерживает следующие дополнительные параметры:

+ **--container** &lt;контейнер>: имя контейнера службы хранилища, который необходимо создать.
+ **-p** или **-prefix** &lt;префикс>: префикс имени контейнера службы хранилища.
+ **-a** или **--account-name** &lt;имя\_учетной\_записи>: имя учетной записи хранения.
+ **-k** или **--account-key** &lt;ключ\_учетной\_записи>: ключ учетной записи хранения.
+ **-c** или **--connection-string** &lt;строка\_подключения>: строка подключения к службе хранилища.
+ **--debug**: запуск команды storage в режиме отладки.

###Команды для управления большими двоичными объектами службы хранилища

**storage blob list [параметры] [контейнер] [префикс]**

Эта команда возвращает список больших двоичных объектов службы хранилища в указанном контейнере службы хранилища.

Эта команда поддерживает следующие дополнительные параметры:

+ **--container** &lt;контейнер>: имя контейнера службы хранилища, который необходимо создать.
+ **-p** или **-prefix** &lt;префикс>: префикс имени контейнера службы хранилища.
+ **-a** или **--account-name** &lt;имя\_учетной\_записи>: имя учетной записи хранения.
+ **-k** или **--account-key** &lt;ключ\_учетной\_записи>: ключ учетной записи хранения.
+ **-c** или **--connection-string** &lt;строка\_подключения>: строка подключения к службе хранилища.
+ **--debug**: запуск команды storage в режиме отладки.

**storage blob show [параметры] [контейнер] [BLOB-объект]**

Эта команда отображает информацию об указанном большом двоичном объекте службы хранилища.

Эта команда поддерживает следующие дополнительные параметры:

+ **--container** &lt;контейнер>: имя контейнера службы хранилища, который необходимо создать.
+ **-p** или **-prefix** &lt;префикс>: префикс имени контейнера службы хранилища.
+ **-a** или **--account-name** &lt;имя\_учетной\_записи>: имя учетной записи хранения.
+ **-k** или **--account-key** &lt;ключ\_учетной\_записи>: ключ учетной записи хранения.
+ **-c** или **--connection-string** &lt;строка\_подключения>: строка подключения к службе хранилища.
+ **--debug**: запуск команды storage в режиме отладки.

**storage blob delete [параметры] [контейнер] [BLOB-объект]**

Эта команда поддерживает следующие дополнительные параметры:

+ **--container** &lt;контейнер>: имя контейнера службы хранилища, который необходимо создать.
+ **-b** или **--blob** &lt;имя\_большого\_двоичного\_объекта>: имя большого двоичного объекта службы хранилища, который необходимо удалить.
+ **-q** или **--quiet**: удаление указанного большого двоичного объекта службы хранилища без подтверждения.
+ **-a** или **--account-name** &lt;имя\_учетной\_записи>: имя учетной записи хранения.
+ **-k** или **--account-key** &lt;ключ\_учетной\_записи>: ключ учетной записи хранения.
+ **-c** или **--connection-string** &lt;строка\_подключения>: строка подключения к службе хранилища.
+ **--debug**: запуск команды storage в режиме отладки.

**storage blob upload [параметры] [файл] [контейнер] [BLOB-объект]**

Эта команда передает указанный файл в указанный большой двоичный объект службы хранилища.

Эта команда поддерживает следующие дополнительные параметры:

+ **--container** &lt;контейнер>: имя контейнера службы хранилища, который необходимо создать.
+ **-b** или **--blob** &lt;имя\_большого\_двоичного\_объекта>: имя большого двоичного объекта службы хранилища, который необходимо отправить.
+ **-t** или **--blobtype** &lt;тип\_большого\_двоичного\_объекта>: тип большого двоичного объекта службы хранилища: страница или блок.
+ **-p** или **--properties** &lt;свойства>: свойства большого двоичного объекта службы хранилища для отправляемого файла. Свойства — это пары "ключ=значение", разделенные точкой с запятой (;). Доступные свойства: contentType, contentEncoding, contentLanguage и cacheControl.
+ **-m** или **--metadata** &lt;метаданные>: метаданные большого двоичного объекта службы хранилища для отправляемого файла. Метаданные — это пары "ключ=значение", разделенные точкой с запятой (;).
+ **--concurrenttaskcount** &lt;количество\_одновременно\_выполняющихся\_задач>: максимальное количество одновременных запросов на отправку.
+ **-q** или **--quiet**: перезапись указанного большого двоичного объекта службы хранилища без подтверждения.
+ **-a** или **--account-name** &lt;имя\_учетной\_записи>: имя учетной записи хранения.
+ **-k** или **--account-key** &lt;ключ\_учетной\_записи>: ключ учетной записи хранения.
+ **-c** или **--connection-string** &lt;строка\_подключения>: строка подключения к службе хранилища.
+ **--debug**: запуск команды storage в режиме отладки.

**storage blob download [параметры] [контейнер] [BLOB-объект] [назначение]**

Эта команда скачивает указанный большой двоичный объект службы хранилища.

Эта команда поддерживает следующие дополнительные параметры:

+ **--container** &lt;контейнер>: имя контейнера службы хранилища, который необходимо создать.
+ **-b** или **--blob** &lt;имя\_большого\_двоичного\_объекта>: имя большого двоичного объекта службы хранилища
+ **-d** или **--destination** [назначение]: конечный путь к файлу или каталогу, в который будет скачан объект.
+ **-m** или **--checkmd5**: проверка контрольной суммы MD5 для скачанного файла.
+ **--concurrenttaskcount** &lt;количество\_одновременно\_выполняющихся\_задач>: максимальное количество одновременных запросов на отправку.
+ **-q** или **--quiet**: перезапись конечного файла без подтверждения.
+ **-a** или **--account-name** &lt;имя\_учетной\_записи>: имя учетной записи хранения.
+ **-k** или **--account-key** &lt;ключ\_учетной\_записи>: ключ учетной записи хранения.
+ **-c** или **--connection-string** &lt;строка\_подключения>: строка подключения к службе хранилища.
+ **--debug**: запуск команды storage в режиме отладки.

## Команды для управления базами данных SQL

Для управлениями вашими базами данных Azure SQL используйте следующие команды.

###Команды для управления серверами SQL Server

Для управлениями вашими серверами SQL Server используйте следующие команды.

**sql server create &lt;имя\_пользователя\_администратора> &lt;пароль\_администратора> &lt;расположение>**

Создает новый сервер базы данных.

	~$ azure sql server create test T3stte$t "West US"
	info:    Executing command sql server create
	+ Creating SQL Server
	data:    Server Name i1qwc540ts
	info:    sql server create command OK

**sql server show &lt;имя>**

Отображает информацию о сервере.

	~$ azure sql server show xclfgcndfg
	info:    Executing command sql server show
	+ Getting SQL server
	data:    SQL Server Name xclfgcndfg
	data:    SQL Server AdministratorLogin msopentechforums
	data:    SQL Server Location West US
	data:    SQL Server FullyQualifiedDomainName xclfgcndfg.database.windows.net
	info:    sql server show command OK

**sql server list**

Возвращает список серверов.

	~$ azure sql server list
	info:    Executing command sql server list
	+ Getting SQL server
	data:    Name        Location
	data:    ----------  --------
	data:    xclfgcndfg  West US
	info:    sql server list command OK

**sql server delete &lt;имя>**

Удаляет сервер.

	~$ azure sql server delete i1qwc540ts
	info:    Executing command sql server delete
	Delete server i1qwc540ts? [y/n] y
	+ Removing SQL Server
	info:    sql server delete command OK

###Команды для управления базами данных SQL

Для управлениями своими базами данных SQL используйте следующие команды.

**sql db create [параметры] &lt;имя\_сервера> &lt;имя\_базы\_данных> &lt;пароль\_администратора>**

Создает новый экземпляр базы данных.

	~$ azure sql db create fr8aelne00 newdb test
	info:    Executing command sql db create
	Administrator password: ********
	+ Creating SQL Server Database
	info:    sql db create command OK

**sql db show [параметры] &lt;имя\_сервера> &lt;имя\_базы\_данных> &lt;пароль\_администратора>**

Отображает информацию о базе данных.

	C:\windows\system32>azure sql db show fr8aelne00 newdb test
	info:    Executing command sql db show
	Administrator password: ********
	+ Getting SQL server databases
	data:    Database _ ContentRootElement=m:properties, id=https://fr8aelne00.datab
	ase.windows.net/v1/ManagementService.svc/Server2('fr8aelne00')/Databases(4), ter
	m=Microsoft.SqlServer.Management.Server.Domain.Database, scheme=http://schemas.m
	icrosoft.com/ado/2007/08/dataservices/scheme, link=[rel=edit, title=Database, hr
	ef=Databases(4), rel=http://schemas.microsoft.com/ado/2007/08/dataservices/relat
	ed/Server, type=application/atom+xml;type=entry, title=Server, href=Databases(4)
	/Server, rel=http://schemas.microsoft.com/ado/2007/08/dataservices/related/Servi
	ceObjective, type=application/atom+xml;type=entry, title=ServiceObjective, href=
	Databases(4)/ServiceObjective, rel=http://schemas.microsoft.com/ado/2007/08/data
	services/related/DatabaseMetrics, type=application/atom+xml;type=entry, title=Da
	tabaseMetrics, href=Databases(4)/DatabaseMetrics, rel=http://schemas.microsoft.c
	om/ado/2007/08/dataservices/related/DatabaseCopies, type=application/atom+xml;ty
	pe=feed, title=DatabaseCopies, href=Databases(4)/DatabaseCopies], title=, update
	d=2013-11-18T19:48:27Z, name=
	data:    Database Id 4
	data:    Database Name newdb
	data:    Database ServiceObjectiveId 910b4fcb-8a29-4c3e-958f-f7ba794388b2
	data:    Database AssignedServiceObjectiveId 910b4fcb-8a29-4c3e-958f-f7ba794388b2
	data:    Database ServiceObjectiveAssignmentState 1
	data:    Database ServiceObjectiveAssignmentStateDescription Complete
	data:    Database ServiceObjectiveAssignmentErrorCode
	data:    Database ServiceObjectiveAssignmentErrorDescription
	data:    Database ServiceObjectiveAssignmentSuccessDate
	data:    Database Edition Web
	data:    Database MaxSizeGB 1
	data:    Database MaxSizeBytes 1073741824
	data:    Database CollationName SQL_Latin1_General_CP1_CI_AS
	data:    Database CreationDate
	data:    Database RecoveryPeriodStartDate
	data:    Database IsSystemObject
	data:    Database Status 1
	data:    Database IsFederationRoot
	data:    Database SizeMB -1
	data:    Database IsRecursiveTriggersOn
	data:    Database IsReadOnly
	data:    Database IsFederationMember
	data:    Database IsQueryStoreOn
	data:    Database IsQueryStoreReadOnly
	data:    Database QueryStoreMaxSizeMB
	data:    Database QueryStoreFlushPeriodSeconds
	data:    Database QueryStoreIntervalLengthMinutes
	data:    Database QueryStoreClearAll
	data:    Database QueryStoreStaleQueryThresholdDays
	info:    sql db show command OK

**sql db list [параметры] &lt;имя\_сервера> &lt;пароль\_администратора>**

Выводит список баз данных.

	~$ azure sql db list fr8aelne00 test
	info:    Executing command sql db list
	Administrator password: ********
	+ Getting SQL server databases
	data:    Name    Edition  Collation                     MaxSizeInGB
	data:    ------  -------  ----------------------------  -----------
	data:    master  Web      SQL_Latin1_General_CP1_CI_AS  5
	info:    sql db list command OK

**sql db delete [параметры] &lt;имя\_сервера> &lt;имя\_базы\_данных> &lt;пароль\_администратора>**

Удаляет базу данных.

	~$ azure sql db delete fr8aelne00 newdb test
	info:    Executing command sql db delete
	Administrator password: ********
	Delete database newdb? [y/n] y
	+ Getting SQL server databases
	+ Removing database
	info:    sql db delete command OK

###Команды для управления правилами брандмауэра SQL Server

Для управления правилами брандмауэра SQL Server используйте следующие команды:

**sql firewallrule create [параметры] &lt;имя\_сервера> &lt;имя\_правила> &lt;начальный\_IP-адрес> &lt;конечный\_IP-адрес>**

Создает новое правило брандмауэра для SQL Server.

	~$ azure sql firewallrule create fr8aelne00 allowed 131.107.0.0 131.107.255.255
	info:    Executing command sql firewallrule create
	+ Creating Firewall Rule
	info:    sql firewallrule create command OK

**sql firewallrule show [параметры] &lt;имя\_сервера> &lt;имя\_правила>**

Отображает информацию о правиле брандмауэра.

	~$ azure sql firewallrule show fr8aelne00 allowed
	info:    Executing command sql firewallrule show
	+ Getting firewall rule
	data:    Firewall rule Name allowed
	data:    Firewall rule Type Microsoft.SqlAzure.FirewallRule
	data:    Firewall rule State Normal
	data:    Firewall rule SelfLink https://management.core.windows.net/9e672699-105
	5-41ae-9c36-e85152f2e352/services/sqlservers/servers/fr8aelne00/firewallrules/allowed
	data:    Firewall rule ParentLink https://management.core.windows.net/9e672699-1
	055-41ae-9c36-e85152f2e352/services/sqlservers/servers/fr8aelne00
	data:    Firewall rule StartIPAddress 131.107.0.0
	data:    Firewall rule EndIPAddress 131.107.255.255
	info:    sql firewallrule show command OK

**sql firewallrule list [параметры] &lt;имя\_сервера>**

Выводит список правил брандмауэра.

	~$ azure sql firewallrule list fr8aelne00
	info:    Executing command sql firewallrule list
	\data:    Name     Start IP address  End IP address
	data:    -------  ----------------  ---------------
	data:    allowed  131.107.0.0       131.107.255.255
	+
	info:    sql firewallrule list command OK

**sql firewallrule delete [параметры] &lt;имя\_сервера> &lt;имя\_правила>**

Эта команда удаляет правило брандмауэра.

	~$ azure sql firewallrule delete fr8aelne00 allowed
	info:    Executing command sql firewallrule delete
	Delete rule allowed? [y/n] y
	+ Removing firewall rule
	info:    sql firewallrule delete command OK

## Команды для управления виртуальными сетями

Для управления виртуальными сетями используйте следующие команды:

**network vnet create [параметры] &lt;расположение>**

Создает новую виртуальную сеть.

	~$ azure network vnet create vnet1 --location "West US" -v
	info:    Executing command network vnet create
	info:    Using default address space start IP: 10.0.0.0
	info:    Using default address space cidr: 8
	info:    Using default subnet start IP: 10.0.0.0
	info:    Using default subnet cidr: 11
	verbose: Address Space [Starting IP/CIDR (Max VM Count)]: 10.0.0.0/8 (16777216)
	verbose: Subnet [Starting IP/CIDR (Max VM Count)]: 10.0.0.0/11 (2097152)
	verbose: Fetching Network Configuration
	verbose: Fetching or creating affinity group
	verbose: Fetching Affinity Groups
	verbose: Fetching Locations
	verbose: Creating new affinity group AG1
	info:    Using affinity group AG1
	verbose: Updating Network Configuration
	info:    network vnet create command OK

**network vnet show &lt;имя>**

Отображает информацию о виртуальной сети.

	~$ azure network vnet show vnet1
	info:    Executing command network vnet show
	+ Fetching Virtual Networks
	data:    Name "vnet1"
	data:    Id "25786fbe-08e8-4e7e-b1de-b98b7e586c7a"
	data:    AffinityGroup "AG1"
	data:    State "Created"
	data:    AddressSpace AddressPrefixes 0 "10.0.0.0/8"
	data:    Subnets 0 Name "subnet-1"
	data:    Subnets 0 AddressPrefix "10.0.0.0/11"
	info:    network vnet show command OK

**vnet list**

Выводит список всех существующих виртуальных сетей.

	~$ azure network vnet list
	info:    Executing command network vnet list
	+ Fetching Virtual Networks
	data:    Name        Status   AffinityGroup
	data:    ----------  -------  -------------
	data:    vnet1      Created  AG1
	data:    vnet2      Created  AG1
	data:    vnet3      Created  AG1
	data:    vnet4      Created  AG1
	info:    network vnet list command OK


**network vnet delete &lt;имя>**

Удаляет указанную виртуальную сеть.

	~$ azure network vnet delete opentechvn1
	info:    Executing command network vnet delete
	+ Fetching Network Configuration
	Delete the virtual network opentechvn1 ?  (y/n) y
	+ Deleting the virtual network opentechvn1
	info:    network vnet delete command OK

**network export [путь\_к\_файлу]**

Для расширенной настройки сети можно экспортировать конфигурацию сети локально. Обратите внимание, что экспортированная конфигурация сети включает параметры DNS-сервера, параметры виртуальной сети, локальные параметры сетевого сайта и другие параметры.

**network import [путь\_к\_файлу]**

Импортирует локальную конфигурацию сети.

**network dnsserver register [параметры] &lt;IP-адрес\_DNS>**

Регистрирует DNS-сервер, который планируется использовать для разрешения имен в конфигурации сети.

	~$ azure network dnsserver register 98.138.253.109 --dns-id FrontEndDnsServer
	info:    Executing command network dnsserver register
	+ Fetching Network Configuration
	+ Updating Network Configuration
	info:    network dnsserver register command OK

**network dnsserver list**

Выводит список DNS-серверов, зарегистрированных в конфигурации сети.

	~$ azure network dnsserver list
	info:    Executing command network dnsserver list
	+ Fetching Network Configuration
	data:    DNS Server ID         DNS Server IP
	data:    --------------------  --------------
	data:    DNS-bb39b4ac34d66a86  44.55.22.11
	data:    FrontEndDnsServer     98.138.253.109
	info:    network dnsserver list command OK

**network dnsserver unregister [параметры] &lt;IP-адрес\_DNS>**

Удаляет запись DNS-сервера из конфигурации сети.

	~$ azure network dnsserver unregister 77.88.99.11
	info:    Executing command network dnsserver unregister
	+ Fetching Network Configuration
	Delete the DNS server entry dns-4 ( 77.88.99.11 ) %s ? (y/n) y
	+ Deleting the DNS server entry dns-4 ( 77.88.99.11 )
	info:    network dnsserver unregister command OK

<!---HONumber=Sept15_HO4-->