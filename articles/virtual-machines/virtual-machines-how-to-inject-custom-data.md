<properties
	pageTitle="Включение пользовательских данных в виртуальные машины Azure | Microsoft Azure"
	description="В этом разделе описывается включение пользовательских данных в виртуальную машину Azure во время создания экземпляра, а также поиск пользовательских данных в Windows или Linux."
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management" />

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/14/2015"
	ms.author="rasquill"/>


#Включение пользовательских данных в виртуальную машину Azure

Включение сценария или других данных в виртуальную машину Azure при ее подготовке является очень распространенным сценарием, независимо от используемой операционной системы, Windows или Linux.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]В этой статье описывается процесс создания ресурсов с помощью классической модели развертывания.

В этом разделе описываются следующие действия:

- включение данных в виртуальную машину Azure при ее подготовке;

- извлечение их для Windows и Linux;

- использование специальных средств, доступных в некоторых системах, для автоматического обнаружения и обработки пользовательских данных.

> [AZURE.NOTE]В этой статье рассказывается, как вставить пользовательские данные с помощью виртуальной машины, созданной с помощью API управления службами Azure. Чтобы узнать, как использовать API управления ресурсами Azure, см. [этот пример шаблона](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-customdata).

## Включение пользовательских данных в виртуальную машину Azure

В настоящее время эта функция поддерживается только в [интерфейсе командной строки Azure](https://github.com/Azure/azure-xplat-cli). Хотя для команды `azure vm create` можно использовать любой из вариантов, следующий подход демонстрирует самый простой способ.

```
    PASSWORD='AcceptablePassword -- more than 8 chars, a cap, a num, a special'
    VMNAME=mycustomdataubuntu
    USERNAME=username
    VMIMAGE= An image chosen from among those listed by azure vm image list
    azure vm create $VMNAME $VMIMAGE $USERNAME $PASSWORD --location "West US" --json -d ./custom-data.txt -e 22
```


## Использование пользовательских данных в виртуальной машине

+ Если в виртуальной машине Azure используется платформа Windows, то пользовательские данные сохраняются в файл `%SYSTEMDRIVE%\AzureData\CustomData.bin`. Хотя для передачи с локального компьютера на новую виртуальную машину эти данные были зашифрованы с помощью кодировки base64, они автоматически расшифровываются и могут немедленно открываться и использоваться.

   >[AZURE.NOTE]Если такой файл существует, он перезаписывается. В каталоге устанавливается защита **System:Full Control** и **Administrators:Full Control**.

+ Если в виртуальной машине Azure используется платформа Linux, файл пользовательских данных расположен в следующих двух местах. Данные будут в кодировке Base64, поэтому их необходимо сначала расшифровать.

    + В `/var/lib/waagent/ovf-env.xml`
    + В `/var/lib/waagent/CustomData`



## Cloud-Init в Azure

Если виртуальная машина Azure создана из образа Ubuntu или CoreOS, то с помощью CustomData вы можете отправить файл cloud-config в пакет cloud-init. Если же файл пользовательских данных является сценарием, пакет cloud-init может просто выполнить его.

### Образы облаков Ubuntu

В большинстве образов Azure Linux вы изменяете /etc/waagent.conf, чтобы настроить временный диск ресурсов и файл подкачки. Дополнительную информацию см. в [руководстве пользователя агента Linux Azure](virtual-machines-linux-agent-user-guide.md).

В образах Ubuntu Cloud для настройки диска ресурсов (который также называется временным) и раздела подкачки необходимо использовать пакет cloud-init. Дополнительные сведения см. на следующей странице на вики-сайте по Ubuntu: [AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions).



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Дальнейшие действия: использование пакета cloud-init

Дополнительные сведения см. в [документации по cloud-init для Ubuntu](https://help.ubuntu.com/community/CloudInit).

<!--Link references-->
[Справочник по REST API управления добавлением службы роли](http://msdn.microsoft.com/library/azure/jj157186.aspx)

[Интерфейс командной строки Azure](https://github.com/Azure/azure-sdk-tools-xplat)

<!---HONumber=Sept15_HO4-->