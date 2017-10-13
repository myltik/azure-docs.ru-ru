---
title: "Создание пакета поддержки StorSimple | Документация Майкрософт"
description: "Узнайте, как создавать, расшифровывать и изменять содержимое пакетов поддержки для устройства StorSimple."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: eac76f5f-5db1-4c92-af8c-54053b91e66c
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/17/2016
ms.author: alkohli
ms.openlocfilehash: 32d20e7a8adcfc646c592213fe7395b87a93c985
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-manage-a-storsimple-support-package"></a>Создание пакетов поддержки StorSimple и управление ими
## <a name="overview"></a>Обзор
Пакет поддержки StorSimple — это простой в использовании механизм, который собирает все соответствующие журналы, помогая службе технической поддержки Майкрософт устранять неполадки в работе устройства StorSimple. Собранные журналы шифруются и сжимаются.

В этом руководстве содержатся пошаговые инструкции по созданию пакета поддержки и управлению им.

## <a name="create-and-upload-a-support-package-in-the-azure-classic-portal"></a>Создание и отправка пакета поддержки на классическом портале Azure
Можно создать и отправить пакет поддержки на сайт службы поддержки Майкрософт с помощью страницы **Обслуживание** для службы на классическом портале Azure.

> [!NOTE]
> Для отправки требуется ключ доступа к поддержке. Он должен быть предоставлен вам сотрудником службы поддержки по электронной почте.
> 
> 

Зашифрованный и сжатый пакет поддержки (CAB-файл) создается и отправляется на сайт службы поддержки. Затем сотрудник службы поддержки может получить этот пакет с сайта технической поддержки для устранения неполадки.

Чтобы создать пакет поддержки, выполните указанные ниже действия на классическом портале.

#### <a name="to-create-a-support-package-in-the-azure-classic-portal"></a>Создание пакета поддержки на классическом портале Azure
1. Выберите **Устройства** > **Обслуживание**.
2. В разделе **Пакет поддержки** щелкните **Создание и отправка пакета поддержки**.
3. В окне **Создание и загрузка пакета поддержки** выполните следующие действия.
   
    ![Создание пакета поддержки](./media/storsimple-create-manage-support-package/IC740923.png)
   
   * В текстовое поле **Ключ доступа к поддержке** введите ключ доступа. Этот ключ должен быть отправлен вам сотрудником службы поддержки Майкрософт в сообщении электронной почты.
   * Установите флажок, чтобы дать согласие на автоматическую отправку пакета поддержки на сайт службы поддержки Майкрософт.
   * Щелкните значок галочки  ![значок с изображением флажка](./media/storsimple-create-manage-support-package/IC740895.png).

## <a name="manually-create-a-support-package"></a>Создание пакета поддержки вручную
В некоторых случаях необходимо вручную создать пакет поддержки с помощью Windows PowerShell для StorSimple. Например:

* Если необходимо удалить конфиденциальную информацию из файлов журнала до их предоставления службе технической поддержки Майкрософт.
* Если возникают сложности с отправкой пакета из-за проблем с подключением.

Созданный вручную пакет поддержки можно предоставить службе технической поддержки Майкрософт по электронной почте. Для создания пакета поддержки в Windows PowerShell для StorSimple выполните следующие действия.

#### <a name="to-create-a-support-package-in-windows-powershell-for-storsimple"></a>Создание пакета поддержки в Windows PowerShell для StorSimple
1. Для запуска сеанса Windows PowerShell с правами администратора на удаленном компьютере, используемом для подключения к устройству StorSimple, введите следующую команду:
   
    `Start PowerShell`
2. В сеансе Windows PowerShell подключитесь к консоли SSAdmin устройства:
   
   * В командной строке введите:
     
       `$MS = New-PSSession -ComputerName <IP address for DATA 0> -Credential SSAdmin -ConfigurationName "SSAdminConsole"`
   * В открывшемся диалоговом окне введите пароль администратора устройства. Пароль по умолчанию:
     
      `Password1`
     
      ![Диалоговое окно с учетными данными PowerShell](./media/storsimple-create-manage-support-package/IC740962.png)
   * Нажмите кнопку **ОК**.
   * В командной строке введите:
     
      `Enter-PSSession $MS`
