<properties
	pageTitle="Эквивалентные команды диспетчера ресурсов и управления службами для операций с виртуальными машинами в Azure CLI для Mac, Linux и Windows"
	description="Показаны эквивалентные команды Azure CLI для создания виртуальных машин Azure и управления ими в режимах диспетчера ресурсов и управления службами."
	services="virtual-machines"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="command-line-interface"
	ms.workload="infrastructure-services"
	ms.date="04/28/2015"
	ms.author="danlep"/>


# Эквивалентные команды диспетчера ресурсов и управления службами для операций с виртуальными машинами в Azure CLI для Mac, Linux и Windows
В этой статье показаны эквивалентные команды интерфейса командной строки Azure (Azure CLI) для создания виртуальных машин Azure и управления ими в режиме управления службами (ASM) и режиме диспетчера ресурсов (ARM). Используйте ее в качестве удобного руководства по переноса сценариев из одной режима команд в другой.

* Если вы не установили Azure CLI и не подключили его к своей подписке, см. разделы [Установка Azure CLI для Mac, Linux и Windows](../xplat-cli-install.md) и [Подключение к подписке Azure из Azure CLI для Mac, Linux и Windows](../xplat-cli-connect.md). Убедитесь, что при использовании команд режима ARM для подключения используется метод входа.

* Чтобы приступить к работе с режимом ARM в режиме команд Azure CLI и переключать режимы команд, см. раздел [Использование интерфейса командной строки Azure совместно с диспетчером ресурсов](xplat-cli-azure-resource-manager.md).

* Чтобы использовать интерактивную справку по командам и параметры, введите `azure <command> <subcommand> --help` или `azure help <command> <subcommand>`.

## Сценарии
Ниже приведены распространенные операций с виртуальными машинами, которые можно выполнять с помощью команд Azure CLI в режиме ASM и ARM. Для многих команд режима ARM необходимо передавать имя существующей группы ресурсов.

> [AZURE.NOTE]Эти примеры не включают в себя операции на основе шаблонов в режиме ARM. Дополнительную информацию см. в разделе [Использование интерфейса командной строки Azure совместно с диспетчером ресурсов](xplat-cli-azure-resource-manager.md).

Сценарий | Режим ASM | Режим ARM
-------------- | ----------- | -------------------------
Создание самой простой виртуальной машины | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure vm quick-create [options] <resource-group> <name> <location> <os-type> <image-urn> <admin-username> <admin-password>`
Создание виртуальной машины Linux | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] <resource-group> <name> <location> -y "Linux"`
Создание виртуальной машины Windows | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] <resource-group> <name> <location> -y "Windows"`
Вывод списка виртуальных машин | `azure  vm list [options]` | `azure  vm list [options] <resource_group>`
Получение информации о виртуальной машине | `azure  vm show [options] <vm_name>` | `azure  vm show [options] <resource_group> <name>`
Запуск виртуальной машины | `azure vm start [options] <name>` | `azure vm start [options] <resource_group> <name>`
Остановка виртуальной машины | `azure vm shutdown [options] <name>` | `azure vm stop [options] <resource_group> <name>`
Перезапуск виртуальной машины | `azure vm restart [options] <vname>` | `azure vm restart [options] <resource_group> <name>`
Удаление виртуальной машины | `azure vm delete [options] <name>` | `azure vm delete [options] <resource_group> <name>`
Запись виртуальной машины | `azure vm capture [options] <name>` | `azure vm capture [options] <resource_group> <name>`
Создание виртуальной машины из образа пользователя | `azure  vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] –q <image-name> <resource-group> <name> <location> <os-type>`
Создание виртуальной машины из специализированного диска | `azure  vm create [options]-d <custom-data-file> <dns-name> [userName] [password]` | `azue  vm create [options] –d <os-disk-vhd> <resource-group> <name> <location> <os-type>`
Добавление диска данных в виртуальную машину | `azure  vm disk attach [options] <vm-name> <disk-image-name>` ИЛИ <br/> `vm disk attach-new [options] <vm-name> <size-in-gb> [blob-url]` | `azure  vm disk attach-new [options] <resource-group> <vm-name> <size-in-gb> [vhd-name]`
Удаление диска данных от виртуальной машины | `azure  vm disk detach [options] <vm-name> <lun>` | `azure  vm disk detach [options] <resource-group> <vm-name> <lun>`
Добавление универсального расширения в виртуальную машину | `azure  vm extension set [options] <vm-name> <extension-name> <publisher-name> <version>` | `azure  vm extension set [options] <resource-group> <vm-name> <name> <publisher-name> <version>`
Добавление в виртуальную машину расширения VM Access | Недоступно | `azure vm reset-access [options] <resource-group> <name>`
Добавление в виртуальную машину расширения Docker | `azure  vm docker create [options] <dns-name> <image> <user-name> [password]` | `azure  vm docker create [options] <resource-group> <name> <location> <os-type>`
Добавление в виртуальную машину расширения Chef | `azure  vm extension get-chef [options] <vm-name>` | Недоступно
Отключение расширения виртуальной машины | `azure  vm extension set [options] –b <vm-name> <extension-name> <publisher-name> <version>` | Недоступно
Удаление расширения виртуальной машины | `azure  vm extension set [options] –u <vm-name> <extension-name> <publisher-name> <version>` | `azure  vm extension set [options] –u <resource-group> <vm-name> <name> <publisher-name> <version>`
Вывод списка расширений виртуальной машины | `azure vm extension list [options]` | `azure  vm extension get [options] <resource-group> <vm-name>`
Вывод списка образов виртуальных машин | `azure vm image list [options]` | `azure vm image list [options] <location> <publisher> [offer] [sku]` ИЛИ <br/> `azure vm image list-publishers [options] <location>` ИЛИ <br/> `azure vm image list-offers [options] <location>` ИЛИ <br/> `azure vm image list-skus [options] <location>`
Отображение образа виртуальной машины | `azure vm image show [options]` | Недоступно


## Дальнейшие действия

* Дополнительную информацию об использовании Azure CLI для работы с ресурсами в режиме ARM см. в разделах [Использование интерфейса командной строки Azure совместно с диспетчером ресурсов](xplat-cli-azure-resource-manager.md) и [Управление доступом на основе ролей с помощью интерфейса командной строки Azure](../role-based-access-control-xplat-cli.md).
* Дополнительные примеры команд CLI см. в разделах [Использование интерфейса командной строки Azure для Mac, Linux и Windows](../virtual-machines-command-line-tools.md) и [Использование Azure CLI для Mac, Linux и Windows совместно с диспетчером ресурсов Azure](azure-cli-arm-commands.md).

<!--HONumber=52-->
 