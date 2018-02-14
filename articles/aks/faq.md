---
title: "Вопросы и ответы о Службе контейнеров Azure"
description: "Ответы на распространенные вопросы о Службе контейнеров Azure."
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 2/01/2018
ms.author: nepeters
ms.openlocfilehash: 2b78479c257930669729a7781b3893b3e2064bab
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/03/2018
---
# <a name="frequently-asked-questions-about-azure-container-service-aks"></a>Вопросы и ответы о Службе контейнеров Azure (AKS)

В этой статье представлены ответы на часто задаваемые вопросы о Службе контейнеров Azure.

## <a name="which-azure-regions-will-have-azure-container-service-aks"></a>В каких регионах Azure будет доступна Служба контейнеров Azure? 

- Центральная Канада 
- Восточная Канада 
- Центральный регион США 
- Восток США 
- Юго-Восточная Азия 
- Западная Европа 
- Западный регион США 2 

## <a name="when-will-additional-regions-be-added"></a>Когда будут добавлены другие регионы? 

Другие регионы будут добавлены по мере увеличения спроса.

## <a name="are-security-updates-applied-to-aks-nodes"></a>Применяются ли обновления для системы безопасности к узлам AKS? 

Обновления для системы безопасности ОС применяются к узлам в кластере в соответствии с ночным графиком, но перезапуск не выполняется. При необходимости можно перезапустить узлы с помощью портала или Azure CLI. При обновлении кластера используется последний образ Ubuntu и применяются все обновления системы безопасности (с перезапуском).

## <a name="do-you-recommend-customers-use-acs-or-akss"></a>Какую службу вы рекомендуете использовать клиентам: ACS или AKS? 

Учитывая то, что Служба контейнеров Azure скоро станет общедоступной, мы рекомендуем создавать кластеры для операций подтверждения концепции, разработки и тестирования в этой службе, а рабочие кластеры — в службе ACS для Kubernetes.  

## <a name="when-will-acs-be-deprecated"></a>Когда служба ACS будет считаться устаревшей? 

Служба ACS будет считаться устаревшей, когда служба AKS станет общедоступной. У вас будет 12 месяцев после указанной даты для переноса кластеров в AKS. В течение этих 12 месяцев вы сможете выполнять все операции ACS.

## <a name="does-aks-support-node-autoscaling"></a>Поддерживает ли AKS автомасштабирование узла? 

Автомасштабирование узла не поддерживается, но мы планируем добавить такую возможность. Вы можете просмотреть этот открытый код [реализации автомасштабирования][auto-scaler].

## <a name="why-are-two-resource-groups-created-with-aks"></a>Почему с AKS создаются две группы ресурсов? 

Вторая группа ресурсов создается автоматически, чтобы упростить удаление всех ресурсов, связанных с развертыванием AKS.

## <a name="is-azure-key-vault-integrated-with-aks"></a>Интегрируется ли Azure Key Vault с AKS? 

Нет, не интегрируется, но мы планируем реализовать такую возможность. Тем временем вы можете опробовать следующее решение от [Hexadite][hexadite]. 

<!-- LINKS - external -->
[auto-scaler]: https://github.com/kubernetes/autoscaler
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent  