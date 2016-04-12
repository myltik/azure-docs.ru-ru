<properties 
   pageTitle="Создание групп безопасности сети в режиме ARM с помощью портала Azure | Microsoft Azure"
   description="Узнайте, как создавать и развертывать группы безопасности сети в режиме ARM с помощью портала Azure."
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/04/2016"
   ms.author="telmos" />

# Как управлять сетевыми группами безопасности с помощью портала предварительной версии

[AZURE.INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]В этой статье описывается модель развертывания с использованием менеджера ресурсов. Вы также можете [создавать группы безопасности сети с помощью классической модели развертывания](virtual-networks-create-nsg-classic-ps.md).

[AZURE.INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

Для приведенных ниже примеров команд PowerShell требуется уже созданная простая среда, основанная на приведенном выше сценарии. Чтобы выполнять команды в том виде, в котором они представлены в этом документе, сначала создайте тестовую среду, развернув [этот шаблон](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd), нажмите **Deploy to Azure** (Развернуть в Azure), при необходимости замените значения параметров по умолчанию и следуйте указаниям на портале. В описанных ниже действиях используйте **RG-NSG** в качестве имени группы ресурсов, в которой был развернут шаблон.

## Создание группы безопасности сети NSG-FrontEnd

Чтобы создать группу безопасности сети **NSG-FrontEnd**, как показано в описанном выше сценарии, выполните указанные ниже действия.

1. В браузере откройте страницу http://portal.azure.com и при необходимости войдите в свою учетную запись Azure.
2. Щелкните **Обзор** > **Сетевые группы безопасности**.

    ![Портал Azure — группы безопасности сети](./media/virtual-networks-create-nsg-arm-pportal/figure11.png)

3. В колонке **Сетевые группы безопасности** щелкните **Добавить**.
  
    ![Портал Azure — группы безопасности сети](./media/virtual-networks-create-nsg-arm-pportal/figure12.png)

4. В колонке **Создать группу безопасности сети** создайте группу безопасности *NSG-FrontEnd* в группе ресурсов *RG-NSG*, а затем щелкните **Создать**.

	![Портал Azure — группы безопасности сети](./media/virtual-networks-create-nsg-arm-pportal/figure13.png)

## Создание правил в существующей сетевой группе безопасности

Чтобы создать правила в существующей группе безопасности сети с помощью портала Azure, выполните следующие действия.

2. Щелкните **Обзор** > **Сетевые группы безопасности**.

3. В списке групп выберите **NSG-FrontEnd** > **Правила безопасности для входящего трафика**

	![Портал Azure — NSG-FrontEnd](./media/virtual-networks-create-nsg-arm-pportal/figure2.png)

4. В списке **Правила безопасности для входящего трафика** нажмите **Добавить**.

	![Портал Azure — добавление правила](./media/virtual-networks-create-nsg-arm-pportal/figure3.png)

5. В колонке **Добавление правила безопасности для входящего трафика** создайте правило под названием *web-rule* с приоритетом *200*, которое разрешает доступ по протоколу *TCP* к порту *80* на любой виртуальной машине из любого источника, и нажмите кнопку **ОК**. Обратите внимание, что для большинства этих параметров уже заданы значения по умолчанию.

	![Портал Azure — параметры правила](./media/virtual-networks-create-nsg-arm-pportal/figure4.png)

6. Через несколько секунд новое правило появится в сетевой группе безопасности.

	![Портал Azure — новое правило](./media/virtual-networks-create-nsg-arm-pportal/figure5.png)

7. Повторите действия до шага 6, чтобы создать правило *rdp-rule* с приоритетом *250*, которое разрешает доступ через *TCP*-порт *3389* к любой виртуальной машине из любого источника.

## Связывание группы безопасности сети с подсетью FrontEnd

1. Щелкните **Обзор** > **Группы ресурсов** > **RG-NSG**.
2. В колонке **RG-NSG** щелкните **...** > **TestVNet**.

	![Портал Azure — TestVNet](./media/virtual-networks-create-nsg-arm-pportal/figure14.png)

3. В колонке **Параметры** щелкните **Подсети** > **FrontEnd** > **Группа безопасности сети** > **NSG-FrontEnd**.

	![Портал Azure — параметры подсети](./media/virtual-networks-create-nsg-arm-pportal/figure15.png)

4. В колонке **FrontEnd** нажмите кнопку **Сохранить**.

	![Портал Azure — параметры подсети](./media/virtual-networks-create-nsg-arm-pportal/figure16.png)

## Создание группы безопасности сети NSG-BackEnd

Чтобы создать группу безопасности сети **NSG-BackEnd** и связать ее с подсетью **BackEnd**, выполните следующие действия.

1. Повторите шаги раздела [Создание группы безопасности сети NSG-FrontEnd](#Create-the-NSG-FrontEnd-NSG), чтобы создать группу безопасности сети *NSG-FrontEnd*.
2. Повторите шаги раздела [Создание правил в существующей сетевой группе безопасности](#Create-rules-in-an-existing-NSG), чтобы создать правила **входящих подключений**, приведенные в таблице ниже.

	|Правило входящих подключений|Правило исходящих подключений|
	|---|---|
	|![Портал Azure — правило входящих подключений](./media/virtual-networks-create-nsg-arm-pportal/figure17.png)|![Портал Azure — правило исходящих подключений](./media/virtual-networks-create-nsg-arm-pportal/figure18.png)|

3. Повторите шаги раздела [Связывание группы безопасности сети с подсетью FrontEnd](#Associate-the-NSG-to-the-FrontEnd-subnet), чтобы связать группу безопасности сети **NSG-BackEnd** с подсетью **BackEnd**.

## Дальнейшие действия

- Узнайте, как [управлять существующими группами безопасности сети](virtual-network-manage-nsg-arm-portal.md).
- [Включите ведение журнала](virtual-network-nsg-manage-log.md) для групп безопасности сети.

<!---HONumber=AcomDC_0330_2016-->