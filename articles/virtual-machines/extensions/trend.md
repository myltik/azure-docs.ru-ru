---
title: Установка Trend Micro Deep Security на виртуальную машину | Документация Майкрософт
description: В этой статье описывается установка и настройка защиты Trend Micro на виртуальной машине, созданной с помощью классической модели развертывания в Azure.
services: virtual-machines-windows
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: e991b635-f1e2-483f-b7ca-9d53e7c22e2a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: danis
ms.openlocfilehash: 6098d310bcc6fe5df2378688b78277fc7e4b16bc
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33944776"
---
# <a name="how-to-install-and-configure-trend-micro-deep-security-as-a-service-on-a-windows-vm"></a>Установка и настройка Trend Micro Deep Security как услуги на ВМ Windows
[!INCLUDE [virtual-machines-extensions-deprecation-statement](../../../includes/virtual-machines-extensions-deprecation-statement.md)]
В этой статье показывается, как можно установить и настроить Trend Micro Deep Security как услуги на новой или существующей виртуальной машине (ВМ) под управлением Windows Server. Deep Security как услуга включает защиту от вредоносных программ, брандмауэр, систему предотвращения вторжений и мониторинг целостности.

Клиент устанавливается как расширение безопасности с помощью агента виртуальной машины. На новой виртуальной машине следует установить агент Deep Security, так как агент виртуальной машины создается автоматически порталом Azure.

На виртуальной машине, созданной с помощью портала Azure, Azure CLI или PowerShell, может не быть агента виртуальной машины. На существующей виртуальной машине без агента виртуальной машины необходимо сначала скачать и установить этот агент. В данной статье рассматриваются обе ситуации.

При наличии действующей подписки от Trend Micro для локального решения можно использовать ее для защиты виртуальных машин Azure. Если у вас еще нет подписки, можно зарегистрироваться для получения пробной подписки. Дополнительные сведения об этом решении см. в [записи блога Trend Micro, посвященной расширению агента виртуальных машин Microsoft Azure для Deep Security](http://go.microsoft.com/fwlink/p/?LinkId=403945).

## <a name="install-the-deep-security-agent-on-a-new-vm"></a>Установка агента Deep Security Agent на новой виртуальной машине

[Портал Azure](http://portal.azure.com) позволяет установить модуль безопасности Trend Micro при создании виртуальной машины с помощью образа из **Marketplace**. Если создается одна виртуальная машина, то использование портала — это простой способ добавления защиты от Trend Micro.

При использовании образа из **Marketplace** открывается мастер, помогающий установить виртуальную машину. В колонке **Settings** (Параметры) (это третья страница мастера) можно установить модуль безопасности Trend Micro.  Общие указания см. в статье [Создание виртуальной машины под управлением Windows на портале Azure](../windows/classic/tutorial.md).

Перейдя в колонку **Settings** (Параметры) мастера, сделайте следующее.

1. Щелкните **Extensions** (Расширения) и щелкните **Add extension** (Добавить расширение) в следующей области.

   ![Начало добавления расширения][1]

2. Выберите **Deep Security Agent** (Агент Deep Security) в области **New resource** (Новый ресурс). В области агента Deep Security щелкните **Create** (Создать).

   ![Поиск агента Deep Security][2]

3. Введите значения **Tenant Identifier** (Идентификатор клиента) и **Tenant Activation Password** (Пароль активации клиента) для расширения. При необходимости можно ввести значение **Security Policy Identifier** (Идентификатор политики безопасности). Затем щелкните **ОК**, чтобы добавить клиент.

   ![Ввод данных расширения][3]

## <a name="install-the-deep-security-agent-on-an-existing-vm"></a>Установка агента Deep Security Agent на существующей виртуальной машине
Чтобы установить агент на существующей виртуальной машине, требуется следующее.

* Модуль Azure PowerShell версии не ниже 0.8.2, установленный на локальном компьютере. Установленную версию Azure PowerShell можно проверить с помощью команды **Get-Module azure | format-table version**. Указания и ссылку на последнюю версию см. в статье [Установка и настройка Azure PowerShell](/powershell/azure/overview). Войдите в свою подписку Azure с помощью `Add-AzureAccount`.
* Агент ВМ, установленный на конечной виртуальной машине.

Сначала убедитесь, что агент ВМ уже установлен. Укажите имя облачной службы и имя виртуальной машины, а затем выполните следующие команды в командной строке Azure PowerShell уровня администратора. Замените все содержимое внутри кавычек, включая знаки < и >.

    $CSName = "<cloud service name>"
    $VMName = "<virtual machine name>"
    $vm = Get-AzureVM -ServiceName $CSName -Name $VMName
    write-host $vm.VM.ProvisionGuestAgent

Если вы не знаете имя облачной службы и виртуальной машины, запустите командлет **Get-AzureVM** , чтобы отобразить эти сведения для всех виртуальных машин в вашей текущей подписке.

Если команда **write-host** возвращает значение **True**, агент виртуальных машин установлен. Если она возвращает значение **False**, см. инструкции и ссылку для скачивания [во второй части записи блога Azure, посвященной агенту и расширениям виртуальных машин](http://go.microsoft.com/fwlink/p/?LinkId=403947).

Если агент ВМ установлен, выполните следующие команды.

    $Agent = Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA

    Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity –Version $Agent.Version -ExtensionName TrendMicroDSA -VM $vm | Update-AzureVM

## <a name="next-steps"></a>Дополнительная информация
После установки агента потребуется несколько минут для его запуска. Затем необходимо активировать Deep Security на виртуальной машине, чтобы можно было осуществлять управление с помощью Deep Security Manager. Дополнительные указания см. в следующих статьях:

* статья Trend об этом решении, [Мгновенное включение облачной защиты для Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=404101)
* [пример сценария Windows PowerShell](http://go.microsoft.com/fwlink/?LinkId=404100) для настройки виртуальной машины;
* [инструкции](http://go.microsoft.com/fwlink/?LinkId=404099) для данного образца скрипта.

## <a name="additional-resources"></a>Дополнительные ресурсы
[Как войти в виртуальную машину под управлением Windows Server]

[Расширения и компоненты виртуальных машин Azure]

<!-- Image references -->
[1]: ./media/trend/new_vm_Blade3.png
[2]: ./media/trend/find_SecurityAgent.png
[3]: ./media/trend/SecurityAgentDetails.png

<!-- Link references -->
[Как войти в виртуальную машину под управлением Windows Server]:../windows/classic/connect-logon.md
[Расширения и компоненты виртуальных машин Azure]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
