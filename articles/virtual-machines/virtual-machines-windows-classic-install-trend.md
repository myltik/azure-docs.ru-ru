---
title: Установка Trend Micro Deep Security на виртуальную машину | Microsoft Docs
description: В этой статье описывается установка и настройка защиты Trend Micro на виртуальной машине, созданной с помощью классической модели развертывания в Azure.
services: virtual-machines-windows
documentationcenter: ''
author: iainfoulds
manager: timlt
editor: ''
tags: azure-service-management

ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 08/24/2016
ms.author: iainfou

---
# Установка и настройка Trend Micro Deep Security как услуги на ВМ Windows
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

В этой статье показывается, как можно установить и настроить Trend Micro Deep Security как услуги на новой или существующей виртуальной машине (ВМ) под управлением Windows Server. Deep Security как услуга включает защиту от вредоносных программ, брандмауэр, систему предотвращения вторжений и мониторинг целостности.

Клиент устанавливается как расширение безопасности с помощью агента виртуальной машины. На новой виртуальной машине установите агент виртуальной машины вместе с агентом Deep Security Agent. На существующей виртуальной машине без агента виртуальной машины необходимо сначала скачать и установить этот агент. В данной статье рассматриваются обе ситуации.

При наличии действительной подписки от Trend Micro для локального решения можно использовать ее для защиты виртуальных машин Azure. Если у вас еще нет подписки, можно зарегистрироваться для получения пробной подписки. Дополнительную информацию об этом решении см. в [записи блога Trend Micro, посвященной расширению агента виртуальных машин Microsoft Azure для Deep Security](http://go.microsoft.com/fwlink/p/?LinkId=403945).

## Установка агента Deep Security Agent на новой виртуальной машине
[Классический портал Azure](http://manage.windowsazure.com) позволяет установить агент виртуальной машины и расширение безопасности Trend Micro при создании виртуальной машины в режиме **Из коллекции**. Если создается одна виртуальная машина, то использование портала — это простой способ добавления защиты от Trend Micro.

Параметр **Из коллекции** открывает мастер, помогающий установить виртуальную машину. Последняя страница мастера используется для установки агента ВМ и модуля безопасности Trend Micro. Общие указания см. в статье [Создание виртуальной машины под управлением Windows на классическом портале Azure](virtual-machines-windows-classic-tutorial.md). Дойдя до последней страницы мастера, выполните следующие действия.

1. В разделе **Агент ВМ** установите флажок **Установить агент ВМ**.
2. В разделе **Модули безопасности** установите флажок **Агент Trend Micro Deep Security**.
   
   ![Установка агента ВМ и агента Deep Security](./media/virtual-machines-windows-classic-install-trend/InstallVMAgentandTrend.png)
3. Чтобы создать виртуальную машину, поставьте галочку.

## Установка агента Deep Security Agent на существующей виртуальной машине
Чтобы установить агент на существующей виртуальной Машине, выполните приведенные ниже действия.

* Модуль Azure PowerShell версии не ниже 0.8.2, установленный на локальном компьютере. Установленную версию Azure PowerShell можно проверить с помощью команды **Get-Module azure | format-table version**. Указания и ссылку на последнюю версию см. в разделе [Установка и настройка Azure PowerShell](../powershell-install-configure.md). Войдите в свою подписку Azure с помощью `Add-AzureAccount`.
* Агент ВМ, установленный на конечной виртуальной машине.

Сначала убедитесь, что агент ВМ уже установлен. Укажите имя облачной службы и имя виртуальной машины, а затем выполните следующие команды в командной строке Azure PowerShell уровня администратора. Замените все содержимое внутри кавычек, включая знаки < и >.

    $CSName = "<cloud service name>"
    $VMName = "<virtual machine name>"
    $vm = Get-AzureVM -ServiceName $CSName -Name $VMName
    write-host $vm.VM.ProvisionGuestAgent

Если вы не знаете имя облачной службы и виртуальной машины, запустите командлет **Get-AzureVM**, чтобы отобразить эти сведения для всех виртуальных машин в вашей текущей подписке.

Если команда **write-host** возвращает значение **True**, агент виртуальных машин установлен. Если она возвращает значение **False**, см. инструкции и ссылку для скачивания во [второй части записи блога Azure, посвященной агенту и расширениям виртуальных машин](http://go.microsoft.com/fwlink/p/?LinkId=403947).

Если агент ВМ установлен, выполните следующие команды.

    $Agent = Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA

    Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity –Version $Agent.Version -ExtensionName TrendMicroDSA -VM $vm | Update-AzureVM

## Дальнейшие действия
После установки агента потребуется несколько минут для его запуска. Затем необходимо активировать Deep Security на виртуальной машине, чтобы можно было осуществлять управление с помощью Deep Security Manager. Дополнительные указания см. в следующей документации:

* статья Trend об этом решении, [Мгновенное включение облачной защиты для Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=404101);
* [пример сценария Windows PowerShell](http://go.microsoft.com/fwlink/?LinkId=404100) для настройки виртуальной машины;
* [инструкции](http://go.microsoft.com/fwlink/?LinkId=404099) для данного образца скрипта.

## Дополнительные ресурсы
[Как войти в виртуальную машину под управлением Windows Server]

[Расширения и компоненты виртуальных машин Azure]

<!--Link references-->
[Как войти в виртуальную машину под управлением Windows Server]: virtual-machines-windows-classic-connect-logon.md
[Расширения и компоненты виртуальных машин Azure]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409

<!---HONumber=AcomDC_0831_2016-->