<properties
   pageTitle="Развертывание нескольких виртуальных машин с помощью Azure CLI | Microsoft Azure"
   description="Узнайте, как выполнить развертывание нескольких виртуальных машин с помощью классической модели развертывания и Azure CL."
   services="virtual-machines"
   documentationCenter="nodejs"
   authors="AlanSt"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

   <tags
   ms.service="virtual-machines"
   ms.devlang="nodejs"
   ms.topic="article"
   ms.tgt_pltfrm="Linux"
   ms.workload="infrastructure-services"
   ms.date="02/20/2015"
   ms.author="alanst"/>

# Развертывание нескольких виртуальных машин с помощью Azure CLI

> [AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]В этой статье описывается процесс создания ресурсов с помощью классической модели развертывания.

В описанном ниже сценарии показано, как настроить развертывание нескольких виртуальных машин c несколькими облачными службами в виртуальной сети с помощью интерфейса командной строки Azure.

На изображении ниже показано, как будет выглядеть развернутая виртуальная сеть по завершении выполнения сценария:

![](./media/virtual-machines-create-multi-vm-deployment-xplat-cli/multi-vm-xplat-cli.png)

Этот сценарий создаст одну виртуальную машину (**servervm**) с двумя подключенными дисками данных в облачной службе **servercs** и две виртуальные машины (**clientvm1 и clientvm2**) в облачной службе **workercs**. Обе облачные службы будут размещены в виртуальной сети **samplevnet**. Кроме того, в облачной службе **servercs** будет настроена конечная точка для внешних подключений.

## Сценарий CLI для выполнения развертывания
Код для настройки относительно прост:

>[AZURE.NOTE]Возможно, вам понадобится задать вместо имен облачных служб servercs и workercs уникальные имена.

    azure network vnet create samplevnet -l "West US"
    azure vm create -l "West US" -w samplevnet -e 10000 -z Small -n servervm servercs b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-ru-ru-30GB azureuser Password@1
    azure vm create -l "West US" -w samplevnet -e 10001 -z Small –n clientvm1 clientcs b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-ru-ru-30GB azureuser Password@1
    azure vm create -l "West US" -w samplevnet -e 10002 -c -z Small -n clientvm2 clientcs b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-ru-ru-30GB azureuser Password@1
    azure vm disk attach-new servervm 100
    azure vm disk attach-new servervm 500
    azure vm endpoint create servervm 443 443 -n https -o tcp

Код для завершения операции:

    azure vm delete -b -q servervm
    azure vm delete -b -q clientvm1
    azure vm delete -b -q clientvm2
    azure network vnet delete -q samplevnet

*Параметр –q отключает интерактивное подтверждение при удалении объектов, а параметр -b выполняет очистку дисков или больших двоичных объектов, связанных с виртуальной машиной.*

## Универсальные формы используемых команд

Для получения дополнительной информации о командах, которые вводятся в командной строке Azure, вы можете воспользоваться параметром –help. Ниже приведены универсальные формы использованных выше команд.

    azure network vnet create -l <Region> <VNet_name>
    azure network vnet delete -q <VNet_name>

    azure vm create -l <Region> -w <Vnet_name> -e <SSH_port> -z <VM_size> -n <VM_name> <Cloud_service_name> <VM_image> <Username> <Password>
    azure vm delete -b -q <VM_name>
    azure vm disk attach-new <VM_name>
    azure vm endpoint create <VM_name> <External_port> <Internal_port> -n <Endpoint_name> -o <TCP/UDP>

## Дальнейшие действия


* [ОС Linux и использование средств с открытым кодом в Azure](virtual-machines-linux-opensource.md)
* [Вход в виртуальную машину под управлением ОС Linux](virtual-machines-linux-how-to-log-on.md)
 

<!---HONumber=Sept15_HO4-->