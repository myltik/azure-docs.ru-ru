<properties title="How to install and configure Trend on an Azure VM" pageTitle="How to install and configure Trend Micro Deep Security as a Service on an Azure VM" description="Describes installing and configuring Trend Micro security on a VM in Azure" metaKeywords="" services="virtual machines" solutions="" documentationCenter="" authors="kathydav" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-multiple" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav"></tags>

# Установка и настройка Trend Micro Deep Security как услуги на ВМ Azure

В этой статье показывается, как можно установить и настроить Trend Micro Deep Security как услуги на новой или существующей виртуальной машине (ВМ) под управлением Windows Server. Защита, которую предоставляет Deep Security как услуга, включает защиту от вредоносных программ, брандмауэр, систему предотвращения вторжений и мониторинг целостности.

Клиент устанавливается как модуль безопасности с помощью агента ВМ. На новой виртуальной машине агент ВМ будет устанавливаться вместе с агентом Deep Security. На существующей виртуальной машине без агента ВМ необходимо сначала загрузить и установить этот агент. В данной статье рассматриваются обе ситуации.

При наличии действительной подписки от Trend для локального решения можно использовать ее для защиты виртуальных машин Azure. Если у вас еще нет подписки, можно зарегистрироваться для получения пробной подписки. Дополнительные сведения об этом решении см. в публикации в блоге [Расширение агента ВМ Microsoft Azure для Deep Security][Расширение агента ВМ Microsoft Azure для Deep Security].

## Установка агента Deep Security на новой виртуальной машине

[Портал управления Azure][Портал управления Azure] позволяет установить агент ВМ и модуль безопасности Trend при использовании параметра **Из коллекции** для создания виртуальной машины. Это простейший способ добавления защиты от Trend, если создается одна виртуальная машина.

Параметр **Из коллекции** открывает мастер, помогающий установить виртуальную машину. Последняя страница мастера используется для установки агента ВМ и модуля безопасности Trend. Общие инструкции см. в статье [Создание виртуальной машины под управлением Windows Server][Создание виртуальной машины под управлением Windows Server]. Дойдя до последней страницы мастера, выполните следующие действия.

1.  В разделе агента ВМ выберите **Установить агент ВМ**.

2.  В разделе модулей безопасности установите флажок **Trend Micro Deep Security Agent** (Агент Trend Micro Deep Security).

3.  Чтобы создать виртуальную машину, поставьте галочку.

    ![Установка агента ВМ и агента Deep Security][Установка агента ВМ и агента Deep Security]

## Установка агента Deep Security на существующей виртуальной машине

Для этого потребуются следующие компоненты.

-   Модуль Azure PowerShell версии не ниже 0.8.2. Инструкции и ссылку на последнюю версию см. в статье [Установка и настройка Azure PowerShell][Установка и настройка Azure PowerShell].

-   Агент ВМ. Инструкции и ссылку на загрузку см. в публикации в блоге [Агент ВМ и расширения — часть 2][Агент ВМ и расширения — часть 2].

Откройте сеанс Azure PowerShell и выполните следующие команды. Не забудьте подставить собственные значения вместо заполнителей, таких как MyServiceName.

1.  Извлеките имя облачной службы, имя виртуальной машины и ВМ Azure и сохраните эти значения в переменных, чтобы они могли использоваться следующими командами:

    `$servicename = MyServiceName`

    `$name = MyVmName`

    `$vm = Get-AzureVM -ServiceName $servicename -Name $name`

    > [WACOM.NOTE] Если имена облачной службы и ВМ не известны, выполните командлет Get-AzureVM, чтобы отобразить эти сведения для всех ВМ в текущей подписке.

2.  Добавьте агент Deep Security на виртуальную машину:

    `Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA -VM $vm.VM`

    > [WACOM.NOTE] Если требуется установить определенную версию, выполните следующую команду, чтобы получить список доступных версий: `Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA`. Затем при запуске командлета Set-AzureVMExtension включите параметр Version.

3.  Обновите ВМ, установив таким образом агент Deep Security:

    `Update-AzureVM -ServiceName $servicename -Name $name -VM $vm.VM`

## Дальнейшие действия

После установки агента потребуется несколько минут для его запуска. Затем необходимо будет активировать Deep Security на виртуальной машине, чтобы можно было осуществлять управление с помощью диспетчера Deep Security. См. следующую документацию:

-   статью Trend об этом решении, [Instant-On Cloud Security for Microsoft Azure][Instant-On Cloud Security for Microsoft Azure] (Мгновенное включение облачной защиты для Microsoft Azure);
-   [образец скрипта Windows PowerShell][образец скрипта Windows PowerShell] для настройки виртуальной машины;
-   [инструкции][инструкции] для этого образца скрипта.

## Дополнительные ресурсы

[Как войти в виртуальную машину под управлением Windows Server][Как войти в виртуальную машину под управлением Windows Server]

[Управление расширениями][Управление расширениями]

<!--Link references-->

  [Расширение агента ВМ Microsoft Azure для Deep Security]: http://go.microsoft.com/fwlink/p/?LinkId=403945
  [Портал управления Azure]: http://manage.windowsazure.com
  [Создание виртуальной машины под управлением Windows Server]: http://go.microsoft.com/fwlink/p/?LinkId=403943
  [Установка агента ВМ и агента Deep Security]: ./media/virtual-machines-install-trend/InstallVMAgentandTrend.png
  [Установка и настройка Azure PowerShell]: http://go.microsoft.com/fwlink/p/?LinkId=320552
  [Агент ВМ и расширения — часть 2]: http://go.microsoft.com/fwlink/p/?LinkId=403947
  [Instant-On Cloud Security for Microsoft Azure]: http://go.microsoft.com/fwlink/?LinkId=404101
  [образец скрипта Windows PowerShell]: http://go.microsoft.com/fwlink/?LinkId=404100
  [инструкции]: http://go.microsoft.com/fwlink/?LinkId=404099
  [Как войти в виртуальную машину под управлением Windows Server]: ../virtual-machines-log-on-windows-server/
  [Управление расширениями]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
