---
title: "Создание виртуальной сети Azure (классическая модель) с помощью классического портала | Документация Майкрософт"
description: "Узнайте, как создать виртуальную сеть (классическую) с помощью файла netcfg на портале Azure."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 69894a0b-8050-451e-8a25-c513e1bd271e
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/30/2017
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: d365f7137527d60eb509b4f431295de2218ea706
ms.openlocfilehash: 5ff91cccb711d61ed120e4a4e820d6a5dfc5e4a6
ms.contentlocale: ru-ru
ms.lasthandoff: 01/31/2017


---

<a id="create-a-virtual-network-classic-with-a-netcfg-file-using-the-azure-classic-portal" class="xliff"></a>

# Создание виртуальной сети (классической) с помощью файла netcfg на портале Azure
[!INCLUDE [virtual-networks-create-vnet-selectors-classic-include](../../includes/virtual-networks-create-vnet-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

В этой статье описывается, как создать виртуальную сеть на основе классической модели развертывания на классическом портале Azure с помощью файла netcfg. Вы можете также [создать виртуальную сеть на основе классической модели развертывания без использования файла netcfg](virtual-networks-create-vnet-classic-pportal.md) или [создать виртуальную сеть на основе модели развертывания с помощью Azure Resource Manager](virtual-networks-create-vnet-arm-pportal.md) на портале Azure.

[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

<a id="how-to-create-a-vnet-with-a-network-config-file-in-the-microsoft-azure-classic-portal" class="xliff"></a>

## Как создать виртуальную сеть с помощью файла конфигурации сети на классическом портале Microsoft Azure
В Azure для определения всех виртуальных сетей, доступных для подписки, используется файл XML. Вы можете скачать этот файл и изменить его, чтобы создать виртуальные сети с помощью классической модели развертывания, а также чтобы изменить или удалить существующие виртуальные сети. В этой статье объясняется, как скачать этот файл, который называется файлом конфигурации сети (или netcfg), добавить в него виртуальную сеть и передать его для создания виртуальной сети. Ознакомьтесь со [схемой конфигурации виртуальной сети Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx) , чтобы получить дополнительные сведения о файле конфигурации сети.

Чтобы создать виртуальную сеть с помощью файла netcgf на классическом портале Azure, выполните описанные ниже действия.

1. В браузере откройте адрес http://manage.windowsazure.com и войдите с помощью учетной записи Azure.
2. Прокрутите вниз список служб, щелкните **Сети** > **Экспортировать**, как показано на следующем рисунке.

    ![Виртуальные сети Azure](./media/virtual-networks-create-vnet-classic-portal/networks.png)
3. В диалоговом окне **Экспорт конфигурации сети** выберите подписку, из которой нужно экспортировать конфигурацию виртуальной сети, и нажмите кнопку с флажком в правом нижнем углу.
4. Сохраните файл **NetworkConfig.xml** , следуя инструкциям браузера. Запомните, куда вы сохранили файл.
5. Откройте файл, сохраненный при выполнении шага 4, в любом редакторе XML или текстовом редакторе и найдите элемент `<VirtualNetworkSites>` в элементе `<VirtualNetworkConfiguration>`. Каждая имеющаяся виртуальная сеть указана в собственным элементе `<VirtualNetworkSite>`. Если файл не содержит элемент `<VirtualNetworkSites>` в элементе `<VirtualNetworkConfiguration>`, создайте его.
6. Если существующий файл конфигурации сети не содержит виртуальных сетей, то после добавления в него виртуальной сети, описанной в этом сценарии, файл NetworkConfig.xml будет выглядеть следующим образом.

    ```xml
    <NetworkConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
      <VirtualNetworkConfiguration>
        <VirtualNetworkSites>
          <VirtualNetworkSite name="TestVNet" Location="Central US">
            <AddressSpace>
              <AddressPrefix>192.168.0.0/16</AddressPrefix>
            </AddressSpace>
            <Subnets>
              <Subnet name="FrontEnd">
                <AddressPrefix>192.168.1.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="BackEnd">
                <AddressPrefix>192.168.2.0/24</AddressPrefix>
              </Subnet>
            </Subnets>
          </VirtualNetworkSite>
        </VirtualNetworkSites>
      </VirtualNetworkConfiguration>
    </NetworkConfiguration>
    ```
7. Сохраните файл конфигурации сети.
8. На классическом портале Azure щелкните **Создать** > **Сетевых службы** > **Виртуальная сеть** > **Импорт конфигурации**, как показано на следующем рисунке.

    ![ИМПОРТ КОНФИГУРАЦИИ](./media/virtual-networks-create-vnet-classic-portal/import.png)
10. В диалоговом окне **Импорт файла конфигурации сети** щелкните **Обзор файла…** и перейдите в папку, в которой вы сохранили файл на шаге 7, выберите файл и нажмите кнопку **Открыть**, как показано на рисунке ниже.

    ![Страница "Импорт файла конфигурации сети"](./media/virtual-networks-create-vnet-classic-portal/vnet-create-portal-netcfg-figure4.png)

    В правом нижнем углу окна нажмите кнопку со стрелкой, чтобы перейти к следующему шагу.

9. В диалоговом окне **Создание сети** найдите запись новой виртуальной сети, как показано на рисунке ниже.

    ![Страница "Создание сети"](./media/virtual-networks-create-vnet-classic-portal/vnet-create-portal-netcfg-figure5.png)
10. Чтобы создать виртуальную сеть, нажмите кнопку с флажком в нижнем правом углу окна, показанную на предыдущем рисунке. Через несколько секунд виртуальная сеть появится в списке доступных виртуальных сетей, как показано ниже.

    ![Новая виртуальная сеть](./media/virtual-networks-create-vnet-classic-portal/vnet-create-portal-netcfg-figure6.png)

