---
title: "Управление виртуальными машинами с помощью Azure Explorer для Eclipse | Документация Майкрософт"
description: "Вы можете узнать, как управлять виртуальными машинами Azure с помощью Azure Explorer для Eclipse."
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 04/14/2017
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: f8631c5fe566762862f64b47843e154b68bb8031
ms.lasthandoff: 04/22/2017


---

# <a name="managing-virtual-machines-using-the-azure-explorer-for-eclipse"></a>Управление виртуальными машинами с помощью Azure Explorer для Eclipse

Azure Explorer, входящий в состав набора средств Azure для Eclipse, предоставляет разработчикам на Java удобное решение для управления виртуальными машинами в учетной записи Azure из интегрированной среды разработки Eclipse.

[!INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

[!INCLUDE [azure-toolkit-for-eclipse-show-azure-explorer](../includes/azure-toolkit-for-eclipse-show-azure-explorer.md)]

## <a name="creating-a-virtual-machine-in-eclipse"></a>Создание виртуальной машины в Eclipse

Приведенные ниже инструкции помогут вам поэтапно создать виртуальную машину с использованием Azure Explorer.

1. Войдите в свою учетную запись Azure, следуя инструкциям из статьи [Инструкции по входу для набора средств Azure для Eclipse].

1. В представлении **Azure Explorer** разверните узел **Azure**, щелкните правой кнопкой мыши **Виртуальные машины** и выберите **Создать виртуальную машину**.
   ![Меню "Создать виртуальную машину"][CR01]

1. При отображении мастера **Создание виртуальной машины** выберите свою подписку и нажмите кнопку **Далее**.
   ![Мастер создания виртуальной машины][CR02]

1. На следующем экране мастера укажите приведенные ниже параметры и нажмите кнопку **Далее**.  ![Мастер создания виртуальной машины][CR03]

   а. **Расположение**: указывает расположение для создания виртуальной машины, например westus.

   b. **Издатель**: указывает издателя, создавшего образ, который вы используете для создания виртуальной машины, например Microsoft.

   c. **Предложение**: определяет, какое предложение виртуальной машины выбранного издателя нужно использовать, например JDK.

   г) **SKU**: указывает нужный *номер SKU* из выбранного предложения, например JDK_8.

   д. **Версия #**: указывает, какую версию из выбранного номера SKU нужно использовать.

1. На следующем экране мастера укажите приведенные ниже параметры и нажмите кнопку **Далее**. ![Мастер создания виртуальной машины][CR04]

   а. **Имя виртуальной машины**: указывает имя новой виртуальной машины, которое должно начинаться с буквы и содержать только буквы, цифры и дефисы.

   b. **Размер**: указывает количество ядер и объем памяти, выделяемые для виртуальной машины.

   c. **Имя пользователя**: указывает учетную запись администратора, создаваемую для управления виртуальной машиной.

   г) **Пароль** и **Подтверждение**: указывают пароль для учетной записи администратора.

1. На последнем экране мастера укажите следующие параметры.

   а. **Группа ресурсов**: указывает группу ресурсов для виртуальной машины. Нужно выбрать один из следующих параметров:
      * **Создать**: указывает, что нужно создать группу ресурсов.
      * **Использовать существующий**: указывает, что вы выберете группу ресурсов, связанную с учетной записью Azure.

   b. **Учетная запись хранения**: указывает учетную запись хранения, используемую для хранения виртуальной машины. Можно выбрать существующую учетную запись или создать новую. При выборе элемента **&lt;&lt;Создать&gt;&gt;** отображается следующее диалоговое окно.

      ![Диалоговое окно "Создать учетную запись хранения"][CR05]

   c. **Виртуальная сеть** и **Подсеть**: указывают виртуальную сеть и подсеть, к которым будет подключена виртуальная машина. Можно выбрать существующую сеть и подсеть или создать новые. При выборе элемента **&lt;&lt;Создать&gt;&gt;** отображается следующее диалоговое окно.<br/>

      ![Диалоговое окно "Создание виртуальной сети"][CR06]

   d. **Общедоступный IP-адрес**: указывает внешний IP-адрес для виртуальной машины. Можно создать IP-адрес или выбрать значение **(Нет)**, если у виртуальной машины не будет общедоступного IP-адреса.

   д. **Группа безопасности сети**: указывает необязательный брандмауэр, используемый виртуальной машиной. Можно выбрать существующий брандмауэр или задать значение **(Нет)**, чтобы не использовать брандмауэр.

   f. **Группа доступности**: указывает необязательную группу доступности, в которую может входить виртуальная машина. Можно выбрать существующую группу доступности, создать новую или задать значение **(Нет)**, если виртуальная машина не будет входить в группу доступности.

1. Указав все перечисленные выше параметры, нажмите кнопку **Готово**. ![Мастер создания виртуальной машины][CR07]

1. После выполнения описанных выше действий новая виртуальная машина появится в окне инструмента Azure Explorer.
   ![Новая виртуальная машина][CR08]

