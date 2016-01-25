<properties 
   pageTitle="Развертывание виртуальной машины со статическим общедоступным IP-адресом на портале предварительной версии в диспетчере ресурсов | Microsoft Azure"
   description="Дополнительные сведения о развертывании виртуальных машин со статическим общедоступным IP-адресом на портале предварительной версии в диспетчере ресурсов"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/08/2015"
   ms.author="telmos" />

# Развертывание виртуальной машины со статическим общедоступным IP-адресом с использованием портала Azure

[AZURE.INCLUDE [virtual-network-deploy-static-pip-arm-selectors-include.md](../../includes/virtual-network-deploy-static-pip-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]Классическая модель развертывания.

[AZURE.INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## Создание виртуальной машины со статическим общедоступным IP-адресом 

Чтобы создать виртуальную машину со статическим общедоступным IP-адресом на портале Azure, выполните следующие действия.

1. В браузере откройте [портал Azure](http://portal.azure.com) и при необходимости войдите в свою учетную запись Azure.
2. В верхнем левом углу портала щелкните **Создать**>>**Вычисления**>**Windows Server 2012 R2 Datacenter**.
3. В списке **Выбор модели развертывания** выберите элемент **Диспетчер ресурсов** и нажмите кнопку **Создать**.
4. В колонке **Основы** введите сведения о виртуальной машине, как показано ниже, и нажмите кнопку **ОК**.

	![Портал Azure — основы](./media/virtual-network-deploy-static-pip-arm-portal/figure1.png)

5. В колонке **Выбор размера** щелкните **A1 стандартный**, как показано ниже, и нажмите кнопку **Выбрать**.

	![Портал Azure — выбор размера](./media/virtual-network-deploy-static-pip-arm-portal/figure2.png)

6. В колонке **Параметры** щелкните **Общедоступный IP-адрес**, затем в колонке **Создать общедоступный IP-адрес** в разделе **Назначения** нажмите кнопку **Статический**, как показано ниже. Затем нажмите **ОК**.

	![Портал Azure — создание общедоступного IP-адреса](./media/virtual-network-deploy-static-pip-arm-portal/figure3.png)

7. В колонке **Параметры** нажмите кнопку **ОК**.
8. Просмотрите колонку **Сводка**, как показано ниже, а затем нажмите **ОК**.

	![Портал Azure — создание общедоступного IP-адреса](./media/virtual-network-deploy-static-pip-arm-portal/figure4.png)

9. Обратите внимание на новую плитку на панели мониторинга.

	![Портал Azure — создание общедоступного IP-адреса](./media/virtual-network-deploy-static-pip-arm-portal/figure5.png)

10. После создания виртуальной машины колонка **Параметры** будет отображаться, как показано ниже

	![Портал Azure — создание общедоступного IP-адреса](./media/virtual-network-deploy-static-pip-arm-portal/figure6.png)

<!---HONumber=AcomDC_0114_2016-->