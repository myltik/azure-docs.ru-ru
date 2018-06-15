---
title: Создание головного узла пакета HPC на виртуальной машине Azure | Документация Майкрософт
description: Сведения о том, как использовать портал Azure и модель развертывания Resource Manager для создания головного узла пакета Microsoft HPC 2012 R2 на виртуальной машине Azure.
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager,hpc-pack
ms.assetid: e6a13eaf-9124-47b4-8d75-2bc4672b8f21
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 12/29/2016
ms.author: danlep
ms.openlocfilehash: acd4cd44dd35a5b1755d9456f683076567d62165
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
ms.locfileid: "30915265"
---
# <a name="create-the-head-node-of-an-hpc-pack-cluster-in-an-azure-vm-with-a-marketplace-image"></a>Создание головного узла кластера пакета HPC на виртуальной машине Azure с помощью образа Marketplace
Для создания головного узла кластера HPC с помощью портала Azure используйте [образ виртуальной машины пакета Microsoft HPC 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) из Azure Marketplace. Этот образ виртуальной машины пакета HPC основан на ОС Windows Server 2012 R2 Datacenter с предустановленным пакетом HPC 2012 R2 с обновлением 3. Используйте этот головной узел для экспериментального развертывания пакета HPC в Azure. Затем можно добавить к кластеру вычислительные узлы для запуска рабочих нагрузок HPC.

> [!TIP]
> Чтобы развернуть в Azure полный кластер пакета HPC 2012 R2, который включает в себя головной узел и вычислительные узлы, рекомендуется использовать автоматизированный метод. Для этого можно использовать [сценарий развертывания IaaS пакета HPC](classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) и шаблоны Resource Manager, например [кластера пакета HPC для рабочих нагрузок Windows](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/). Шаблоны Resource Manager также доступны для [кластеров пакета Microsoft HPC 2016](https://github.com/MsHpcPack/HPCPack2016/tree/master/newcluster-templates). 
> 
> 

## <a name="planning-considerations"></a>Рекомендации относительно планирования
Как показано на следующей схеме, головной узел должен кластера пакета HPC быть развернут в домене Active Directory в виртуальной сети Azure.

![Головной узел пакета HPC][headnode]

* **Домен Active Directory** — перед запуском служб HPC головной узел пакета HPC 2012 R2 должен быть присоединен к домену Active Directory в Azure на виртуальной машине. Как показано в этой статье, для экспериментального развертывания перед запуском служб HPC можно повысить уровень виртуальной машины, создаваемой для головного узла, до контроллера домена. Другой вариант — развертывание отдельного контроллера домена и леса в Azure, к которому можно присоединить виртуальную машину головного узла.

* **Модель развертывания** — для большинства новых развертываний корпорация Майкрософт рекомендует использовать модель развертывания с помощью Resource Manager. В этой статье предполагается, что вы используете эту модель развертывания.

* **Виртуальная сеть Azure** — при использовании модели развертывания с помощью Resource Manager для развертывания головного узла следует указать или создать виртуальную сеть Azure. Виртуальная сеть используется в том случае, если необходимо присоединить головной узел к существующему домену Active Directory. Кроме того, она потребуются позднее для добавления виртуальных машин вычислительных узлов в кластер.

## <a name="steps-to-create-the-head-node"></a>Шаги по созданию головного узла
Ниже в общих чертах описывается, как с помощью портала Azure создать виртуальную машину Azure для головного узла пакета HPC, используя модель развертывания с помощью Resource Manager. 

1. Если вы хотите создать новый лес Active Directory в Azure с отдельными виртуальными машинами контроллера домена, одним из вариантов является использование [шаблона Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/active-directory-new-domain-ha-2-dc). Для простого подтверждающего концепцию развертывания этот шаг можно пропустить и настроить саму виртуальную машину головного узла в качестве контроллера домена. Этот вариант описан далее в этой статье.
2. На странице [HPC Pack 2012 R2 on Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) (Пакет HPC 2012 R2 на основе Windows Server 2012 R2) в Azure Marketplace щелкните **Создать виртуальную машину**. 
3. На портале на странице **HPC Pack 2012 R2 on Windows Server 2012 R2** (Пакет HPC 2012 R2 на основе Windows Server 2012 R2) выберите модель развертывания **Resource Manager**, а затем нажмите кнопку **Создать**.
   
    ![Образ пакета HPC][marketplace]
4. Используйте портал для указания настроек и создания виртуальной машины. Если вы новичок в Azure, то ознакомьтесь с подробными инструкциями в руководстве [Создание виртуальной машины Windows на портале Azure](../virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Для подтверждающего концепцию развертывания большинство параметров можно оставить без изменения, приняв значения по умолчанию или рекомендуемые значения.
   
   > [!NOTE]
   > Если вы хотите присоединить головной узел к существующему домену Active Directory в Azure, то при создании виртуальной машины укажите виртуальную сеть для этого домена.
   > 
   > 
5. После создания и запуска виртуальной машины [подключитесь к ней](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) через протокол удаленного рабочего стола. 
6. Присоедините виртуальную машину к лесу домена Active Directory, выбрав один из следующих вариантов.
   
   * В случае, если виртуальная машина создана в виртуальной сети Azure с существующим доменным лесом, используйте стандартный диспетчер сервера или инструменты Windows PowerShell для присоединения виртуальной машины к лесу. Выполните перезапуск.
   * Если вы создали виртуальную машину в новой виртуальной сети без доменного леса, повысьте эту виртуальную машину до контроллера домена. Следуйте стандартной процедуре установки и настройки роли доменных служб Active Directory на головном узле. Подробные инструкции см. в разделе [Установка нового леса Active Directory в Windows Server 2012](https://technet.microsoft.com/library/jj574166.aspx).
7. Когда виртуальная машина будет запущена и присоединена к лесу Active Directory, запустите службы пакета HPC, как показано ниже.
   
    a. Подключитесь к виртуальной машине головного узла, используя учетную запись домена, которая является участником локальной группы администраторов. Например, можно использовать учетную запись администратора, настроенную при создании виртуальной машины головного узла.
   
    Б. Для использования конфигурации головного узла по умолчанию запустите Windows PowerShell от имени администратора и введите следующую команду:
   
    ```PowerShell
    & $env:CCP_HOME\bin\HPCHNPrepare.ps1 –DBServerInstance ".\ComputeCluster"
    ```
   
    Запуск служб пакета HPC может занять несколько минут.
   
    Для вывода сведений о дополнительных параметрах конфигурации головного узла введите `get-help HPCHNPrepare.ps1`.

## <a name="next-steps"></a>Дополнительная информация
* Теперь вы можете работать с головным узлом кластера пакета HPC. Например, запустите диспетчер кластеров HPC и завершите [список дел развертывания](https://technet.microsoft.com/library/jj884141.aspx).
* Если требуется увеличивать вычислительную мощность кластера по запросу, добавьте [расширительные узлы Azure](classic/hpcpack-cluster-node-burst.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) в облачную службу. 
* Попробуйте запустить рабочую нагрузку в кластере. См. пример в [руководстве по началу работы](https://technet.microsoft.com/library/jj884144) с пакетом HPC.

<!--Image references-->
[headnode]: ./media/hpcpack-cluster-headnode/headnode.png
[marketplace]: ./media/hpcpack-cluster-headnode/marketplace.png
