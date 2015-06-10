<properties
	pageTitle="Включение пользовательских данных в Виртуальные машины Azure"
	description="В этом разделе описывается включение пользовательских данных в виртуальную машину Azure во время создания экземпляра, а также поиск пользовательских данных в Windows или Linux."
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/07/2015"
	ms.author="rasquill"/>


#Включение пользовательских данных в виртуальную машину Azure

Включение скрипта или других данных в виртуальную машину Azure при ее подготовке является очень распространенным сценарием, независимо от используемой операционной системы, Microsoft Windows или Linux. В этом разделе описываются следующие действия:

- включение данных в виртуальную машину Azure при ее подготовке;

- извлечение их для Windows и Linux;

- использование специальных средств, доступных в некоторых системах, для автоматического обнаружения и обработки пользовательских данных.

> [AZURE.NOTE]Этот раздел является продолжением [данной публикации в блоге Azure](http://azure.microsoft.com/blog/2014/04/21/custom-data-and-cloud-init-on-windows-azure/), посвященной этой функции, и его актуальность будет поддерживаться по мере появления новых функциональных возможностей.

## Включение пользовательских данных в виртуальную машину Azure

В настоящее время эта функция поддерживается только в [интерфейсе командной строки Azure](https://github.com/Azure/azure-sdk-tools-xplat). Хотя для команды `azure vm create` можно использовать любой из вариантов, следующий подход демонстрирует самый простой способ.

```
    PASSWORD='AcceptablePassword -- more than 8 chars, a cap, a num, a special'
    VMNAME=mycustomdataubuntu
    USERNAME=username
    VMIMAGE= An image chosen from among those listed by azure vm image list
    azure vm create $VMNAME $VMIMAGE $USERNAME $PASSWORD --location "West US" --json -d ./custom-data.txt -e 22
```


## Использование пользовательских данных в виртуальной машине

+ Если виртуальная машина Azure является виртуальной машиной Windows, то пользовательские данные сохраняются в файле `%SYSTEMDRIVE%\AzureData\CustomData.bin`. Хотя для передачи с локального компьютера на новую виртуальную машину эти данные были зашифрованы с помощью кодировки base64, они автоматически расшифровываются и могут немедленно открываться и использоваться.

   >[AZURE.NOTE]Если такой файл существует, он перезаписывается. В каталоге устанавливается защита **System:Full Control** и **Administrators:Full Control**.

+ Если виртуальная машина Azure является виртуальной машиной Linux, то пользовательский файл данных будет располагаться в следующих двух местах, но данные будут в кодировке base64, поэтому сначала придется их декодировать.

    + В `/var/lib/waagent/ovf-env.xml`
    + В `/var/lib/waagent/CustomData`



## Пакет Cloud-Init в Azure

Если виртуальная машина Azure создана из образа Ubuntu или CoreOS, то с помощью CustomData вы можете отправить файл cloud-config в пакет cloud-init. Если же файл пользовательских данных является скриптом, пакет cloud-init может просто выполнить его.

### Образы облаков Ubuntu

В большинстве образов Azure Linux вы изменяете /etc/waagent.conf, чтобы настроить временный диск ресурсов и файл подкачки. Дополнительную информацию см. в [руководстве пользователя агента Linux Azure](./virtual-machines-linux-agent-user-guide.md).

Однако в образах облаков Ubuntu для настройки диска ресурсов (который также называется "временным") и раздела подкачки необходимо использовать пакет cloud-init. Дополнительные сведения см. на следующей странице на вики-сайте по Ubuntu:

 - [Вики-сайт по Ubuntu: настройка разделов подкачки](http://go.microsoft.com/fwlink/?LinkID=532955&clcid=0x409)


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Дальнейшие действия: использование пакета cloud-init

Дополнительные сведения см. в [документации по cloud-init для Ubuntu](https://help.ubuntu.com/community/CloudInit).

<!--Link references-->
[Справочник по REST API управления добавлением службы роли](http://msdn.microsoft.com/library/azure/jj157186.aspx)

[Интерфейс командной строки Azure](https://github.com/Azure/azure-sdk-tools-xplat)

<!---HONumber=58-->