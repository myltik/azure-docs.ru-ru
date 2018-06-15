---
title: Создание виртуальной сети | Шаблон Azure Resource Manager | Документация Майкрософт
description: Узнайте, как создать виртуальную сеть с использованием шаблона Azure Resource Manager.
services: virtual-network
documentationcenter: ''
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 69530861-2f97-4a6e-b336-a7baf2690044
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bb096f64a6bc41ad2e75c058c7a9f00bbe480207
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/13/2018
ms.locfileid: "29880474"
---
# <a name="create-a-virtual-network-using-an-azure-resource-manager-template"></a>Создание виртуальной сети с использованием шаблона Azure Resource Manager

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

Azure предоставляет две модели развертывания: с помощью Azure Resource Manager и классическую. Для создания ресурсов корпорация Майкрософт рекомендует использовать модель развертывания с помощью Resource Manager. Дополнительные сведения о различиях между двумя моделями см. в статье [Azure Resource Manager vs. classic deployment: Understand deployment models and the state of your resources](../azure-resource-manager/resource-manager-deployment-model.md) (Azure Resource Manager и классическое развертывание. Общие сведения о моделях развертывания и состоянии ресурсов).
 
В этой статье описывается создание виртуальной сети с помощью модели развертывания Resource Manager с использованием шаблона Azure Resource Manager. Виртуальную сеть также можно создать с помощью Resource Manager, используя другие инструменты, либо с помощью классической модели развертывания, выбрав другой вариант из следующего списка:

> [!div class="op_single_selector"]
- [Портал](quick-create-portal.md)
- [PowerShell](quick-create-powershell.md)
- [ИНТЕРФЕЙС КОМАНДНОЙ СТРОКИ](quick-create-cli.md)
- [Шаблон](virtual-networks-create-vnet-arm-template-click.md)
- [Портал (классический)](virtual-networks-create-vnet-classic-pportal.md)
- [PowerShell (классическая модель)](virtual-networks-create-vnet-classic-netcfg-ps.md)
- [Интерфейс командной строки (классическая модель)](virtual-networks-create-vnet-classic-cli.md)

Узнайте, как скачивать и изменять существующий шаблон Azure Resource Manager из GitHub, а также развертывать шаблон с помощью GitHub, PowerShell и Azure CLI.

