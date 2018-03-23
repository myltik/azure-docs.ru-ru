---
title: Общие сведения о доступности SAP HANA на виртуальных машинах Azure | Документация Майкрософт
description: Операции SAP HANA на собственных виртуальных машинах Azure
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/05/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9b22f89750234a4715d2b7fd2df6ad8740b1d085
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/08/2018
---
# <a name="sap-hana-high-availability-guide-for-azure-virtual-machines"></a>Руководство по обеспечению высокого уровня доступности SAP HANA на виртуальных машинах Azure
В Azure доступны многочисленные возможности, с помощью которых можно развернуть критически важные базы данных, например SAP HANA, на виртуальных машинах Azure. Этот документ содержит инструкции по обеспечению доступности экземпляров SAP HANA, размещенных на виртуальных машинах Azure. В документе описано несколько сценариев, которые можно реализовать в инфраструктуре Azure, чтобы повысить уровень доступности SAP HANA в Azure. 

## <a name="prerequisites"></a>предварительным требованиям
В руководстве предполагается, что вы знакомы с инфраструктурой как услугой (IaaS) в Azure, т. е. умеете выполнять такие задачи: 

- развертывать виртуальные машины или виртуальные сети на портале Azure или с помощью PowerShell;
- работать с кроссплатформенным интерфейсом командной строки Azure, в т. ч. использовать шаблоны JavaScript Object Notification (JSON).

Кроме того, в этом руководстве предполагается, что вы умеете устанавливать экземпляры SAP HANA, а также администрировать их и работать с ними. Особенно важно, чтобы вы умели настраивать репликацию системы HANA, работать с ней и выполнять такие задачи, как резервное копирование и восстановление баз данных SAP HANA.

Ниже перечислены статьи, в которых вы найдете сведения о SAP HANA в Azure:

- [Краткое руководство по установке одного экземпляра SAP HANA вручную на виртуальных машинах Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [Настройка репликации системы SAP HANA в виртуальные машины Azure](sap-hana-high-availability.md)
- [Руководство по резервному копированию SAP HANA на виртуальных машинах Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)

Ознакомьтесь также с этими статьями, посвященными SAP HANA:

- [High Availability for SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/6d252db7cdd044d19ad85b46e6c294a4.html) (Высокий уровень доступности SAP HANA)
- [FAQ: High Availability for SAP HANA](https://archive.sap.com/documents/docs/DOC-66702) (Высокий уровень доступности SAP HANA: вопросы и ответы)
- [How to Perform System Replication for SAP HANA](https://archive.sap.com/documents/docs/DOC-47702) (Как выполнить репликацию системы для SAP HANA)
- [SAP HANA 2.0 SPS 01 What’s New: High Availability](https://blogs.sap.com/2017/05/15/sap-hana-2.0-sps-01-whats-new-high-availability-by-the-sap-hana-academy/) (Новые возможности SAP HANA 2.0 SPS 01: высокий уровень доступности)
- [Рекомендации по настройке сети для репликации системы SAP HANA](https://www.sap.com/documents/2016/06/18079a1c-767c-0010-82c7-eda71af511fa.html)
- [Репликация системы SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
- [Автоматическая перезагрузка SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html)
- [Configuring SAP HANA System Replication](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/676844172c2442f0bf6c8b080db05ae7.html) (Настройка репликации системы SAP HANA)


Если вы умеете развертывать виртуальные машины в Azure, прежде чем продолжать определение архитектуры доступности в Azure, также рекомендуем прочитать статью [Управление доступностью виртуальных машин Windows в Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability).

## <a name="service-level-agreements-for-different-azure-components"></a>Соглашения об уровне обслуживания для различных компонентов Azure
Для разных компонентов Azure, таких как сеть, хранилище и виртуальные машины, предлагаются разные соглашения об уровне обслуживания. Все они задокументированы и доступны на странице [Соглашения об уровне обслуживания](https://azure.microsoft.com/support/legal/sla/). Из [соглашения об уровне обслуживания для виртуальных машин](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) вы узнаете, что Azure предоставляет два разных соглашения с двумя разными конфигурациями. Соглашения об уровне обслуживания определены так:

- Одна виртуальная машина, использующая [хранилище класса Premium](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage) для диска операционной системы и всех дисков с данными, обеспечивает ежемесячный уровень доступности 99,9 %.
- Несколько виртуальных машин (как минимум две), организованных в [группу доступности Azure](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets), обеспечивают ежемесячный уровень доступности 99,95 %.

Определите свои требования к доступности и сравните их с предоставляемыми возможностями, указанными в соглашениях об уровне обслуживания для компонентов Azure. Затем определите сценарии, которые нужно реализовать для SAP HANA, чтобы обеспечить нужный уровень доступности.

## <a name="next-steps"></a>Дополнительная информация
Ознакомьтесь со следующими документами:

- [SAP HANA Availability within one Azure region](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region) (Доступность SAP HANA в пределах одного региона Azure)
- [SAP HANA Availability across Azure regions](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions) (Доступность SAP HANA в нескольких регионах Azure) 















  


