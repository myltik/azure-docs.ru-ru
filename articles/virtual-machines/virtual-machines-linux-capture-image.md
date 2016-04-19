<properties
	pageTitle="Запись образа виртуальной машины Linux для его использования в качестве шаблона | Microsoft Azure"
	description="Узнайте, как записать образ виртуальной машины Azure под управлением Linux, созданной с помощью диспетчера ресурсов Azure."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/22/2016"
	ms.author="danlep"/>


# Запись образа виртуальной машины Linux для его использования в качестве шаблона диспетчера ресурсов

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-linux-classic-capture-image.md).


В этой статье рассказывается, как с помощью интерфейса командной строки Azure (Azure CLI) записать образ виртуальной машины под управлением Linux, чтобы в дальнейшем использовать его как шаблон диспетчера ресурсов Azure для создания других виртуальных машин. В шаблоне указаны диск операционной системы и диски с данными, присоединенные к виртуальной машине. В нем не указаны ресурсы виртуальной сети, которые необходимы для создания виртуальной машины менеджера ресурсов Azure, поэтому в большинстве случаев их нужно настраивать отдельно перед созданием другой виртуальной машины, которая использует шаблон.

## Перед началом работы

Эти инструкции предполагают, что вы уже создали виртуальную машину Azure с помощью диспетчера ресурсов Azure и настроили операционную систему, в частности присоединили диски с данными, установили приложения и т. п. Если это еще не сделано, см. инструкции по использованию Azure CLI в режиме диспетчера ресурсов Azure:

- [Развертывание виртуальных машин и управление ими с помощью шаблонов диспетчера ресурсов Azure и интерфейса командной строки Azure](virtual-machines-linux-cli-deploy-templates.md)

Например, создайте в центральной части США группу ресурсов с именем *MyResourceGroup*. Затем с помощью команды **azure vm quick-create** разверните в этой группе виртуальную машину под управлением Ubuntu 14.04 LTS.

 	azure vm quick-create -g MyResourceGroup -n <your-virtual-machine-name> "centralus" -y Linux -Q canonical:ubuntuserver:14.04.2-LTS:latest -u <your-user-name> -p <your-password>

Когда подготовленная виртуальная машина запустится, присоедините и подключите диск с данными. Инструкции см. [здесь](virtual-machines-linux-add-disk).


## Запись образа виртуальной машины

1. Чтобы записать образ виртуальной машины, подключитесь к ней с помощью SSH-клиента.

2. В окне SSH введите следующую команду. Обратите внимание, что выходные данные служебной программы **waagent** могут незначительно различаться в зависимости от ее версии.

	`sudo waagent -deprovision+user`

	Эта команда попытается очистить систему и сделать ее пригодной для повторной подготовки. В ходе операции выполняются такие задачи:

	- удаляются ключи узла SSH (если в файле конфигурации для Provisioning.RegenerateSshHostKeyPair указано значение «y»);
	- очищается конфигурация имени сервера в /etc/resolv.conf;
	- удаляется пароль пользователя `root` из /etc/shadow (если в файле конфигурации для Provisioning.DeleteRootPassword указано значение «y»);
	- удаляются кэшированные аренды DHCP-клиентов.
	- возвращается имя узла localhost.localdomain;
	- удаляется последняя подготовленная учетная запись пользователя (полученная из /var/lib/waagent) и связанные с ней данные.

	>[AZURE.NOTE] Процедура отзыва приводит к удалению файлов и данных, так как выполняется попытке «обобщить» образ. Эту команду следует выполнять только на той виртуальной машине, образ которой вы хотите записать. Отзыв не гарантирует, что из образа будет удалена вся конфиденциальная информация и что он будет готов к повторному распространению третьим сторонам.

3. Чтобы продолжить, введите **y**. Чтобы запрос на подтверждение не появлялся, добавьте параметр **-force**.

4. Введите **exit**, чтобы закрыть SSH-клиент.

	>[AZURE.NOTE] Далее предполагается, что вы уже [установили Azure CLI](../xplat-cli-install.md) на клиентском компьютере.

5. На клиентском компьютере откройте Azure CLI и войдите в свою подписку Azure. Дополнительные сведения см. в статье [Подключение к среде Azure с использованием интерфейса командной строки Azure](../xplat-cli-connect.md).

