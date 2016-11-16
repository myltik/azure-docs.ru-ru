---
title: "Создание виртуальной машины Linux с помощью портала Azure | Документация Майкрософт"
description: "Создание виртуальной машины Linux с помощью портала Azure."
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: cc5dc395-dc54-4402-8804-2bb15aba8ea2
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: hero-article
ms.date: 10/28/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b1446cd8892e14988ff428eaa03233f8e9aefb8a


---
# <a name="create-a-linux-vm-on-azure-using-the-portal"></a>Создание виртуальной машины Linux в Azure с помощью портала
В этой статье показано, как создать виртуальную машину Linux с помощью [портала Azure](https://portal.azure.com/).

Для этого необходимы следующие компоненты:

* [учетная запись Azure](https://azure.microsoft.com/pricing/free-trial/);
* [файлы открытого и закрытого ключа SSH](virtual-machines-linux-mac-create-ssh-keys.md).

## <a name="sign-in"></a>входа
Войдя на портал Azure с помощью удостоверения учетной записи Azure, щелкните **+ Создать** в левом верхнем углу.

![экран_1](../media/virtual-machines-linux-quick-create-portal/screen1.png)

## <a name="choose-vm"></a>Выбор виртуальной машины
В **Marketplace** щелкните элемент **Виртуальные машины**, а затем выберите **Ubuntu Server 14.04 LTS** в списке образов **Рекомендуемые приложения**.  Внизу страницы проверьте модель развертывания (должна быть указана модель `Resource Manager` ) и нажмите кнопку **Создать**.

![экран_2](../media/virtual-machines-linux-quick-create-portal/screen2.png)

## <a name="enter-vm-options"></a>Ввод параметров виртуальной машины
На странице **Основные сведения** укажите следующие данные:

* имя виртуальной машины;
* имя пользователя-администратора;
* тип проверки подлинности (следует выбрать **Открытый ключ SSH**
* открытый ключ SSH в виде строки (строка из каталога `~/.ssh/` );
* имя группы ресурсов (или выберите существующую).

Нажмите кнопку **ОК**, чтобы продолжить и выбрать размер виртуальной машины. Вы должны увидеть примерно следующее:

![экран_3](../media/virtual-machines-linux-quick-create-portal/screen3.png)

## <a name="choose-vm-size"></a>Выбор размера виртуальной машины
Выберите размер **DS1**, чтобы установить Ubuntu на твердотельном накопителе категории "Премиум", а затем нажмите кнопку **Выбрать** для настройки параметров.

![экран_4](../media/virtual-machines-linux-quick-create-portal/screen4.png)

## <a name="storage-and-network"></a>Хранилище и сеть
В разделе **Параметры** оставьте настройки по умолчанию для хранилища и сети, а затем нажмите кнопку **ОК**, чтобы просмотреть сводку.  Обратите внимание, что при выборе DS1 устанавливается тип диска категории "Премиум". Буква **S** обозначает именно SSD.

![экран_5](../media/virtual-machines-linux-quick-create-portal/screen5.png)

## <a name="confirm-vm-settings-and-launch"></a>Проверка параметров виртуальной машины и запуск
Проверьте параметры для новой виртуальной машины Ubuntu и нажмите кнопку **ОК**.

![экран_6](../media/virtual-machines-linux-quick-create-portal/screen6.png)

## <a name="find-the-vm-nic"></a>Поиск сетевой карты виртуальной машины
На портале откройте панель мониторинга, а затем в разделе **Сетевые интерфейсы** выберите нужный сетевой адаптер.

![экран_7](../media/virtual-machines-linux-quick-create-portal/screen7.png)

## <a name="find-the-public-ip"></a>Поиск общедоступного IP-адреса
Откройте меню общедоступных IP-адресов в параметрах сетевого адаптера.

![экран_8](../media/virtual-machines-linux-quick-create-portal/screen8.png)

## <a name="ssh-to-the-vm"></a>Подключение к виртуальной машине по протоколу SSH
Подключитесь к общедоступному IP-адресу по протоколу SSH, используя открытый ключ SSH.  На рабочей станции Mac или Linux можно установить подключение по протоколу SSH прямо из терминала. Если используется рабочая станция Windows, для установки такого подключения к Linux необходимо использовать PuTTY, MobaXTerm или Cygwin.  Ниже приведено практическое руководство, которое поможет вам настроить рабочую станцию Windows для подключения к Linux по протоколу SSH, если вы еще этого не сделали.

[Использование ключей SSH с Windows в Azure](virtual-machines-linux-ssh-from-windows.md)

```
ssh -i ~/.ssh/azure_id_rsa ubuntu@13.91.99.206
```

## <a name="next-steps"></a>Дальнейшие действия
Вы быстро создали виртуальную машину Linux для использования в целях тестирования или демонстрации. Чтобы создать виртуальную машину Linux, настроенную для вашей инфраструктуры, выполните инструкции, приведенные в любой из следующих статей:

* [Развертывание виртуальных машин и управление ими с помощью шаблонов Azure Resource Manager и интерфейса командной строки Azure.](virtual-machines-linux-cli-deploy-templates.md)
* [Создание защищенной виртуальной машины Linux с помощью шаблона Azure.](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
* [Создание виртуальной машины Linux с нуля с помощью Azure CLI.](virtual-machines-linux-create-cli-complete.md)




<!--HONumber=Nov16_HO2-->


