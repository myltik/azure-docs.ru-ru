---
title: Поставщики вычислительных и сетевых ресурсов, а также ресурсов службы хранилища | Microsoft Docs
description: Концептуальный обзор поставщиков вычислительных и сетевых ресурсов, а также ресурсов хранения (CRP, NRP и SRP) для приложений Windows в модели развертывания Azure Resource Manager
services: virtual-machines-windows
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
tags: azure-resource-manager,azure-service-management

ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/19/2015
ms.author: tomfitz

---
# Поставщики вычислительных и сетевых ресурсов, а также ресурсов хранения для приложений Windows в рамках модели развертывания Azure Resource Manager
Включение возможностей для работы с вычислительными и сетевыми ресурсами, а также ресурсами хранения в модели развертывания Azure Resource Manager значительно упростит развертывание сложных приложений, выполняемых в среде IaaS, а также управление ими. Многим приложениям требуется несколько ресурсов, включая виртуальную сеть, учетную запись хранения, виртуальную машину и сетевой интерфейс. Модель развертывания с помощью Azure Resource Manager обеспечивает возможность создания шаблона JSON для развертывания всех ресурсов и управления ими как единым приложением.

[!INCLUDE [virtual-machines-common-compare-deployment-models](../../includes/virtual-machines-common-compare-deployment-models.md)]

<!---HONumber=AcomDC_0824_2016-->