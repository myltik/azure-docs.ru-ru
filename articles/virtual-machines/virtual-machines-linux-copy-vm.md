---
title: Создание копии виртуальной машины Linux в Azure | Microsoft Docs
description: Узнайте, как создать копию виртуальной машины Linux в Azure, используя модель развертывания с помощью Resource Manager.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: timlt
tags: azure-resource-manager

ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/28/2016
ms.author: cynthn

---
# Создание копии виртуальной машины Linux, работающей в Azure
В этой статье показано, как создать копию виртуальной машины Azure под управлением Linux, используя модель развертывания с помощью Resource Manager. Сначала следует скопировать операционную систему и диски данных в новый контейнер, а затем настроить сетевые ресурсы и создать новую виртуальную машину.

Кроме того, можно [передать пользовательский образ диска и создать виртуальную машину на его основе](virtual-machines-linux-upload-vhd.md).

## Перед началом работы
Перед началом описанной ниже процедуры необходимо выполнить следующие условия.

* Необходимо скачать [интерфейс командной строки Azure (Azure CLI)](../xplat-cli-install.md) и установить его на компьютере.
* Необходимы также некоторые сведения о существующей виртуальной машине Linux в Azure.

| Сведения об исходной виртуальной машине | Как получить |
| --- | --- |
| Имя виртуальной машины |`azure vm list` |
| Имя группы ресурсов |`azure vm list` |
| Расположение |`azure vm list` |
| Имя учетной записи хранения |`azure storage account list -g <resourceGroup>` |
| Имя контейнера |`azure storage container list -a <sourcestorageaccountname>` |
| Имя исходного VHD-файла виртуальной машины |`azure storage blob list --container <containerName>` |

* Необходимо будет выбрать параметры новой виртуальной машины: <br> -имя контейнера; <br> имя виртуальной машины; <br> - размер виртуальной машины; <br> имя виртуальной сети; <br> - имя подсети; <br> - имя IP-адреса; <br> - имя сетевой карты.

## Вход и задание подписки
1. Войдите в интерфейс командной строки.
   
        azure login
2. Убедитесь, что режим Resource Manager включен.
   
        azure config mode arm
3. Задайте соответствующую подписку. Для просмотра всех подписок можно использовать команду azure account list.
   
        azure account set <SubscriptionId>

## Остановка виртуальной машины
Остановите исходную виртуальную машину и отмените ее распределение. Для получения списка всех виртуальных машин в подписке и имен их групп ресурсов можно использовать команду azure vm list.

        azure vm stop <ResourceGroup> <VmName>
        azure vm deallocate <ResourceGroup> <VmName>




## Копирование виртуального жесткого диска
Скопировать виртуальный жесткий диск из исходного хранилища в место назначения можно с помощью команды `azure storage blob copy start`. В этом примере мы скопируем виртуальный жесткий диск в ту же учетную запись хранения, но в другой контейнер.

Чтобы скопировать виртуальный жесткий диск в другой контейнер в той же учетной записи хранения, введите следующую команду.

        azure storage blob copy start https://<sourceStorageAccountName>.blob.core.windows.net:8080/<sourceContainerName>/<SourceVHDFileName.vhd> <newcontainerName>


## Настройка виртуальной сети для новой виртуальной машины
Настройте виртуальную сеть и сетевую карту для новой виртуальной машины.

    azure network vnet create <ResourceGroupName> <VnetName> -l <Location>

    azure network vnet subnet create -a <address.prefix.in.CIDR/format> <ResourceGroupName> <VnetName> <SubnetName>

    azure network public-ip create <ResourceGroupName> <IpName> -l <yourLocation>

    azure network nic create <ResourceGroupName> <NicName> -k <SubnetName> -m <VnetName> -p <IpName> -l <Location>


## Создание виртуальной машины
Теперь можно создать виртуальную машину из переданного виртуального диска, [используя шаблон Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd) или интерфейс командной строки, указав универсальный код ресурса (URI) скопированного диска. Для этого введите следующую команду.

```bash
azure vm create -n <newVMName> -l "<location>" -g <resourceGroup> -f <newNicName> -z "<vmSize>" -d https://<storageAccountName>.blob.core.windows.net/<containerName/<fileName.vhd> -y Linux
```



## Дальнейшие действия
Чтобы узнать, как использовать интерфейс командной строки Azure для управления новой виртуальной машиной, ознакомьтесь со статьей [Команды Azure CLI в режиме Resource Manager](azure-cli-arm-commands.md).

<!---HONumber=AcomDC_0803_2016-->