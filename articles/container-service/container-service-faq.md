---
title: "Часто задаваемые вопросы о Службе контейнеров Azure | Документация Майкрософт"
description: "Ответы на часто задаваемые вопросы о Службе контейнеров Azure — службе, которая упрощает создание и настройку кластера виртуальных машин, использующегося для запуска контейнерных приложений Docker, а также управления им."
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, контейнеры, микрослужбы, Mesos, Azure, Kubernetes"
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/03/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: c11adbe77131d4e0337ccd926da03041042c3703
ms.lasthandoff: 03/06/2017


---
# <a name="container-service-frequently-asked-questions"></a>Часто задаваемые вопросы о службе контейнеров


## <a name="orchestrators"></a>Оркестраторы

### <a name="which-container-orchestrators-do-you-support-on-azure-container-service"></a>Какие оркестраторы контейнеров поддерживает Служба контейнеров Azure? 

Служба контейнеров Azure поддерживает компоненты DC/OS, Docker Swarm и Kubernetes с открытым кодом. Дополнительные сведения см. в статье [Общие сведения о Службе контейнеров Azure](container-service-intro.md).
 
### <a name="do-you-support-docker-swarm-mode"></a>Поддерживается ли режим Docker Swarm? 

Сейчас режим Swarm не поддерживается, но мы планируем добавить его поддержку в будущем. 

### <a name="does-azure-container-service-support-windows-containers"></a>Поддерживает ли Служба контейнеров Azure контейнеры Windows?  

Сейчас для всех оркестраторов поддерживаются только контейнеры Linux. Поддержка контейнеров Windows в Kubernetes находится в на этапе предварительной версии.

### <a name="do-you-recommend-a-specific-orchestrator-in-azure-container-service"></a>Советуете ли вы использовать конкретный оркестратор в Службе контейнеров Azure? 
Обычно мы не советуем использовать конкретный оркестратор. Если вы уже работали с одним из поддерживаемых оркестраторов, вы можете применить этот опыт в Службе контейнеров Azure. Но на основе тенденций в данных DC/OS эффективно использовать для рабочих нагрузок Интернета вещей и больших данных, Kubernetes подходит для облачных рабочих нагрузок, а Docker Swarm отличается возможностью интеграции со средствами Docker и своей простотой.

В зависимости от сценария использования вы также можете создавать пользовательские решения по работе с контейнерами и управлять ими с помощью других служб Azure. К этим службам относятся [Виртуальные машины](../virtual-machines/virtual-machines-linux-azure-overview.md), [Service Fabric](../service-fabric/service-fabric-overview.md), [веб-приложения](../app-service-web/app-service-web-overview.md) и [пакетная служба](../batch/batch-technical-overview.md).  

### <a name="what-is-the-difference-between-azure-container-service-and-acs-engine"></a>Какова разница между Службой контейнеров Azure и модулем ACS? 
Служба контейнеров Azure — это служба Azure с поддержкой соглашения об уровне обслуживания, различными возможностями на портале Azure, программами командной строки и API-интерфейсами Azure. Она позволяет быстро развертывать кластеры, на которых запущены стандартные средства оркестрации контейнеров, и управлять ими, не требуя при этом сложных конфигураций. 

