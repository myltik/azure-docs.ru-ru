<properties 
   pageTitle="Как создавать сетевые группы безопасности в режиме ARM с помощью портала предварительной версии | Microsoft Azure"
   description="Узнайте, как создавать и развертывать сетевые группы безопасности в режиме ARM с помощью портала предварительной версии"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/16/2015"
   ms.author="telmos" />

# Как управлять сетевыми группами безопасности с помощью портала предварительной версии

[AZURE.INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]В этой статье описывается модель развертывания с использованием менеджера ресурсов. Вы также можете [создавать сетевые группы безопасности на основе классической модели развертывания](virtual-networks-create-nsg-classic-ps.md).

[AZURE.INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

## Развертывание шаблона ARM с помощью кнопки развертывания

В настоящее время создавать сетевые группы безопасности с помощью предварительной версии невозможно. Тем не менее вы можете управлять существующими группами безопасности. Прежде чем управлять сетевыми группами безопасности, необходимо использовать образец шаблона, который находится в общедоступном репозитории, чтобы создать ресурсы, описанные в приведенном выше сценарии. Разверните [этот шаблон](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd-NSG), нажмите **Deploy to Azure** (Развернуть в Azure), при необходимости замените значения параметров по умолчанию и следуйте указаниям на портале.

## Создание правил в существующей сетевой группе безопасности

Чтобы создать правила в существующей сетевой группе безопасности с помощью портала предварительной версии, выполните следующие действия.

1. В браузере откройте страницу http://portal.azure.com и при необходимости войдите в свою учетную запись Azure.
2. Нажмите **Обзор>** > **Сетевые группы безопасности**.

![Портал предварительной версии — сетевые группы безопасности](./media/virtual-networks-create-nsg-arm-pportal/figure1.png)

3. В списке групп выберите **NSG-FrontEnd** > **Правила безопасности для входящего трафика**

![Портал предварительной версии — NSG-FrontEnd](./media/virtual-networks-create-nsg-arm-pportal/figure2.png)

4. В списке **Правила безопасности для входящего трафика** нажмите **Добавить**.

![Портал предварительной версии — добавление правила](./media/virtual-networks-create-nsg-arm-pportal/figure3.png)

5. В колонке **Добавление правила безопасности для входящего трафика** создайте правило под названием *web-rule* с приоритетом *200*, которое разрешает доступ по протоколу *TCP* к порту *80* на любой виртуальной машине из любого источника, и нажмите кнопку **ОК**. Обратите внимание, что для большинства этих параметров значения уже заданы по умолчанию.

![Портал предварительной версии — параметры правила](./media/virtual-networks-create-nsg-arm-pportal/figure4.png)

6. Через несколько секунд новое правило появится в сетевой группе безопасности.

![Портал предварительной версии — новое правило](./media/virtual-networks-create-nsg-arm-pportal/figure5.png)

<!---HONumber=Oct15_HO3-->