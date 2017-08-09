---
title: "Просмотр предварительных требований для репликации Hyper-V на дополнительный сайт VMM с использованием Azure Site Recovery | Документация Майкрософт"
description: "Здесь описываются предварительные требования для репликации виртуальных машин Hyper-V на дополнительный сайт VMM с помощью Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 21ff0545-8be5-4495-9804-78ab6e24add6
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: fff84ee45818e4699df380e1536f71b2a4003c71
ms.openlocfilehash: 7897a30bf1774609ca8e6037dabcd5fbf4151271
ms.contentlocale: ru-ru
ms.lasthandoff: 08/01/2017

---
# <a name="step-2-review-the-prerequisites-and-limitations-for-hyper-v-vm-replication-to-a-secondary-vmm-site"></a>Шаг 2. Просмотр предварительных требований и ограничений для репликации виртуальных машин Hyper-V на дополнительный сайт VMM


Ознакомившись с [архитектурой сценария](vmm-to-vmm-walkthrough-architecture.md), прочтите эту статью, чтобы убедиться, что вы понимаете предварительные условия для развертывания при репликации локальных виртуальных машин Hyper-V, управляемых в облаках System Center Virtual Machine Manager (VMM), на дополнительный сайт с помощью [Azure Site Recovery](site-recovery-overview.md) на портале Azure.

Любые комментарии, которые возникнут после прочтения этой статьи, можно добавить в конце этой страницы или на [форуме по службам восстановления Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="prerequisites-and-limitations"></a>Предварительные требования и ограничения

**Требование** | **Дополнительные сведения**
--- | ---
**Таблицы Azure** | [Подписка Microsoft Azure](http://azure.microsoft.com/).<br/><br/> Начните с [бесплатной пробной версии](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> [дополнительными сведениями](https://azure.microsoft.com/pricing/details/site-recovery/) о ценах на использование Site Recovery.<br/><br/> Сведения о поддерживаемых регионах для Site Recovery см. в разделе "Географическая доступность" на странице [цен на службу Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
**Серверы VMM** | Мы рекомендуем использовать два сервера VMM — один на основном сайте и один на дополнительном.<br/><br/> Поддерживается репликация между облаками на одном сервере VMM.<br/><br/> На серверах VMM должен выполняться как минимум System Center 2012 с пакетом обновления 1 (SP1) с последними обновлениями.<br/><br/> Серверам VMM нужен доступ к Интернету.
**Облака VMM** | Для каждого сервера VMM должно быть настроено одно или несколько облаков, и для каждого облака должен быть задан профиль емкости Hyper-V. <br/><br/>Облака должны содержать одну или несколько групп узлов VMM.<br/><br/> Если у вас только один сервер VMM, к нему должны быть подключены по меньшей мере два облака, которые будут выполнять роль основного и дополнительного сайтов.
**Hyper-V** | Серверы Hyper-V должны работать под управлением Windows Server 2012 (или более поздних версий), на них должна быть настроена роль Hyper-V и установлены все последние обновления.<br/><br/> Сервер Hyper-V должен содержать одну или несколько виртуальных машин.<br/><br/>  Серверы узлов Hyper-V должны быть размещены в первичном и вторичном облаках VMM.<br/><br/> Если Hyper-V выполняется в кластере на платформе Windows Server 2012 R2, установите [обновление 2961977](https://support.microsoft.com/kb/2961977).<br/><br/> Если Hyper-V выполняется в кластере на платформе Windows Server 2012, учтите, что брокер кластера не создается автоматически при использовании кластера на основе статических IP-адресов. Брокер кластера необходимо настроить вручную. [Подробная информация](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx).<br/><br/> Серверам Hyper-V необходим доступ к Интернету.




## <a name="next-steps"></a>Дальнейшие действия

Перейдите к статье [Шаг 3. Планирование сетей для репликации виртуальной машины Hyper-V на дополнительный сайт VMM](vmm-to-vmm-walkthrough-network.md).