[Модуль ACS](http://github.com/Azure/acs-engine) — это проект с открытым кодом, который позволяет опытным пользователям настраивать конфигурацию кластера на каждом уровне. Эта возможность изменения конфигурации инфраструктуры и программного обеспечения означает, что мы не предлагаем соглашение об уровне обслуживания для модуля ACS. Поддержка осуществляется через проект с открытым исходным кодом на GitHub, а не через официальные каналы Майкрософт. 

## <a name="cluster-management"></a>Управление кластерами

### <a name="how-do-i-create-ssh-keys-for-my-cluster"></a>Как создавать ключи SSH для кластера?

Чтобы создать для кластера пару ключей SSH RSA (открытый и закрытый), используемых для проверки подлинности виртуальных машин, можно воспользоваться стандартными средствами операционной системы. Пошаговые инструкции см. в руководстве для [OS X, Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md) или [Windows](../virtual-machines/virtual-machines-linux-ssh-from-windows.md). 

Если для развертывания кластера службы контейнеров используются [команды Azure CLI 2.0](container-service-create-acs-cluster-cli.md), то ключи SSH создаются автоматически.

### <a name="how-do-i-create-a-service-principal-for-my-kubernetes-cluster"></a>Как создать субъект-службу для кластера Kubernetes?

Чтобы создать кластер Kubernetes в Службе контейнеров Azure, требуется идентификатор и пароль субъекта-службы Azure Active Directory. Дополнительные сведения см. в статье [о субъекте-службе для кластера Kubernetes](container-service-kubernetes-service-principal.md).


Если для развертывания кластера Kubernetes используются [команды Azure CLI 2.0](container-service-create-acs-cluster-cli.md), то учетные данные субъекта-службы создаются автоматически.


### <a name="how-do-i-increase-the-number-of-masters-after-a-cluster-is-created"></a>Как увеличить количество главных узлов после создания кластера? 
После создания кластера число главных узлов фиксировано. Его изменить нельзя. Во время создания кластера рекомендуется выбрать несколько главных узлов. Это позволит обеспечить высокую доступность.


### <a name="how-do-i-increase-the-number-of-agents-after-a-cluster-is-created"></a>Как увеличить количество агентов после создания кластера? 
Количество агентов в кластере можно изменить с помощью портала или программ командной строки Azure. Дополнительные сведения см. в статье [Масштабирование кластера Службы контейнеров Azure](container-service-scale.md).


### <a name="what-are-the-urls-of-my-masters-and-agents"></a>Что такое URL-адреса главных узлов и агентов? 
URL-адреса ресурсов кластера в Службе контейнеров Azure состоят из указанного префикса DNS-имени и имени региона Azure, выбранного для развертывания. Например, полное доменное имя главного узла имеет следующий формат:

``` 
DNSnamePrefix.AzureRegion.cloudapp.azure.net
```

Часто используемые URL-адреса кластера можно просмотреть с помощью портала Azure, обозревателя ресурсов Azure и других средств Azure.

### <a name="how-do-i-tell-which-orchestrator-version-is-running-in-my-cluster"></a>Как узнать, какая версия оркестратора используется в кластере?

* сведения о версии, используемой в DC/OS, см. в [документации по Mesosphere](https://support.mesosphere.com/hc/en-us/articles/207719793-How-to-get-the-DCOS-version-from-the-command-line-);
* в Docker Swarm используется `docker version`;
* в Kubernetes используется `kubectl version`.


### <a name="how-do-i-upgrade-the-orchestrator-after-deployment"></a>Как обновить оркестратор после развертывания?

В настоящее время служба контейнеров Azure не предоставляет средства для обновления версии оркестратора, развернутого в кластере. Если служба контейнеров поддерживает более позднюю версию, можно развернуть новый кластер. Другим вариантом является использование специальных средств оркестратора (если они доступны) для обновления кластера на месте. Для примера можно ознакомиться с документацией об [обновлении DC/OS](https://dcos.io/docs/1.8/administration/upgrading/).
 
### <a name="where-do-i-find-the-ssh-connection-string-to-my-cluster"></a>Где найти строку SSH-подключения к кластеру?

Строку подключения можно просмотреть на портале Azure или с помощью программ командной строки Azure. 

1. На портале перейдите к группе ресурсов, используемой для развертывания кластера.  

2. Щелкните **Обзор**, а затем в разделе **Основные компоненты** щелкните ссылку **Развертывания**. 

3. В колонке **История развертывания** выберите развертывание, имя которого начинается с **microsoft-acs**, а затем следует дата развертывания. Например, microsoft-acs-201701310000.  

4. На странице **Сводка** в разделе **Выходные данные** доступно несколько ссылок кластеров <provided></provided>. **SSHMaster0** предоставляет строку SSH-подключения к первому главному узлу в кластере службы контейнеров. 

Как уже отмечалось, для поиска полного доменного имени главного узла можно также использовать средства Azure. Установите SSH-подключение к главному узлу, используя его полное доменное имя и имя пользователя, указанное при создании кластера. Например:

```bash
ssh userName@masterFQDN –A –p 22 
```

Дополнительные сведения см. в статье [Подключение к кластеру службы контейнеров Azure](container-service-connect.md).




## <a name="next-steps"></a>Дальнейшие действия

* Узнайте больше о [Службе контейнеров Azure](container-service-intro.md).
* Разверните кластер службы контейнеров с помощью [портала](container-service-deployment.md) или [Azure CLI 2.0](container-service-create-acs-cluster-cli.md).
