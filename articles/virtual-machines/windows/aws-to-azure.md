---
title: Перемещение виртуальных машин Windows из AWS в Azure | Документация Майкрософт
description: В этой статье описывается перемещение экземпляра виртуальной машины Windows EC2 из Amazon Web Services (AWS) в службу виртуальных машин Azure с помощью Azure PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: cynthn
ms.openlocfilehash: 4b7e794cb08647dde6fe59b6d4b06a9cbfab06e1
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="move-a-windows-vm-from-amazon-web-services-aws-to-azure-using-powershell"></a>Перемещение виртуальной машины Windows из Amazon Web Services (AWS) в Azure с помощью PowerShell

При оценке виртуальных машин Azure для размещения рабочих нагрузок можно экспортировать существующий экземпляр виртуальной машины Windows EC2 из Amazon Web Services (AWS) и передать виртуальный жесткий диск (VHD) в Azure. После передачи VHD вы можете создать на его основе виртуальную машину в Azure. 

В этой статье описывается перемещение одной виртуальной машины из AWS в Azure. Сведения о том, как переместить масштабируемые виртуальные машины из AWS в Azure, см. в статье [Перенос виртуальных машин из Amazon Web Services (AWS) в Azure с помощью Azure Site Recovery](../../site-recovery/site-recovery-migrate-aws-to-azure.md).

## <a name="prepare-the-vm"></a>Подготовка виртуальной машины 
 
В Azure можно передавать как универсальные, так и специализированные виртуальные жесткие диски. Для обоих типов требуется предварительная подготовка виртуальной машины к экспорту из AWS. 

- **Универсальный виртуальный жесткий диск**. С универсального VHD с помощью Sysprep удалены все сведения вашей личной учетной записи. Если вы планируете использовать виртуальный жесткий диск в качестве образа для создания виртуальных машин, то выполните следующие действия: 
 
    * [Подготовьте виртуальную машину Windows](prepare-for-upload-vhd-image.md).  
    * Сделайте виртуальную машину универсальной с помощью Sysprep.  

 
- **Специализированный виртуальный жесткий диск**. На специализированном VHD сохраняются учетные записи пользователей, приложения и другие данные о состоянии исходной виртуальной машины. Если вы планируете использовать виртуальный жесткий диск "как есть" для создания виртуальной машины, то необходимо выполнить следующие действия:  
    * [Подготовьте виртуальный жесткий диск Windows к передаче в Azure](prepare-for-upload-vhd-image.md). **Не выполняйте** подготовку виртуальной машины к использованию с помощью Sysprep. 
    * Удалите все гостевые инструменты и агенты виртуализации, которые установлены на виртуальной машине (т. е. инструменты VMware). 
    * Убедитесь, что виртуальная машина настроена на получение IP-адреса и параметров DNS через DHCP. Таким образом, сервер будет получать IP-адрес в виртуальной сети при запуске.  


## <a name="export-and-download-the-vhd"></a>Экспорт и скачивание VHD-файла 

Экспортируйте экземпляр EC2 на VHD в контейнере Amazon S3. Выполните действия, описанные в документации по Amazon в разделе об [экспорте экземпляра виртуальной машины с помощью импорта и экспорта](http://docs.aws.amazon.com/vm-import/latest/userguide/vmexport.html), и выполните команду [create-instance-export-task](http://docs.aws.amazon.com/cli/latest/reference/ec2/create-instance-export-task.html), чтобы экспортировать экземпляр EC2 в VHD-файл. 

Экспортированный VHD-файл сохраняется в указанном контейнере Amazon S3. Базовый синтаксис для экспорта VHD приведен ниже. Просто замените замещающий текст в <brackets> собственными данными.

```
aws ec2 create-instance-export-task --instance-id <instanceID> --target-environment Microsoft \
  --export-to-s3-task DiskImageFormat=VHD,ContainerFormat=ova,S3Bucket=<bucket>,S3Prefix=<prefix>
```

По завершении экспорта VHD следуйте инструкциям в разделе о [How Do I Download an Object from an S3 Bucket?](http://docs.aws.amazon.com/AmazonS3/latest/user-guide/download-objects.html) (Как скачать объект из контейнера S3?), чтобы скачать VHD-файл из контейнера S3. 

> [!IMPORTANT]
> В AWS взимается плата за передачу данных при скачивании VHD-файла. Дополнительные сведения см. на странице [Цены на Amazon S3](https://aws.amazon.com/s3/pricing/).


## <a name="next-steps"></a>Дополнительная информация

Теперь можно передать VHD в Azure и создать виртуальную машину. 

- Если вы запустили Sysprep на исходной виртуальной машине, чтобы **сделать ее универсальной** перед экспортом, см. статью о [передаче универсального виртуального жесткого диска и его использовании для создания виртуальных машин в Azure](upload-generalized-managed.md)
- Если вы не запускали Sysprep перед экспортом, VHD считается **специализированным**. См. статью [Создание виртуальной машины из специализированного диска](create-vm-specialized.md).

 
