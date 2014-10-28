<properties title="How To Change the Drive Letter of the Windows Temporary Disk" pageTitle="How To Change the Drive Letter of the Windows Temporary Disk" description="Describes how to remap the temporary disk on a Windows VM in Azure" metaKeywords="" services="virtual machines" solutions="" documentationCenter="" authors="kathydav" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav"></tags>

# Изменение буквы диска для временного диска Windows

Если диск D необходимо использовать в других целях, можно изменить букву диска для временного диска. Обычно это делается для поддержки приложения или службы, использующей диск D в качестве расположения постоянного хранилища.

Перед началом работы необходимо проверить, что имеются следующие компоненты.

-   Один подключенный диск данных, который можно использовать для хранения файла подкачки Windows (pagefile.sys) во время этой процедуры. Инструкции см. в разделе [Подключение диска данных к виртуальной машине Windows][Подключение диска данных к виртуальной машине Windows].
-   Отправленный виртуальный жесткий диск в учетной записи хранения, если планируется использовать VHD существующего диска данных на букве диска D. Инструкции см. в шагах 3 и 4 в разделе [Создание и отправка VHD Windows Server в Azure][Создание и отправка VHD Windows Server в Azure].

## Изменение буквы диска

1.  Войдите на виртуальную машину.

2.  Переместите файл pagefile.sys с диска D на другой диск.

3.  Перезапустите виртуальную машину.

4.  Снова войдите и измените букву диска с D на E.

5.  На [портале управления Azure][портале управления Azure] подключите существующий диск данных или пустой диск данных.

6.  Снова войдите на виртуальную машину, инициализируйте диск и назначьте только что подключенному диску букву D.

7.  Убедитесь, что буква E назначена диску временного хранилища.

8.  Переместите файл pagefile.sys с другого диска на диск E.

## Дополнительные ресурсы

[Как войти в виртуальную машину под управлением Windows Server][Как войти в виртуальную машину под управлением Windows Server]

[Отключение диска данных от виртуальной машины][Отключение диска данных от виртуальной машины]

[Что такое учетная запись хранения?][Что такое учетная запись хранения?]

<!--Link references-->

  [Подключение диска данных к виртуальной машине Windows]: ../storage-windows-attach-disk
  [Создание и отправка VHD Windows Server в Azure]: ../virtual-machines-create-upload-vhd-windows-server/
  [портале управления Azure]: http://manage.windowsazure.com
  [Как войти в виртуальную машину под управлением Windows Server]: ../virtual-machines-log-on-windows-server/
  [Отключение диска данных от виртуальной машины]: ../storage-windows-detach-disk/
  [Что такое учетная запись хранения?]: ../storage-whatis-account/
