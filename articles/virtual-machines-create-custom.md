<properties urlDisplayName="Create a custom VM" pageTitle=" Создание настраиваемой виртуальной машины под управлением Linux в Azure" metaKeywords="Azure custom vm, creating custom vm" description="Узнайте, как создать настраиваемую виртуальную машину в Azure." metaCanonical="http://www.windowsazure.com/ru-ru/manage/windows/how-to-guides/custom-create-a-vm/" services="virtual-machines" documentationCenter="" title="" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav" />

# Как создать настраиваемую виртуальную машину

*Настраиваемая* виртуальная машина — это виртуальная машина, созданная с помощью метода **Из коллекции**, что дает больший выбор настроек, чем метод **Быстрое создание**. В частности, описаны такие возможности:

-   Больший выбор образов для создания виртуальной машины (ВМ).
-   Подключение виртуальной машины к виртуальной сети.
-   Установка агента виртуальной машины и расширений, таких как защита от вредоносных программ
-   Добавление виртуальной машины к существующей облачной службе.
-   Добавление виртуальной машины в группу доступности.

**Важно!** Если к виртуальной машине необходимо подключаться с помощью имени узла по виртуальной сети или настроить подключения между организациями, при создании виртуальной машины обязательно укажите виртуальную сеть. Виртуальную машину можно настроить для подключения к виртуальной сети только при ее создании. Дополнительную информацию о виртуальных сетях см. в разделе [Обзор виртуальных сетей Azure][Обзор виртуальных сетей Azure].

[WACOM.INCLUDE [виртуальные-машины-создание-WindowsVM](../includes/virtual-machines-create-WindowsVM.md)]

  [Обзор виртуальных сетей Azure]: http://go.microsoft.com/fwlink/p/?LinkID=294063
