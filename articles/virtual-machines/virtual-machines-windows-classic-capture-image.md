---
title: Запись образа виртуальной машины Azure Windows | Microsoft Docs
description: Запись образа виртуальной машины Azure Windows, созданной с использованием классической модели развертывания.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management

ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: cynthn

---
# <a name="capture-an-image-of-an-azure-windows-virtual-machine-created-with-the-classic-deployment-model."></a>Запись образа виртуальной машины Azure Windows, созданной с использованием классической модели развертывания.
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Сведения о модели Resource Manager см. в статье [Создание копии виртуальной машины Windows, запущенной в Azure](virtual-machines-windows-vhd-copy.md).

В этой статье показано, как записать виртуальную машину Azure под управлением Windows, чтобы использовать ее в качестве образа при создании других виртуальных машин. Этот образ включает диск с операционной системой и все диски с данными, подключенные к виртуальной машине. Он не включает сетевые конфигурации, поэтому необходимо настроить их при создании других виртуальных машин на базе этого образа.

Azure хранит образы в папке **Мои образы**. Здесь же хранятся все отправленные вами образы. Дополнительные сведения об образах см. в разделе [Об образах виртуальных машин](virtual-machines-linux-classic-about-images.md).

## <a name="before-you-begin##"></a>Перед началом работы
В этой статье предполагается, что вы уже создали виртуальную машину Azure и настроили операционную систему, а также присоединили диски данных. Если это еще не сделано, см. данные указания:

* [Создание виртуальной машины из образа](virtual-machines-windows-classic-createportal.md)
* [Подключение диска с данными к виртуальной машине](virtual-machines-windows-classic-attach-disk.md)
* Убедитесь, что роли сервера поддерживаются Sysprep. Дополнительные сведения см. в разделе [Поддержка ролей сервера в Sysprep](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

> [!WARNING]
> Этот процесс удаляет исходную виртуальную машину, как только она будет зарегистрирована. 
> 
> 

Перед записью образа виртуальной машины Azure рекомендуется создать резервную копию целевой виртуальной машины. Это можно сделать с помощью службы архивации Azure. Дополнительные сведения см. в статье [Архивация виртуальных машин Azure](../backup/backup-azure-vms.md). Доступны другие решения от сертифицированных партнеров. Чтобы узнать, что сейчас доступно, выполните поиск в Azure Marketplace.

## <a name="capture-the-virtual-machine"></a>Запись виртуальной машины
1. На [классическом портале Azure](http://manage.windowsazure.com) **подключитесь **к виртуальной машине. Указания см. в разделе [Как войти в виртуальную машину под управлением Windows Server][Как войти в виртуальную машину под управлением Windows Server].
2. Откройте окно командной строки с правами администратора.
3. Измените каталог на `%windir%\system32\sysprep`и запустите файл sysprep.exe.
4. Откроется диалоговое окно **Программа подготовки системы** . Выполните следующее:
   
   * В разделе **Действие по очистке системы** выберите **Запуск при первом включении компьютера (OOBE)** и убедитесь, что установлен флажок **Обобщить**. Дополнительную информацию об использовании Sysprep см. в разделе [Как использовать Sysprep: введение][Как использовать Sysprep: введение].
   * В разделе **Параметры завершения работы** выберите **Завершение работы**.
   * Нажмите кнопку **ОК**.
   
   ![Запуск Sysprep](./media/virtual-machines-windows-classic-capture-image/SysprepGeneral.png)
5. Sysprep завершит работу виртуальной машины, при этом ее состояние на классическом портале Azure изменится на **Остановлена**.
6. На классическом портале Azure щелкните **Виртуальные машины** , а затем выберите виртуальную машину, которую требуется записать.
7. На панели команд щелкните **Запись**.
   
   ![Запись виртуальной машины](./media/virtual-machines-windows-classic-capture-image/CaptureVM.png)
   
   Отображается диалоговое окно **Запись виртуальной машины** .
8. В поле **Имя образа**введите имя нового образа.
9. Прежде чем добавить образ Windows Server в набор настраиваемых образов, его необходимо обобщить, запустив программу Sysprep, как описано в предыдущих шагах. Щелкните **Я запустил Sysprep на виртуальной машине** , чтобы указать, что это выполнено.
10. Щелкните галочку, чтобы записать образ. Новый образ станет доступным в разделе **Образы**.
    
    ![Успешная запись образа](./media/virtual-machines-windows-classic-capture-image/VMCapturedImageAvailable.png)

## <a name="next-steps"></a>Дальнейшие действия
Образ готов к использованию для создания виртуальных машин. Для этого создайте виртуальную машину с помощью пункта меню **Из коллекции** и выберите только что созданный образ. Инструкции см. в статье [Создание виртуальной машины из образа](virtual-machines-windows-classic-createportal.md).

[Как войти в виртуальную машину под управлением Windows Server]: virtual-machines-windows-classic-connect-logon.md
[Как использовать Sysprep: введение]: http://technet.microsoft.com/library/bb457073.aspx
[Запуск Sysprep.exe]: ./media/virtual-machines-capture-image-windows-server/SysprepCommand.png
[Ввод параметров Sysprep.exe]: ./media/virtual-machines-windows-classic-capture-image/SysprepGeneral.png
[Виртуальная машина остановлена]: ./media/virtual-machines-capture-image-windows-server/SysprepStopped.png
[Запись образа виртуальной машины]: ./media/virtual-machines-windows-classic-capture-image/CaptureVM.png
[Ввод имени образа]: ./media/virtual-machines-capture-image-windows-server/Capture.png
[Успешная запись образа]: ./media/virtual-machines-capture-image-windows-server/CaptureSuccess.png
[Использование записанного образа]: ./media/virtual-machines-capture-image-windows-server/MyImagesWindows.png



<!--HONumber=Oct16_HO2-->


