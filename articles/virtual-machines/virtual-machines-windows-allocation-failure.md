<properties
	pageTitle="Устранение ошибок выделения ресурсов для виртуальных машин Windows | Microsoft Azure"
	description="Устранение ошибок выделения ресурсов при создании, перезагрузке или изменении размера виртуальной машины Windows в Azure."
	services="virtual-machines-windows, azure-resource-manager"
	documentationCenter=""
	authors="jiangchen79"
	manager="felixwu"
	editor=""
	tags="top-support-issue,azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="na"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="support-article"
	ms.date="02/02/2016"
	ms.author="cjiang"/>

# Устранение ошибок выделения ресурсов при создании, перезагрузке или изменении размера виртуальных машин Windows в Azure

При создании виртуальной машины или изменении ее размера, а также при перезагрузке остановленных (освобожденных) виртуальных машин Microsoft Azure выделяет вычислительные ресурсы для вашей подписки. Иногда во время выполнения этих операций могут возникать ошибки, даже если еще не достигнуты ограничения подписки Azure. В этой статье объясняются причины возникновения некоторых распространенных ошибок выделения, а также представлены возможные способы их устранения. Эта информация также может быть полезна при планировании развертывания служб. Вы также можете [устранить ошибки выделения ресурсов при создании, перезапуске или изменении размера виртуальных машин Linux в Azure](virtual-machines-linux-allocation-failure.md).

[AZURE.INCLUDE [virtual-machines-common-allocation-failure](../../includes/virtual-machines-common-allocation-failure.md)]

<!---HONumber=AcomDC_0525_2016-->