## <a name="restarting-a-virtual-machine-in-eclipse"></a>Перезапуск виртуальной машины в Eclipse

Чтобы перезапустить виртуальную машину с помощью Azure Explorer в Eclipse, выполните следующее.

1. В представлении **Azure Explorer** щелкните правой кнопкой мыши виртуальную машину и выберите **Перезапустить**.
   ![Перезапуск виртуальной машины][RE01]

1. При появлении запроса выберите **Да**, чтобы перезапустить виртуальную машину.
   ![Перезапуск виртуальной машины][RE02]

## <a name="shutting-down-a-virtual-machine-in-eclipse"></a>Завершение работы виртуальной машины в Eclipse

Чтобы завершить работу виртуальной машины с помощью Azure Explorer в Eclipse, выполните следующее.

1. В представлении **Azure Explorer** щелкните правой кнопкой мыши виртуальную машину и выберите **Завершить работу**.
   ![Завершение работы виртуальной машины][SH01]

1. При появлении запроса выберите **Да**, чтобы завершить работу виртуальной машины.
   ![Завершение работы виртуальной машины][SH02]

## <a name="deleting-a-virtual-machine-in-eclipse"></a>Удаление виртуальной машины в Eclipse

Чтобы удалить виртуальную машину с помощью Azure Explorer в Eclipse, выполните следующее.

1. В представлении **Azure Explorer** щелкните правой кнопкой мыши виртуальную машину и выберите **Удалить**.
   ![Удаление виртуальной машины][DE01]

1. При появлении запроса выберите **Да**, чтобы удалить виртуальную машину.
   ![Удаление виртуальной машины][DE02]

## <a name="see-also"></a>См. также
Дополнительные сведения о размерах виртуальных машин Azure и ценах на них можно получить, перейдя по следующим ссылкам:

* Размеры виртуальных машин Azure
  * [Размеры виртуальных машин Windows в Azure]
  * [Размеры виртуальных машин Linux в Azure]
* Цены на виртуальные машины Azure
  * [Цены на виртуальные машины Windows]
  * [Цены на виртуальные машины Linux]

Дополнительные сведения о наборах средств Azure для Java IDE см. по следующим ссылкам:

* [Набор средств Azure для Eclipse]
  * [Новые возможности набора средств Azure для Eclipse]
  * [Установка набора средств Azure для Eclipse]
  * [Инструкции по входу для набора средств Azure для Eclipse]
  * [Создание веб-приложения Hello World для Azure в Eclipse]
* [Набор средств Azure для IntelliJ]
  * [Новые возможности набора средств Azure для IntelliJ]
  * [Установка набора средств Azure для IntelliJ]
  * [Инструкции по входу для набора средств Azure для IntelliJ]
  * [Создание веб-приложения Hello World для Azure в IntelliJ]

Дополнительные сведения об использовании Azure см. в [центре разработчиков Java для Azure] и на странице [инструментов Java для Visual Studio Team Services].

<!-- URL List -->

[Набор средств Azure для Eclipse]: ./azure-toolkit-for-eclipse.md
[Набор средств Azure для IntelliJ]: ./azure-toolkit-for-intellij.md
[Создание веб-приложения Hello World для Azure в Eclipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Создание веб-приложения Hello World для Azure в IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Установка набора средств Azure для Eclipse]: ./azure-toolkit-for-eclipse-installation.md
[Установка набора средств Azure для IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[Инструкции по входу для набора средств Azure для Eclipse]: ./azure-toolkit-for-eclipse-sign-in-instructions.md
[Инструкции по входу для набора средств Azure для IntelliJ]: ./azure-toolkit-for-intellij-sign-in-instructions.md
[Новые возможности набора средств Azure для Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[Новые возможности набора средств Azure для IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[центре разработчиков Java для Azure]: https://azure.microsoft.com/develop/java/
[инструментов Java для Visual Studio Team Services]: https://java.visualstudio.com/ (Инструменты Java для Visual Studio Team Services)

[Размеры виртуальных машин Windows в Azure]: /azure/virtual-machines/virtual-machines-windows-sizes
[Размеры виртуальных машин Linux в Azure]: /azure/virtual-machines/virtual-machines-linux-sizes
[Цены на виртуальные машины Windows]: /pricing/details/virtual-machines/windows/
[Цены на виртуальные машины Linux]: /pricing/details/virtual-machines/linux/

<!-- IMG List -->

[RE01]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/RE01.png
[RE02]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/RE02.png

[SH01]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/SH01.png
[SH02]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/SH02.png

[DE01]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/DE01.png
[DE02]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/DE02.png

[CR01]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/CR01.png
[CR02]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/CR02.png
[CR03]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/CR03.png
[CR04]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/CR04.png
[CR05]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/CR05.png
[CR06]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/CR06.png
[CR07]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/CR07.png
[CR08]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/CR08.png