6. Активируйте режим диспетчера ресурсов:

	`azure config mode arm`

7. Остановите отозванную виртуальную машину:

	`azure vm stop –g <your-resource-group-name> -n <your-virtual-machine-name>`

8. Обобщите виртуальную машину:

	`azure vm generalize –g <your-resource-group-name> -n <your-virtual-machine-name>`

9. Теперь запишите образ и шаблон локального файла:

	`azure vm capture <your-resource-group-name>  <your-virtual-machine-name> <your-vhd-name-prefix> -t <your-template-file-name.json>`

	Эта команда создает обобщенный образ операционной системы, используя префикс имени VHD, указанный для дисков виртуальной машины. По умолчанию VHD-файлы образа создаются в учетной записи хранения, которую использует исходная виртуальная машина. Параметр **-t** создает локальный шаблона JSON-файла, с помощью которого из образа можно создать новую виртуальную машину.

>[AZURE.TIP] Чтобы найти расположение образа, откройте шаблон JSON-файла. В разделе **storageProfile** в контейнере **system** найдите **URI** **образа**. Например, URI для образа диска ОС может выглядеть так: `https://clixxxxxxxxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/your-prefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`.

## Развертывание новой виртуальной машины из записанного образа
Теперь с помощью образа и шаблона мы создадим новую виртуальную машину под управлением Linux. Мы покажем, как можно создать виртуальную машину в новой виртуальной сети, используя Azure CLI и шаблон JSON-файла, созданного с помощью команды `azure vm capture`.

### Создание сетевых ресурсов

Чтобы использовать шаблон, для новой виртуальной машины сначала необходимо настроить виртуальную сеть и сетевую карту. Для этих ресурсов лучше создать новую группу ресурсов. Выполните приведенные ниже команды, подставив нужные имена ресурсов и указав соответствующий регион Azure (вместе centralus).

	azure group create <your-new-resource-group-name> -l "centralus"

	azure network vnet create <your-new-resource-group-name> <your-vnet-name> -l "centralus"

	azure network vnet subnet create <your-new-resource-group-name> <your-vnet-name> <your-subnet-name>

	azure network public-ip create <your-new-resource-group-name> <your-ip-name> -l "centralus"

	azure network nic create <your-new-resource-group-name> <your-nic-name> -k <your-subnetname> -m <your-vnet-name> -p <your-ip-name> -l "centralus"

Чтобы развернуть виртуальную машину из образа, используя JSON-файл, который был сохранен во время записи образа, вам потребуется идентификатор сетевого адаптера. Получить идентификатор можно с помощью следующей команды:

	azure network nic show <your-new-resource-group-name> <your-nic-name>

В возвращенных данных **идентификатор** будет выглядеть примерно так:

	/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/<your-new-resource-group-name>/providers/Microsoft.Network/networkInterfaces/<your-nic-name>



### Создание развертывания
Теперь из образа и сохраненного шаблона JSON-файла создайте виртуальную машину. Для этого выполните следующую команду:

	azure group deployment create <your-new-resource-group-name> <your-new-deployment-name> -f <your-template-file-name.json>

Вам будет предложено указать имя виртуальной машины, имя и пароль администратора, а также идентификатор созданного ранее сетевого адаптера.

	info:    Executing command group deployment create
	info:    Supply values for the following parameters
	vmName: mynewvm
	adminUserName: myadminuser
	adminPassword: ********
	networkInterfaceId: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resource Groups/mynewrg/providers/Microsoft.Network/networkInterfaces/mynewnic

Если развертывание пройдет успешно, вы увидите примерно такой результат:

	+ Initializing template configurations and parameters
	+ Creating a deployment
	info:    Created template deployment "dlnewdeploy"
	+ Waiting for deployment to complete
	data:    DeploymentName     : mynewdeploy
	data:    ResourceGroupName  : mynewrg
	data:    ProvisioningState  : Succeeded
	data:    Timestamp          : 2015-10-29T16:35:47.3419991Z
	data:    Mode               : Incremental
	data:    Name                Type          Value


	data:    ------------------  ------------  -------------------------------------

	data:    vmName              String        mynewvm


	data:    vmSize              String        Standard_D1


	data:    adminUserName       String        myadminuser


	data:    adminPassword       SecureString  undefined


	data:    networkInterfaceId  String        /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/mynewrg/providers/Microsoft.Network/networkInterfaces/mynewnic
	info:    group deployment create command OK

