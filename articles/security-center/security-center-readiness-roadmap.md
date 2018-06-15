---
title: План подготовки к работе с центром безопасности Azure | Документация Майкрософт
description: Этот документ содержит план подготовки для эффективного использования центра безопасности Azure.
services: security-center
documentationcenter: na
author: terrylan
manager: ndicola
editor: ''
ms.assetid: fece670cc-df70-445d-9773-b32cbaba8d4a
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2018
ms.author: yurid
ms.openlocfilehash: 51dd957a94d1e10658678d9123e9cce6183c0ab0
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/03/2018
ms.locfileid: "32776607"
---
# <a name="azure-security-center-readiness-roadmap"></a>План подготовки к работе с центром безопасности Azure
Этот документ содержит план подготовки, который поможет вам приступить к работе с центром безопасности Azure.

## <a name="understanding-security-center"></a>Основные сведения о центре безопасности
Центр безопасности Azure предоставляет возможности унифицированного управления безопасностью и расширенной защиты от угроз для рабочих нагрузок в Azure, в локальной среде и в других облаках. 

Используйте следующие ресурсы для начала работы с центром безопасности.

Статьи
* [Введение в Центр безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
* [Краткое руководство по центру безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-get-started)

Видеоролики
* [Introduction to Azure Security Center](https://azure.microsoft.com/resources/videos/introduction-to-azure-security-center/) (Ознакомительное видео о центре безопасности Azure)
* [Overview of Security Center Prevention, Detection and Response Capabilities](https://azure.microsoft.com/resources/videos/azurecon-2015-new-azure-security-center-helps-you-prevent-detect-and-respond-to-threats/) (Видеообзор возможностей предотвращения, обнаружения и ответных действий центра безопасности)

## <a name="planning-and-operations"></a>Планирование и эксплуатация
Чтобы воспользоваться всеми преимуществами центра безопасности, важно понять, каким образом разные люди или группы в организации используют эту службу. Это позволит обеспечить соответствие требованиям к безопасной эксплуатации, мониторингу, управлению и реагированию на инциденты.

Следующие ресурсы пригодятся вам в процессе планирования и эксплуатации.


Статья
* [Руководство по планированию использования центра безопасности Azure и работе в нем](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

Видео
* [Hybrid cloud workload protection with Security Center](https://mva.microsoft.com/training-courses/hybrid-cloud-workload-protection-with-azure-security-center-18173?l=X4WqTA3jE_1106218965) (Защита рабочей нагрузки гибридного облака с помощью центра безопасности)

### <a name="onboarding-computers-to-security-center"></a>Подключение компьютеров к центру безопасности
Центр безопасности автоматически обнаруживает подписки и рабочие области Azure, не включенные в центр безопасности уровня "Стандартный". Сюда входят подписки Azure, использующие версию центра безопасности уровня "Бесплатный" и рабочие нагрузки без включенного решения безопасности.

Следующие ресурсы пригодятся вам в процессе подключения.

Статья
* [Подключение к центру безопасности Azure уровня "Стандартный" для повышения уровня безопасности](https://docs.microsoft.com/azure/security-center/security-center-onboarding)

Видео
* [Azure Security Center Hybrid - Overview](https://youtu.be/NMa4L_M597k) (Видеообзор гибридных ресурсов центра безопасности Azure)

## <a name="mitigating-security-issues-using-security-center"></a>Устранение проблем безопасности с помощью центра безопасности
Центр безопасности автоматически собирает, анализирует и объединяет данные журналов, поступающие от ресурсов Azure, сети и подключенных решений партнеров, таких как брандмауэры и решения для защиты конечных точек, для выявления реальных угроз и сокращения ложных срабатываний.

Следующие ресурсы помогут вам управлять предупреждениями безопасности и защищать ресурсы.

Статьи    
* [Наблюдение за работоспособностью системы безопасности в Центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-monitoring)
* [Защита виртуальных машин в центре безопасности Azure.](https://docs.microsoft.com/azure/security-center/security-center-virtual-machine-recommendations)
* [Защита сети в центре безопасности Azure.](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)
* [Защита приложений в центре безопасности Azure.](https://docs.microsoft.com/azure/security-center/security-center-application-recommendations)
* [Защита службы SQL Azure и данных в центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-sql-service-recommendations)


Видео   
* [Устранение проблем безопасности с помощью центра безопасности Azure](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Mitigating-Security-Issues-using-Azure-Security-Center)

### <a name="security-center-for-incident-response"></a>Реагирование на инциденты с помощью центра безопасности
Чтобы минимизировать затраты и риски, важно составить план реагирования до обнаружения атаки. Центр безопасности Azure можно использовать на разных этапах реагирования на инциденты.

Ознакомьтесь со следующими ресурсами, что узнать, как центр безопасности можно интегрировать в процесс реагирования на инциденты.

Видеоролики  
* [Azure Security Center in Incident Response](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-in-Incident-Response) (Реагирование на инциденты с помощью центра безопасности)
* [Respond quickly to threats with next-generation security operation, and investigation](https://youtu.be/e8iFCz5RM4g) (Быстрая реакция на угрозы с помощью современных операций и анализа безопасности)

Статьи    
* [Использование центра безопасности Azure для реагирования на инциденты](https://docs.microsoft.com/azure/security-center/security-center-incident-response)
* [Автоматизация реагирования с помощью тренировочных заданий по обеспечению безопасности](https://docs.microsoft.com/azure/security-center/security-center-playbooks)

## <a name="advanced-cloud-defense"></a>Расширенная защита облака

Для виртуальных машин Azure можно использовать дополнительные возможности защиты облака в центре безопасности. Эти возможности включают только JIT-доступ к виртуальной машине и адаптивные элементы управления приложением.

В следующих ресурсах объясняется, как использовать эти возможности в центре безопасности.

Видеоролики  
* [Azure Security Center – Just-in-Time VM Access](https://youtu.be/UOQb2FcdQnU) (Центр безопасности Azure — JIT-доступ к виртуальной машине)
* [Центр безопасности Azure — адаптивные элементы управления приложениями](https://youtu.be/wWWekI1Y9ck)

Статьи    
* [Управление доступом к виртуальным машинам с помощью JIT](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)
* [Адаптивные элементы управления приложениями в центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

## <a name="hands-on-activities"></a>Практические упражнения

* [Security Center hands-on lab](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=78871abf-6f35-4aa0-840f-d801f5cdbd72) (Практические занятия по работе з центром безопасности)
* [Web Application Firewall (WAF) recommendation playbook in Security Center](https://gallery.technet.microsoft.com/ASC-Playbook-Protect-38bd47ff) (Сборник рекомендаций для брандмауэра веб-приложений в центре безопасности)
* [Azure Security Center Playbook: Security Alerts](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046) (Сборник схем для центра безопасности Azure: оповещения безопасности)

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Документация по центру безопасности](https://docs.microsoft.com/azure/security-center/)
* [Security Center REST API Documentation Page](https://msdn.microsoft.com/library/mt704034.aspx) (Документация по REST API центра безопасности)
* [Azure Security Center frequently asked questions (FAQ)](https://docs.microsoft.com/azure/security-center/security-center-faq) (Центр безопасности Azure: часто задаваемые вопросы)
* [Цены на центр безопасности](https://azure.microsoft.com/pricing/details/security-center/)
* [Рекомендации по обеспечению безопасности удостоверений](https://docs.microsoft.com/azure/security/azure-security-identity-management-best-practices)
* [Рекомендации по обеспечению сетевой безопасности](https://docs.microsoft.com/azure/security/azure-security-network-security-best-practices)
* [Рекомендации для PaaS](https://docs.microsoft.com/azure/security/security-paas-deployments)
* [Соответствие требованиям](https://www.microsoft.com/trustcenter/Compliance/Due-Diligence-Checklist)
* [OMS customers can now use Azure Security Center to protect their hybrid cloud workloads](https://blogs.technet.microsoft.com/msoms/2017/09/25/oms-customers-can-now-use-azure-security-center-to-protect-their-hybrid-cloud-workloads/) (Клиенты OMS теперь могут использовать центр безопасности Azure для защиты своих рабочих нагрузок в гибридном облаке)

## <a name="community-resources"></a>Ресурсы сообщества

* [UserVoice центра безопасности](https://feedback.azure.com/forums/347535-azure-security-center)
* [Форум сообщества центра безопасности](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSecurityCenter)



