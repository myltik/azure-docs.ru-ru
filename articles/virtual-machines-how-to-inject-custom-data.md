<properties title="virtual-machines-how-to-inject-custom-data" pageTitle="Включение пользовательских данных в виртуальные машины Azure" description="В этом разделе описывается включение пользовательских данных в виртуальную машину Azure во время создания экземпляра, а также поиск пользовательских данных в Windows или Linux." metaKeywords="Azure linux vm, linux vm, userdata vm, user data vm, custom data vm, windows custom data" services="virtual-machines" solutions="" documentationCenter="" authors="rasquill" manager="timlt" editor="tysonn" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="rasquill" />

# Включение пользовательских данных в виртуальную машину Azure

Включение скрипта или других данных в виртуальную машину Azure при ее подготовке является очень распространенным сценарием, независимо от используемой операционной системы, Microsoft Windows или Linux. В этом разделе описываются следующие действия:

-   включение данных в виртуальную машину Azure при ее подготовке;

-   извлечение их для Windows и Linux;

-   использование специальных средств, доступных в некоторых системах, для автоматического обнаружения и обработки пользовательских данных.

> [WACOM.NOTE] Настоящий раздел является продолжением [данной публикации в блоге Azure][данной публикации в блоге Azure], посвященной этой функции, и его актуальность будет поддерживаться по мере появления новых функциональных возможностей.

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->

Содержание раздела

-   [Включение пользовательских данных в виртуальную машину Azure][Включение пользовательских данных в виртуальную машину Azure]

-   [Использование пользовательских данных в виртуальной машине][Использование пользовательских данных в виртуальной машине]

-   [Дальнейшие действия][Дальнейшие действия]

## <span id="injectingCustomData"></span></a>Включение пользовательских данных в виртуальную машину Azure

В настоящее время эта функция поддерживается только в [кроссплатформенном интерфейсе командной строки Microsoft Azure][кроссплатформенном интерфейсе командной строки Microsoft Azure]. Хотя для команды `azure vm create` можно использовать любой из вариантов, следующий подход демонстрирует один самый простой способ.

        PASSWORD='AcceptablePassword -- more than 8 chars, a cap, a num, a special'
        VMNAME=mycustomdataubuntu
        USERNAME=username
        VMIMAGE= An image chosen from among those listed by azure vm image list
        azure vm create $VMNAME $VMIMAGE $USERNAME $PASSWORD --location "West US" --json -d ./custom-data.txt -e 22

## <span id="usingCustomData"></span></a>Использование пользовательских данных в виртуальной машине

-   Если виртуальная машина Azure является виртуальной машиной Windows, то пользовательские данные сохраняются в файле `%SYSTEMDRIVE%\AzureData\CustomData.bin`, и хотя для передачи с локального компьютера на новую виртуальную машину эти данные были зашифрованы с помощью кодировки base64, они автоматически расшифровываются и могут немедленно открываться и использоваться.

> [WACOM.NOTE] Если такой файл существует, он перезаписывается. В каталоге устанавливается защита **System:Full Control** и **Administrators:Full Control**.

-   Если виртуальная машина Azure является виртуальной машиной Linux, то пользовательский файл данных будет располагаться в следующих двух местах, но данные будут в кодировке base64, поэтому сначала придется их декодировать.

    -   В `/var/lib/waagent/ovf-env.xml`
    -   В `/var/lib/waagent/CustomData`

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <span id="nextsteps"></span></a>Дальнейшие действия: использование cloud-init

Если виртуальная машина Azure является образом Ubuntu, то можно с помощью cloud-init запустить скрипт для автоматического использования пользовательских данных (или если файл пользовательских данных является скриптом, для его выполнения). Дополнительные сведения см. в [документации по cloud-init для Ubuntu][документации по cloud-init для Ubuntu].

<!--Link references-->

[Справочник по API REST управления добавлением службы роли][Справочник по API REST управления добавлением службы роли]

[Кроссплатформенный интерфейс командной строки Microsoft Azure][кроссплатформенном интерфейсе командной строки Microsoft Azure]

  [данной публикации в блоге Azure]: http://azure.microsoft.com/blog/2014/04/21/custom-data-and-cloud-init-on-windows-azure/
  [Включение пользовательских данных в виртуальную машину Azure]: #injectingCustomData
  [Использование пользовательских данных в виртуальной машине]: #usingCustomData
  [Дальнейшие действия]: #nextsteps
  [кроссплатформенном интерфейсе командной строки Microsoft Azure]: https://github.com/Azure/azure-sdk-tools-xplat
  [документации по cloud-init для Ubuntu]: https://help.ubuntu.com/community/CloudInit
  [Справочник по API REST управления добавлением службы роли]: http://msdn.microsoft.com/library/azure/jj157186.aspx