3. В открывшемся сеансе введите соответствующую команду.
   
   * Для сетевых ресурсов, которые защищены паролем, введите:
     
       `Export-HcsSupportPackage –PackageTag "MySupportPackage" –Credential "Username" -Force`
     
       Будет предложено ввести пароль, путь к общей сетевой папке и парольную фразу для шифрования (поскольку пакет поддержки зашифрован). Затем будет создан пакет поддержки в указанной папке.
   * Для общих папок, не защищенных паролем, указывать параметр `-Credential` не требуется. Заполните следующие поля:
     
       `Export-HcsSupportPackage –PackageTag "MySupportPackage" -Force`
     
       Пакет поддержки создается для обоих контроллеров в указанной общей сетевой папке. Это зашифрованный сжатый файл, который можно отправить в службу поддержки Майкрософт для устранения неполадок. Дополнительные сведения см. в статье [Обращение в службу поддержки Майкрософт](storsimple-contact-microsoft-support.md).

### <a name="the-export-hcssupportpackage-cmdlet-parameters"></a>Параметры командлета Export-HcsSupportPackage
С командлетом Export-HcsSupportPackage можно использовать следующие параметры.

| Параметр | Обязательный/необязательный | Description (Описание) |
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
> Изменить можно только пакет поддержки, который был создан через Windows PowerShell для StorSimple. Пакет, созданный на классическом портале Azure в службе диспетчера StorSimple, изменить нельзя.
> 
> 

Чтобы изменить пакет поддержки перед отправкой на сайт технической поддержки корпорации Майкрософт, сначала расшифруйте пакет поддержки, внесите изменения в файлы, а затем снова зашифруйте пакет. Выполните следующие действия.

#### <a name="to-edit-a-support-package-in-windows-powershell-for-storsimple"></a>Изменение пакета поддержки в Windows PowerShell для StorSimple
1. Создайте пакет поддержки в соответствии с инструкциями в приведенном выше разделе [Создание пакета поддержки в Windows PowerShell для StorSimple](#to-create-a-support-package-in-windows-powershell-for-storsimple).
2. [Загрузите скрипт](http://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) в локальную систему на клиентском компьютере.
3. Импортируйте модуль Windows PowerShell. Укажите путь к локальной папке, в которую вы скачали скрипт. Для импорта модуля введите команду:
   
    `Import-module <Path to the folder that contains the Windows PowerShell script>`
4. Все файлы имеют расширение *AES*. Они сжаты и зашифрованы. Для распаковки и расшифровки файлов введите:
   
    `Open-HcsSupportPackage <Path to the folder that contains support package files>`
   
    Обратите внимание, что теперь у всех файлов отображаются настоящие расширения.
   
    ![Изменение пакета поддержки](./media/storsimple-create-manage-support-package/IC750706.png)
5. При появлении запроса введите парольную фразу, которая использовалась при создании пакета поддержки.
   
        cmdlet Open-HcsSupportPackage at command pipeline position 1
   
        Supply values for the following parameters:EncryptionPassphrase: ****
6. Перейдите в папку с файлами журналов. Так как файлы журналов теперь распакованы и расшифрованы, у них будут исходные расширения. Измените эти файлы, удалив информацию о пользователе (например, имена томов и IP-адреса устройств), и сохраните файлы.
7. Закройте файлы, чтобы сжать их с помощью средства gzip и зашифровать алгоритмом AES-256. Это выполняется из соображений безопасности, а также для ускорения передачи пакета поддержки по сети. Для сжатия и шифрования файлов введите следующее:
   
    `Close-HcsSupportPackage <Path to the folder that contains support package files>`
   
    ![Изменение пакета поддержки](./media/storsimple-create-manage-support-package/IC750707.png)
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

## <a name="next-steps"></a>Дальнейшие действия
* Узнайте об [использовании пакетов поддержки и журналов устройства для устранения неполадок при его развертывании](storsimple-troubleshoot-deployment.md#support-packages-and-device-logs-available-for-troubleshooting).
* Узнайте об [использовании службы диспетчера StorSimple для администрирования устройства StorSimple](storsimple-manager-service-administration.md).