### Проверка развертывания

Чтобы проверить развертывание и начать использовать новую виртуальную машину, подключитесь к ней с помощью SSH-клиента. Для этого сначала определите IP-адрес созданной виртуальной машины:

	azure network public-ip show <your-new-resource-group-name> <your-ip-name>

Команда возвращает общедоступный IP-адрес. По умолчанию подключение к виртуальной машине Linux по протоколу SSH выполняется через порт 22.

## Создание дополнительных виртуальных машин с помощью шаблона

Развертывание дополнительных виртуальных машин с использованием записанного образа и шаблона выполняется так же, как описано в предыдущем разделе.

* Образ виртуальной машины должен храниться в той же учетной записи хранения, в которой будет размещен виртуальный жесткий диск виртуальной машины.
* Скопируйте шаблон JSON-файла и введите уникальное значение для **URI** каждого виртуального жесткого диска виртуальной машины.
* Создайте сетевой адаптер в той же или другой виртуальной сети.
* С помощью измененного шаблона JSON-файла создайте развертывание в группе ресурсов, в которой вы настраиваете виртуальную сеть.

Если вы хотите, чтобы сеть настроилась автоматически при создании виртуальной машины из образа, используйте [шаблон 101-vm-from-user-image](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) с сайта GitHub. Этот шаблон создает не только виртуальную машину из пользовательского образа, но и необходимую виртуальную сеть, общедоступный IP-адрес и сетевые ресурсы. Пошаговые инструкции по использованию шаблона на портале Azure см. в статье [Создание виртуальной машины из образа с помощью шаблона ARM](http://codeisahighway.com/how-to-create-a-virtual-machine-from-a-custom-image-using-an-arm-template/).

## Команда azure vm create

Обычно шаблон диспетчера ресурсов используют для создания виртуальной машины из образа. Но виртуальную машину можно создать и _принудительно_ — с помощью команды **azure vm create** с параметром **--os-disk-vhd** (**-d**).

Прежде чем пытаться создать виртуальную машину из образа с помощью команды **azure vm create**, сделайте вот что:

1.	Создайте для развертывания новую группу ресурсов или определите существующую.

2.	Создайте для новой виртуальной машины общедоступный IP-адрес и сетевой адаптер. Процедура создания виртуальной сети, общедоступного IP-адреса и сетевого адаптера с помощью CLI описана выше в этой статье. (Команда **azure vm create** может создать также и новый сетевой адаптер, но для этого потребуется указать дополнительные параметры виртуальной сети и подсети.)

3.	Убедитесь, что VHD-файл образа будет скопирован в расположение контейнера BLOB-объектов, в котором нет папок (виртуальных каталогов). По умолчанию записанный образ хранится во вложенных папках в контейнере BLOB-объектов хранилища (URI выглядит примерно так: `https://clixxxxxxxxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/your-prefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`). Сейчас команда **azure vm create** может создать виртуальную машину только с VHD-диска операционной системы, который хранится на верхнем уровне контейнера BLOB-объектов. Например, VHD-файл образа можно скопировать в расположение `https://yourstorage.blob.core.windows.net/vhds/your-prefix-OsDisk.vhd`.

Затем выполните такую команду:

	azure vm create <your-resource-group-name> <your-new-vm-name> eastus Linux -o <your-storage-account-name> -d "https://yourstorage.blob.core.windows.net/vhds/your-prefix-OsDisk.vhd" -z Standard_A1 -u <your-admin-name> -p <your-admin-password> -f <your-nic-name>

Чтобы получить информацию о дополнительных параметрах команды, выполните команду `azure help vm create`.

## Дальнейшие действия

Сведения об управлении виртуальными машинами с помощью интерфейса командной строки см. в статье [Развертывание виртуальных машин и управление ими с помощью шаблонов диспетчера ресурсов Azure и интерфейса командной строки Azure](virtual-machines-linux-cli-deploy-templates.md).

<!---HONumber=AcomDC_0406_2016-->