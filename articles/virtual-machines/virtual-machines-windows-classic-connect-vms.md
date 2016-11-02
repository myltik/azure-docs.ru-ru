<properties
    pageTitle="Подключение виртуальных машин Windows в облачной службе | Microsoft Azure"
    description="Подключение виртуальных машин Windows, созданных с помощью классической модели развертывания, к облачной службе Azure или виртуальной сети."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="cynthn"/>


# <a name="connect-windows-virtual-machines-created-with-the-classic-deployment-model-with-a-virtual-network-or-cloud-service"></a>Подключение виртуальных машин Windows, созданных с помощью классической модели развертывания, к виртуальной сети или облачной службе

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Виртуальные машины Windows, созданные по классической модели развертывания, всегда размещаются в облачной службе. Облачная служба выступает в качестве контейнера и предоставляет уникальное общедоступное DNS-имя, общедоступный IP-адрес и набор конечных точек для доступа к виртуальной машине через Интернет. Облачная служба может находиться в виртуальной сети, но это не обязательно. Вы также можете [подключить виртуальные машины Linux к виртуальной сети или облачной службе](virtual-machines-linux-classic-connect-vms.md).

Если облачная служба не находится в виртуальной сети, она называется *автономной* . Виртуальные машины в изолированной облачной службе могут взаимодействовать с другими виртуальными машинами только с помощью общедоступных DNS-имен других виртуальных машин, при этом трафик проходит через Интернет. Если облачная служба находится в виртуальной сети, виртуальные машины в этой облачной службе могут взаимодействовать со всеми остальными виртуальными машинами в виртуальной сети без отправки трафика через Интернет.

Если разместить виртуальные машины в одной автономной облачной службе, можно использовать балансировку нагрузки и группы доступности. Дополнительные сведения см. в разделах [Балансировка нагрузки виртуальных машин](virtual-machines-windows-load-balance.md) и [Управление доступностью виртуальных машин](virtual-machines-windows-manage-availability.md). Тем не менее, невозможно упорядочить виртуальные машины в подсетях или подключить автономную облачную службу к локальной сети. Ниже приведен пример:

[AZURE.INCLUDE [virtual-machines-common-classic-connect-vms](../../includes/virtual-machines-common-classic-connect-vms.md)]

## <a name="next-steps"></a>Дальнейшие действия

После создания виртуальной машины рекомендуется [добавить диск данных](virtual-machines-windows-classic-attach-disk.md) , чтобы ваши службы и рабочие нагрузки имели расположение для хранения данных. 


<!--HONumber=Oct16_HO2-->


