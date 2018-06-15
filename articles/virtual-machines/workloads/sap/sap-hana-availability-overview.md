---
title: Общие сведения о доступности SAP HANA на виртуальных машинах Azure | Документация Майкрософт
description: Описание операций SAP HANA, выполняемых на собственных виртуальных машинах Azure.
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
ms.openlocfilehash: 7049a4b5159687ab928cda7ddc6b1a35959529ac
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32187163"
---
# <a name="sap-hana-high-availability-for-azure-virtual-machines"></a>Обеспечение высокого уровня доступности для SAP HANA на виртуальных машинах Azure

Вы можете использовать многочисленные возможности Azure для развертывания критически важных баз данных, таких как SAP HANA, на виртуальных машинах Azure. Эта статья содержит рекомендации по организации доступа для экземпляров SAP HANA, размещенных на виртуальных машинах Azure. Здесь описаны несколько сценариев, которые можно реализовать на основе инфраструктуры Azure для повышения уровня доступности SAP HANA в Azure. 

## <a name="prerequisites"></a>предварительным требованиям

В статье предполагается, что вы знакомы с концепцией инфраструктуры как услуги (IaaS) в Azure, т. е. умеете выполнять такие задачи: 

- развертывать виртуальные машины или виртуальные сети на портале Azure или с помощью PowerShell;
- работать с кроссплатформенным интерфейсом командной строки (Azure CLI), в т. ч. использовать шаблоны JSON (нотация объектов JavaScript).

Также предполагается, что вы умеете устанавливать экземпляры SAP HANA, администрировать их и работать с ними. Здесь особенно важно уметь настраивать и использовать репликацию системы HANA, в том числе выполнять резервное копирование и восстановление баз данных SAP HANA.

Вот несколько статей, в которых хорошо объясняется, как использовать SAP HANA в Azure.

- [Краткое руководство по установке одного экземпляра SAP HANA вручную на виртуальных машинах Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [Высокий уровень доступности SAP HANA на виртуальных машинах Azure](sap-hana-high-availability.md)
- [Руководство по резервному копированию SAP HANA на виртуальных машинах Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)

Еще мы рекомендуем ознакомиться со следующими статьями о SAP HANA.

- [High Availability for SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/6d252db7cdd044d19ad85b46e6c294a4.html) (Высокий уровень доступности для SAP HANA)
- [FAQ: High Availability for SAP HANA](https://archive.sap.com/documents/docs/DOC-66702) (Высокий уровень доступности для SAP HANA: вопросы и ответы)
- [How to Perform System Replication for SAP HANA](https://archive.sap.com/documents/docs/DOC-47702) (Как выполнить репликацию системы для SAP HANA)
- [SAP HANA 2.0 SPS 01 What’s New: High Availability](https://blogs.sap.com/2017/05/15/sap-hana-2.0-sps-01-whats-new-high-availability-by-the-sap-hana-academy/) (Новые возможности SAP HANA 2.0 SPS 01: высокий уровень доступности)
- [Network Configuration for SAP HANA System Replication](https://www.sap.com/documents/2016/06/18079a1c-767c-0010-82c7-eda71af511fa.html) (Настройка сети для репликации системы SAP HANA)
- [System replication](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html) (Репликация системы)
- [Service auto-restart](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html) (Автоматический перезапуск службы)
- [Configuring SAP HANA System Replication](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/676844172c2442f0bf6c8b080db05ae7.html) (Настройка репликации системы SAP HANA)

Если вы уже умеете развертывать виртуальные машины в Azure, мы рекомендуем прочитать статью [Управление доступностью виртуальных машин Windows в Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability), прежде чем определить архитектуру доступности в Azure.

## <a name="service-level-agreements-for-azure-components"></a>Соглашения об уровне обслуживания для компонентов Azure

Для разных компонентов Azure, таких как сеть, хранилище и виртуальные машины, предлагаются разные соглашения об уровне обслуживания. Все эти соглашения документированы. Дополнительные сведения см. на странице [Соглашения об уровне обслуживания](https://azure.microsoft.com/support/legal/sla/). 

Для виртуальных машин [здесь](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) описаны два разных соглашения об уровне обслуживания, применяемые для двух разных конфигураций:

- Одна виртуальная машина, у которой диск ОС и все диски с данными размещены в [хранилище Azure класса Premium](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage). Этот вариант гарантирует время работоспособности на уровне 99,9 % в месяц.
- Несколько (не менее двух) виртуальных машин, организованных в [группу доступности Azure](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Этот вариант гарантирует время работоспособности на уровне 99,95 % в месяц.

Сравните свои требования к уровню доступности с теми параметрами, которые предложены в соглашениях об уровне обслуживания для компонентов Azure. По результатам этого анализа выберите такой сценарий SAP HANA, который обеспечит необходимый уровень доступности.

## <a name="next-steps"></a>Дополнительная информация

- [Доступность SAP HANA в пределах одного региона Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)
- [Доступность SAP HANA в разных регионах Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions) 















  


