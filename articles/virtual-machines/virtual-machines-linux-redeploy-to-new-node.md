<properties 
	pageTitle="Повторное развертывание виртуальных машин Linux | Microsoft Azure" 
	description="В этой статье описывается, как повторно развернуть виртуальные машины Linux для устранения проблем подключения по протоколу SSH." 
	services="virtual-machines-linux" 
	documentationCenter="virtual-machines" 
	authors="iainfoulds" 
	manager="timlt"
	tags="azure-resource-manager,top-support-issue" 
/>
	

<tags 
	ms.service="virtual-machines-linux" 
	ms.devlang="na" 
	ms.topic="support-article" 
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure" 
	ms.date="09/19/2016" 
	ms.author="iainfou" 
/>

# Повторное развертывание виртуальной машины на новом узле Azure

Если у вас возникли сложности при устранении неполадок подключения SSH и получения доступа к приложению на виртуальной машине Azure, можно попробовать повторно развернуть виртуальную машину. При повторном развертывании виртуальная машина перемещается на новый узел в рамках инфраструктуры Azure. Там она снова включается с сохранением всех параметров конфигурации и связанных ресурсов. В этой статье показано, как повторно развернуть виртуальную машину с помощью интерфейса командной строки Azure или портала Azure.

> [AZURE.NOTE] После развертывания временный диск будет удален, а связанные с виртуальным сетевым интерфейсом динамические IP-адреса будут обновлены.


## Использование Azure CLI

На виртуальной машине должен быть [установлен интерфейс командной строки Azure (Azure CLI) последней версии](../xplat-cli-install.md). Его нужно переключить в режим Resource Manager (`azure config mode arm`).

Чтобы повторно развернуть виртуальную машину, выполните следующую команду интерфейса командной строки Azure:

```bash
azure vm redeploy --resourcegroup <resourcegroup> --vm-name <vmname> 
```

Вы можете отслеживать изменение состояния виртуальной машины, которая повторно развертывается. В ходе повторного развертывания виртуальной машины на новом узле значение состояния в свойстве `PowerState` будет меняться с "Работает" на "Выполняется обновление", а затем на "Запуск" и снова на "Работает". Проверьте состояние виртуальных машин в группе ресурсов с помощью следующей команды:

```bash
azure vm list -g <resourcegroup>
```


[AZURE.INCLUDE [virtual-machines-common-redeploy-to-new-node](../../includes/virtual-machines-common-redeploy-to-new-node.md)]


## Дальнейшие действия
При проблемах с подключением к виртуальной машине ознакомьтесь со статьями [Устранение неполадок SSH-подключения к виртуальной машине Azure](virtual-machines-linux-troubleshoot-ssh-connection.md) и [Подробное описание устранения неполадок SSH](virtual-machines-linux-detailed-troubleshoot-ssh-connection.md). При проблемах с доступом к приложению, выполняющемуся в виртуальной машине, ознакомьтесь со статьей [Устранение неполадок доступа к приложению, выполняющемуся в виртуальной машине Azure](virtual-machines-linux-troubleshoot-app-connection.md).

<!---HONumber=AcomDC_0921_2016-->