Если вы развертываете шаблон ARM непосредственно из GitHub без изменений, перейдите к [соответствующему разделу](#deploy-the-arm-template-by-using-click-to-deploy).

[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

## <a name="download-and-understand-the-azure-resource-manager-template"></a>Скачивание и использование шаблона диспетчера ресурсов Azure
Вы можете скачать уже имеющийся шаблон для создания виртуальной сети и двух подсетей из GitHub, внести в него желаемые изменения и применить. Для этого сделайте следующее:

1. Перейдите к [странице примера шаблона](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets).
2. Щелкните **azuredeploy.json** и нажмите кнопку **RAW**.
3. Сохраните файл в локальную папку на своем компьютере.
4. Если вы знакомы с шаблонами, перейдите к шагу 7.
5. Откройте только что сохраненный файл и просмотрите содержимое раздела **parameters** в строке 5. В параметрах шаблона ARM есть заполнитель для значений, которые могут подставляться во время развертывания.
   
   | Параметр | ОПИСАНИЕ |
   | --- | --- |
   | **vnetName** |Имя для новой виртуальной сети |
   | **addressPrefix** |Адресное пространство виртуальной сети в формате CIDR |
   | **subnet1Name** |Имя первой виртуальной сети |
   | **subnet1Prefix** |Блок CIDR для первой подсети |
   | **subnet2Name** |Имя второй виртуальной сети |
   | **subnet2Prefix** |Блок CIDR для второй подсети |
   
   > [!IMPORTANT]
   > Шаблоны ARM, хранящиеся в GitHub, со временем могут изменяться. Перед использованием шаблона обязательно его проверьте.
   > 
   > 
6. Проверьте содержимое раздела **resources** и обратите внимание на следующие параметры:
   
   * **type**. Тип ресурса, созданного на основе шаблона. В данном случае это тип **Microsoft.Network/virtualNetworks**, который представляют виртуальную сеть.
   * **name**. Имя ресурса. Обратите внимание на фрагмент кода **[parameters('vnetName')]**, означающий, что имя предоставляется пользователем или в файле параметров в процессе развертывания.
   * **properties**. Список свойств для ресурса. При создании виртуальной сети в этом шаблоне используется адресное пространство и свойства подсети.
7. Вернитесь на [страницу примера шаблона](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets).
8. Щелкните **azuredeploy paremeters.json** и нажмите кнопку **RAW**.
9. Сохраните файл в локальную папку на своем компьютере.
10. Откройте только что сохраненный файл и измените значения параметров. Для развертывания виртуальной сети в этом сценарии используйте следующие значения.

    ```json
        {
          "vnetName": {
              "value": "TestVNet"
          },
          "addressPrefix": {
              "value": "192.168.0.0/16"
          },
          "subnet1Name": {
              "value": "FrontEnd"
          },
          "subnet1Prefix": {
            "value": "192.168.1.0/24"
          },
          "subnet2Name": {
              "value": "BackEnd"
          },
          "subnet2Prefix": {
              "value": "192.168.2.0/24"
          }
        }
    ```

11. Сохраните файл.


## <a name="deploy-the-template-using-powershell"></a>Развертывание шаблона с помощью PowerShell

Чтобы развернуть шаблон, скачанный с помощью PowerShell, сделайте следующее:

1. Установите и настройте Azure PowerShell, выполнив действия, описанные в [этой статье](/powershell/azure/overview).
2. Выполните следующую команду, чтобы создать новую группу ресурсов:

    ```powershell
    New-AzureRmResourceGroup -Name TestRG -Location centralus
    ```

    Эта команда создает группу ресурсов с именем *TestRG* в регионе Azure *Центральная часть США*. Дополнительные сведения о группах ресурсов см. в разделе "Группы ресурсов" [обзора Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

    Ожидаемые выходные данные:

        ResourceGroupName : TestRG
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                            Actions  NotActions
                            =======  ==========
                            *
        ResourceId        : /subscriptions/[Id]/resourceGroups/TestRG

3. Выполните следующую команду, чтобы развернуть новую виртуальную сеть с помощью файлов шаблонов и параметров, которые вы скачали и изменили раньше.

    ```powershell
    New-AzureRmResourceGroupDeployment -Name TestVNetDeployment -ResourceGroupName TestRG `
    -TemplateFile C:\ARM\azuredeploy.json -TemplateParameterFile C:\ARM\azuredeploy-parameters.json
    ```

    Ожидаемые выходные данные:
   
        DeploymentName    : TestVNetDeployment
        ResourceGroupName : TestRG
        ProvisioningState : Succeeded
        Timestamp         : [Date and time]
        Mode              : Incremental
        TemplateLink      :
        Parameters        :
                            Name             Type                       Value
                            ===============  =========================  ==========
                            location         String                     Central US
                            vnetName         String                     TestVNet
                            addressPrefix    String                     192.168.0.0/16
                            subnet1Prefix    String                     192.168.1.0/24
                            subnet1Name      String                     FrontEnd
                            subnet2Prefix    String                     192.168.2.0/24
                            subnet2Name      String                     BackEnd
   
        Outputs           :
4. Чтобы просмотреть свойства новой виртуальной сети, выполните следующую команду:

    ```powershell
    Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
    ```

    Ожидаемые выходные данные:

        Name              : TestVNet
        ResourceGroupName : TestRG
        Location          : centralus
        Id                : /subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag              : W/"[Id]"
        ProvisioningState : Succeeded
        Tags              :
        AddressSpace      : {
                              "AddressPrefixes": [
                                "192.168.0.0/16"
                              ]
                            }
        DhcpOptions       : {
                              "DnsServers": null
                            }
        NetworkInterfaces : null
        Subnets           : [
                              {
                                "Name": "FrontEnd",
                                "Etag": "W/\"[Id]\"",
                                "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                                "AddressPrefix": "192.168.1.0/24",
                                "IpConfigurations": [],
                                "NetworkSecurityGroup": null,
                                "RouteTable": null,
                                "ProvisioningState": "Succeeded"
                              },
                              {
                                "Name": "BackEnd",
                                "Etag": "W/\"[Id]\"",
                                "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
                                "AddressPrefix": "192.168.2.0/24",
                                "IpConfigurations": [],
                                "NetworkSecurityGroup": null,
                                "RouteTable": null,
                                "ProvisioningState": "Succeeded"
                              }
                            ]

## <a name="deploy-the-template-using-click-to-deploy"></a>Развертывание шаблона с помощью кнопки развертывания

Вы можете использовать шаблоны Azure Resource Manager, уже настроенные и загруженные в репозиторий GitHub корпорации Майкрософт и доступные для всех. Эти шаблоны можно развернуть прямо из репозитория GitHub или скачать и внести необходимые изменения. Чтобы развернуть шаблон, создающий виртуальную сеть с двумя подсетями, сделайте следующее:

1. В браузере откройте страницу [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates).
2. Прокрутите список шаблонов вниз и выберите шаблон **101-vnet-two-subnets**. Просмотрите файл **README.md** , который будет выглядеть следующим образом.

    ![Файл README.md в Github](./media/virtual-networks-create-vnet-arm-template-click-include/figure1.png)

3. Нажмите кнопку **Развернуть в Azure**. При необходимости введите учетные данные для входа в Azure. 
4. В колонке **Параметры** введите значения, которые нужно использовать для создания новой виртуальной сети, и нажмите кнопку **ОК**. На следующем рисунке показаны значения для этого сценария.
   
    ![Параметры шаблона ARM](./media/virtual-networks-create-vnet-arm-template-click-include/figure2.png)

5. Выберите параметр **Группа ресурсов** и укажите группу ресурсов, в которую нужно добавить виртуальную сеть, или щелкните **Создать**, чтобы добавить виртуальную сеть в новую группу ресурсов. На следующем рисунке показаны параметры новой группы ресурсов с именем **TestRG**.

    ![Группа ресурсов](./media/virtual-networks-create-vnet-arm-template-click-include/figure3.png)

6. При необходимости измените параметры **Подписка** и **Расположение** для виртуальной сети.
7. Если вы не хотите, чтобы виртуальная сеть отображалась в виде плитки на **начальной панели**, снимите флажок **Закрепить на начальной панели**.
8. Щелкните **Условия**, ознакомьтесь с ними и примите их, нажав кнопку **Купить**. 
9. Щелкните **Создать**, чтобы создать виртуальную сеть.
   
    ![Отправка элемента развертывания в портал предварительной версии](./media/virtual-networks-create-vnet-arm-template-click-include/figure4.png)

10. Когда развертывание будет завершено, на портале Azure щелкните **Все службы** и введите в появившемся поле фильтра *виртуальные сети*. Затем щелкните "Виртуальные сети", чтобы открыть соответствующую колонку. В этой колонке щелкните *TestVNet*. В колонке *TestVNet* выберите **Подсети**, чтобы просмотреть созданные подсети, как показано на следующем рисунке.
    
     ![Создание виртуальной сети в портале предварительной версии](./media/virtual-networks-create-vnet-arm-template-click-include/figure5.png)

## <a name="next-steps"></a>Дополнительная информация

Инструкции по подключению:

- Сведения о подключении виртуальной машины к виртуальной сети см. в статье [Создание первой виртуальной машины Windows на портале Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md) или [Создание виртуальной машины Linux в Azure с помощью портала](../virtual-machines/linux/quick-create-portal.md). Вместо создания виртуальной сети и подсети с помощью действий, описанных в этой статье, виртуальную машину можно подключить к имеющейся виртуальной сети и подсети.
- Сведения об установке подключения между виртуальными сетями см. в статье [Настройка подключения между виртуальными сетями на портале Azure](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).
- Сведения о подключении виртуальной сети к локальной сети с использованием виртуальной частной сети типа "сеть — сеть" или канала ExpressRoute см. в [этой статье](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md) и в статье [Связывание виртуальной сети с каналом ExpressRoute](../expressroute/expressroute-howto-linkvnet-arm.md).
