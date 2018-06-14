---
title: Создание пакета поддержки StorSimple серии 8000 | Документация Майкрософт
description: Узнайте, как создавать, расшифровывать и изменять содержимое пакетов поддержки для устройства StorSimple серии 8000.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: c83caae58e73d6f2d0fe086938e0bbc5cfdf80c4
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/10/2018
ms.locfileid: "27743584"
---
# <a name="create-and-manage-a-support-package-for-storsimple-8000-series"></a>Создание пакета поддержки StorSimple для устройства StorSimple серии 8000 и управление им

## <a name="overview"></a>Обзор

Пакет поддержки StorSimple — это простой в использовании механизм, который собирает все соответствующие журналы, помогая службе технической поддержки Майкрософт устранять неполадки в работе устройства StorSimple. Собранные журналы шифруются и сжимаются.

Это руководство содержит пошаговые инструкции по созданию пакета поддержки для устройства StorSimple серии 8000 и управлению им. Если вы работаете с виртуальным массивом StorSimple, перейдите к [созданию пакета журналов](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="create-a-support-package"></a>Создать пакет поддержки.

В некоторых случаях необходимо вручную создать пакет поддержки с помощью Windows PowerShell для StorSimple. Например: 

* Если необходимо удалить конфиденциальную информацию из файлов журнала до их предоставления службе технической поддержки Майкрософт.
* Если возникают сложности с отправкой пакета из-за проблем с подключением.

Созданный вручную пакет поддержки можно предоставить службе технической поддержки Майкрософт по электронной почте. Для создания пакета поддержки в Windows PowerShell для StorSimple выполните следующие действия.

#### <a name="to-create-a-support-package-in-windows-powershell-for-storsimple"></a>Создание пакета поддержки в Windows PowerShell для StorSimple

1. Для запуска сеанса Windows PowerShell с правами администратора на удаленном компьютере, используемом для подключения к устройству StorSimple, введите следующую команду:
   
    `Start PowerShell`
2. В сеансе Windows PowerShell подключитесь к консоли SSAdmin устройства:
   
   1. В командной строке введите:
     
       `$MS = New-PSSession -ComputerName <IP address for DATA 0> -Credential SSAdmin -ConfigurationName "SSAdminConsole"`
   2. В открывшемся диалоговом окне введите пароль администратора устройства. Пароль по умолчанию — _Password1_.
     
      ![Диалоговое окно с учетными данными PowerShell](./media/storsimple-8000-create-manage-support-package/IC740962.png)
   3. Нажмите кнопку **ОК**.
   4. В командной строке введите:
     
      `Enter-PSSession $MS`
3. В открывшемся сеансе введите соответствующую команду.
   
   * Для сетевых ресурсов, которые защищены паролем, введите:
     
       `Export-HcsSupportPackage -Path <\\IP address\location of the shared folder> -Include Default -Credential domainname\username`
     
       Будет предложено ввести пароль и парольную фразу для шифрования (поскольку пакет поддержки зашифрован). Затем в папке по умолчанию (добавлено имя устройства и текущая дата и время) создается пакет поддержки.
   * Для общих папок, не защищенных паролем, указывать параметр `-Credential` не требуется. Заполните следующие поля:
     
       `Export-HcsSupportPackage`
     
       Пакет поддержки создается для обоих контроллеров в папке по умолчанию. Он является зашифрованным сжатым файлом, который можно отправить в службу поддержки Майкрософт для устранения неполадок. Дополнительные сведения см. в статье [Обращение в службу поддержки Майкрософт](storsimple-8000-contact-microsoft-support.md).

### <a name="the-export-hcssupportpackage-cmdlet-parameters"></a>Параметры командлета Export-HcsSupportPackage

С командлетом Export-HcsSupportPackage можно использовать следующие параметры.

| Параметр | Обязательный/необязательный | ОПИСАНИЕ |
| --- | --- | --- |
| `-Path` |Обязательно |Позволяет указать расположение общей сетевой папки, в которой будет расположен пакет поддержки. |
| `-EncryptionPassphrase` |Обязательно |Позволяет указать парольную фразу для шифрования пакета поддержки. |
| `-Credential` |Необязательно |Позволяет передать учетные данные для доступа к общей сетевой папке. |
| `-Force` |Необязательно |Используется, чтобы пропустить шаг подтверждения парольной фразы шифрования. |
| `-PackageTag` |Необязательно |Позволяет указать в поле *Путь* каталог, в котором будет размещен пакет поддержки. Значение по умолчанию — [имя устройства]-[текущая дата и время:гггг-ММ-дд-ЧЧ-мм-сс]. |
| `-Scope` |Необязательно |Укажите в качестве значения **Cluster** (по умолчанию), чтобы создать пакет поддержки для обоих контроллеров. Если требуется создать пакет только для текущего контроллера, укажите значение **Controller**. |

## <a name="edit-a-support-package"></a>Изменение пакета поддержки

Созданный пакет поддержки при необходимости можно изменить для удаления конфиденциальных сведений. К таким сведениям относится имена томов, IP-адреса устройств и имена резервных копий из файлов журнала.

> [!IMPORTANT]
> Изменить можно только пакет поддержки, который был создан через Windows PowerShell для StorSimple. Пакет, созданный на портале Azure в службе диспетчера устройств StorSimple, изменить нельзя.

Чтобы изменить пакет поддержки перед отправкой на сайт технической поддержки корпорации Майкрософт, сначала расшифруйте пакет поддержки, внесите изменения в файлы, а затем снова зашифруйте пакет. Выполните следующие действия.

#### <a name="to-edit-a-support-package-in-windows-powershell-for-storsimple"></a>Изменение пакета поддержки в Windows PowerShell для StorSimple

1. Создайте пакет поддержки в соответствии с инструкциями в приведенном выше разделе [Создание пакета поддержки в Windows PowerShell для StorSimple](#to-create-a-support-package-in-windows-powershell-for-storsimple).
2. [Загрузите скрипт](http://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) в локальную систему на клиентском компьютере.
3. Импортируйте модуль Windows PowerShell. Укажите путь к локальной папке, в которую вы скачали скрипт. Для импорта модуля введите команду:
   
    `Import-module <Path to the folder that contains the Windows PowerShell script>`
4. Все файлы имеют расширение *AES*. Они сжаты и зашифрованы. Для распаковки и расшифровки файлов введите:
   
    `Open-HcsSupportPackage <Path to the folder that contains support package files>`
   
    Обратите внимание, что теперь у всех файлов отображаются настоящие расширения.
   
    ![Изменение пакета поддержки](./media/storsimple-8000-create-manage-support-package/IC750706.png)
5. При появлении запроса введите парольную фразу, которая использовалась при создании пакета поддержки.
   
        cmdlet Open-HcsSupportPackage at command pipeline position 1
   
        Supply values for the following parameters:EncryptionPassphrase: ****
6. Перейдите в папку с файлами журналов. Так как файлы журналов теперь распакованы и расшифрованы, у них будут исходные расширения. Измените эти файлы, удалив информацию о пользователе (например, имена томов и IP-адреса устройств), и сохраните файлы.
7. Закройте файлы, чтобы сжать их с помощью средства gzip и зашифровать алгоритмом AES-256. Это выполняется из соображений безопасности, а также для ускорения передачи пакета поддержки по сети. Для сжатия и шифрования файлов введите следующее:
   
    `Close-HcsSupportPackage <Path to the folder that contains support package files>`
   
    ![Изменение пакета поддержки](./media/storsimple-8000-create-manage-support-package/IC750707.png)
8. По запросу введите парольную фразу для шифрования для измененного пакета поддержки.
   
        cmdlet Close-HcsSupportPackage at command pipeline position 1
        Supply values for the following parameters:EncryptionPassphrase: ****
9. Запишите новую парольную фразу, чтобы сообщить ее специалистам службы технической поддержки Майкрософт по запросу.

### <a name="example-editing-files-in-a-support-package-on-a-password-protected-share"></a>Пример: изменение файлов в пакете поддержки на ресурсе, защищенном паролем

Ниже приведен пример, демонстрирующий расшифровку, изменение и повторное шифрование пакета поддержки.

        PS C:\WINDOWS\system32> Import-module C:\Users\Default\StorSimple\SupportPackage\HCSSupportPackageTools.psm1

        PS C:\WINDOWS\system32> Open-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

        cmdlet Open-HcsSupportPackage at command pipeline position 1

        Supply values for the following parameters:

        EncryptionPassphrase: ****

        PS C:\WINDOWS\system32> Close-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

        cmdlet Close-HcsSupportPackage at command pipeline position 1

        Supply values for the following parameters:

        EncryptionPassphrase: ****

        PS C:\WINDOWS\system32>

## <a name="next-steps"></a>Дополнительная информация

* Дополнительные сведения о [данных, собираемых в пакете поддержки](https://support.microsoft.com/help/3193606/storsimple-support-packages-and-device-logs).
* Узнайте об [использовании пакетов поддержки и журналов устройства для устранения неполадок при его развертывании](storsimple-8000-troubleshoot-deployment.md#support-packages-and-device-logs-available-for-troubleshooting).
* Узнайте, как [использовать службу диспетчера устройств StorSimple для администрирования устройства StorSimple](storsimple-8000-manager-service-administration.md).

