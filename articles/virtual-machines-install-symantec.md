<properties title="How to install and configure Symantec Endpoint Protection on an Azure VM" pageTitle="How to install and configure Symantec Endpoint Protection on an Azure VM" description="Describes installing and configuring Symantec Endpoint Protection on a VM in Azure" metaKeywords="" services="virtual machines" solutions="" documentationCenter="" authors="kathydav" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-multiple" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav" />

# Установка и настройка Symantec Endpoint Protection на виртуальной машине Azure

В этой статье показывается, как можно установить и настроить клиент Symantec Endpoint Protection на новой или существующей виртуальной машине (ВМ) под управлением Windows Server. Это полный клиент, включающий такие службы, как защита от вирусов и шпионских программ, брандмауэр и система предотвращения вторжений.

Клиент устанавливается как модуль безопасности с помощью агента ВМ. На новой виртуальной машине этот агент будет устанавливаться вместе с клиентом конечной точки. на существующей виртуальной машине без агента необходимо сначала загрузить и установить этот агент. В данной статье рассматриваются обе ситуации.

Если имеется действительная подписка от Symantec, <for an on-premises solution, you can use it to protect your azure virtual machines> Если вы еще не являетесь клиентом, можно зарегистрироваться для получения пробной подписки. Дополнительные сведения об этом решении см. в статье [Symantec Endpoint Protection на платформе Microsoft Azure][Symantec Endpoint Protection на платформе Microsoft Azure]. В этой статье также предоставляются ссылки на сведения о лицензировании и другие инструкции по установке клиента для тех, кто уже является клиентом Symantec.

## Установка Symantec Endpoint Protection на новой виртуальной машине

[Портал управления Azure][Портал управления Azure] позволяет установить агент ВМ и модуль безопасности Symantec при использовании параметра **Из коллекции** для создания виртуальной машины. Это простейший способ добавления защиты от Symantec, если создается одна виртуальная машина.

Параметр **Из коллекции** открывает мастер, помогающий установить виртуальную машину. Последняя страница мастера используется для установки агента ВМ и модуля безопасности Symantec.

Общие инструкции см. в статье [Создание виртуальной машины под управлением Windows Server][Создание виртуальной машины под управлением Windows Server]. Дойдя до последней страницы мастера, выполните следующие действия.

1.  В разделе агента ВМ выберите **Установить агент ВМ**.

2.  В разделе модулей безопасности установите флажок **Symantec Endpoint Protection**.

    ![Установка агента ВМ и клиента Endpoint Protection][Установка агента ВМ и клиента Endpoint Protection]

3.  Щелкните флажок в нижней части страницы, чтобы создать виртуальную машину.

## Установка Symantec Endpoint Protection на существующей виртуальной машине

Для установки Deep Security Agent на существующей виртуальной машине потребуются следующие компоненты.

-   Модуль Azure PowerShell версии не ниже 0.8.2. Инструкции и ссылку на последнюю версию см. в статье [Установка и настройка Azure PowerShell][Установка и настройка Azure PowerShell].

-   Агент ВМ. Инструкции и ссылку на загрузку см. в публикации в блоге [Агент ВМ и расширения — часть 2][Агент ВМ и расширения — часть 2].

Откройте сеанс Azure PowerShell и выполните следующие команды. Не забудьте подставить собственные значения вместо заполнителей, таких как MyServiceName.

1.  Извлеките имя облачной службы, имя виртуальной машины и ВМ Azure и сохраните эти значения в переменных, чтобы они могли использоваться следующими командами:

    `$servicename = MyServiceName`

    `$name = MyVmName`

    `$vm = Get-AzureVM -ServiceName $servicename -Name $name`

    > [WACOM.NOTE] Если имена облачной службы и ВМ не известны, выполните командлет Get-AzureVM, чтобы отобразить эти сведения для всех ВМ в текущей подписке.

2.  Добавьте агент Symantec Endpoint Protection на виртуальную машину. Чтобы установить последнюю версию, выполните следующую команду:

    `Set-AzureVMExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection -VM $vm.VM`

    > [WACOM.NOTE] Если требуется установить определенную версию, выполните следующую команду, чтобы получить список доступных версий: `Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection`.
    > Затем при запуске командлета Set-AzureVMExtension включите параметр Version.

3.  Обновите ВМ, установив таким образом агент Symantec Endpoint Protection:

    `Update-AzureVM -ServiceName $servicename -Name $name -VM $vm.VM`

## Дополнительные ресурсы

[Как войти в виртуальную машину под управлением Windows Server][Как войти в виртуальную машину под управлением Windows Server]

[Управление расширениями][Управление расширениями]

<!--Link references-->

  [Symantec Endpoint Protection на платформе Microsoft Azure]: http://go.microsoft.com/fwlink/p/?LinkId=403942
  [Портал управления Azure]: http://manage.windowsazure.com
  [Создание виртуальной машины под управлением Windows Server]: http://go.microsoft.com/fwlink/p/?LinkId=403943
  [Установка агента ВМ и клиента Endpoint Protection]: ./media/virtual-machines-install-symantec/InstallVMAgentandSymantec.png
  [Установка и настройка Azure PowerShell]: http://go.microsoft.com/fwlink/p/?LinkId=320552
  [Агент ВМ и расширения — часть 2]: http://go.microsoft.com/fwlink/p/?LinkId=403947
  [Как войти в виртуальную машину под управлением Windows Server]: ../virtual-machines-log-on-windows-server/
  [Управление расширениями]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
