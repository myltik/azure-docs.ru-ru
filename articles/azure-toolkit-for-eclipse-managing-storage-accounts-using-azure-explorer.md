---
title: "Управление учетными записями хранения с помощью Azure Explorer для Eclipse | Документы Майкрософт"
description: "Вы можете узнать, как управлять учетными записями хранения Azure с помощью Azure Explorer для Eclipse."
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
ms.openlocfilehash: 3c95ddde2909cd8dee055724ba01f22647627bd3
ms.lasthandoff: 04/22/2017


---

# <a name="managing-storage-accounts-using-the-azure-explorer-for-eclipse"></a>Управление учетными записями хранения с помощью Azure Explorer для Eclipse

Azure Explorer, входящий в состав набора средств Azure для Eclipse, предоставляет разработчикам на Java удобное решение для управления учетными записями хранения в их учетной записи Azure из интегрированной среды разработки Eclipse.

[!INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

[!INCLUDE [azure-toolkit-for-eclipse-show-azure-explorer](../includes/azure-toolkit-for-eclipse-show-azure-explorer.md)]

## <a name="creating-a-storage-account-in-eclipse"></a>Создание учетной записи хранения в Eclipse

Следующие инструкции помогут вам поэтапно создать учетную запись хранения с использованием Azure Explorer.

1. Войдите в свою учетную запись Azure, следуя инструкциям из статьи [Инструкции по входу для набора средств Azure для Eclipse].

1. В представлении **Azure Explorer** разверните узел **Azure**, щелкните правой кнопкой мыши элемент **Учетные записи хранения** и выберите **Создать учетную запись хранения**.
   ![Меню "Создать учетную запись хранения"][CS01]

1. При отображении диалогового окна **Создать учетную запись хранения** укажите следующие параметры: ![Диалоговое окно "Создать учетную запись хранения"][CS02]

   а. **Имя**: указывает имя для новой учетной записи хранения.

   b. **Подписка**: указывает подписку Azure, которую нужно использовать для новой учетной записи хранения.

   c. **Группа ресурсов**: указывает группу ресурсов для виртуальной машины, нужно выбрать один из следующих параметров:
      * **Создать**: указывает, что нужно создать группу ресурсов.
      * **Использовать имеющийся**: указывает, что вы выберете группу ресурсов из списка, сопоставленного с учетной записью Azure.

   d. **Регион**: указывает расположение для создания учетной записи хранения, например "западная часть США".

   д. **Тип учетной записи**: указывает тип создаваемой учетной записи хранения, например "хранилище BLOB-объектов". (Дополнительные сведения см. в статье [Об учетных записях хранения Azure].)

   f. **Производительность**: определяет, какое предложение по учетной записи хранения выбранного издателя нужно использовать, например "Премиум". (Дополнительные сведения см. в статье [Целевые показатели масштабируемости и производительности службы хранилища Azure].)

   g. **Репликация**: указывает репликацию для учетной записи хранения, например "избыточная в пределах зоны". (Дополнительные сведения см. в статье [Репликация службы хранилища Azure].)

1. После указания всех перечисленных выше параметров нажмите кнопку **Создать**.

## <a name="creating-a-storage-container-in-eclipse"></a>Создание контейнера хранилища в Eclipse

Следующие инструкции помогут вам поэтапно создать контейнер хранилища с использованием Azure Explorer.

1. В Azure Explorer щелкните правой кнопкой мыши учетную запись хранения, для которой нужно создать контейнер, а затем нажмите кнопку **Создать контейнер BLOB-объектов**.
   ![Меню "Создание контейнера хранилища"][CC01]

1. При отображении диалогового окна **Создать контейнер BLOB-объектов** укажите имя для своего контейнера и нажмите кнопку **ОК**. (Дополнительные сведения об именовании контейнеров хранилища см. в статье [Именование контейнеров, больших двоичных объектов и метаданных и ссылка на них].) ![Диалоговое окно "Создание контейнера хранилища"][CC02]

## <a name="deleting-a-storage-container-in-eclipse"></a>Удаление контейнера хранилища в Eclipse

Чтобы удалить контейнер хранилища с помощью Azure Explorer, выполните следующие действия:

1. В Azure Explorer щелкните правой кнопкой мыши контейнер хранилища и выберите **Удалить**.
   ![Меню "Удаление контейнера хранилища"][DC01]

1. Нажмите кнопку **ОК** при появлении запроса на удаление контейнера хранилища.
   ![Диалоговое окно "Удаление контейнера хранилища"][DC02]

## <a name="deleting-a-storage-account-in-eclipse"></a>Удаление учетной записи хранения в Eclipse

Чтобы удалить учетную запись хранения с помощью Azure Explorer, выполните следующие действия:

1. В представлении **Azure Explorer** щелкните правой кнопкой мыши учетную запись хранения и выберите пункт **Удалить**.
   ![Меню "Удалить учетную запись хранения"][DS01]

1. Нажмите кнопку **ОК** при появлении запроса на удаление учетной записи хранения.
   ![Диалоговое окно "Удалить учетную запись хранения"][DS02]

## <a name="see-also"></a>См. также
Дополнительные сведения об учетных записях хранения, размерах и ценах см. по следующим ссылкам:

* [Введение в службу хранилища Microsoft Azure]
* [Об учетных записях хранения Azure]
* Размеры учетных записей хранения Azure
  * [Размеры учетных записей хранения Windows в Azure]
  * [Размеры учетных записей хранения Linux в Azure]
* Цены на учетные записи хранения Azure
  * [Цены на учетные записи хранения Windows]
  * [Цены на учетные записи хранения Linux]

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

[Введение в службу хранилища Microsoft Azure]: /azure/storage/storage-introduction
[Об учетных записях хранения Azure]: /azure/storage/storage-create-storage-account
[Репликация службы хранилища Azure]: /azure/storage/storage-redundancy
[Целевые показатели масштабируемости и производительности службы хранилища Azure]: /azure/storage/storage-scalability-targets
[Именование контейнеров, больших двоичных объектов и метаданных и ссылка на них]: http://go.microsoft.com/fwlink/?LinkId=255555

[Размеры учетных записей хранения Windows в Azure]: /azure/virtual-machines/virtual-machines-windows-sizes
[Размеры учетных записей хранения Linux в Azure]: /azure/virtual-machines/virtual-machines-linux-sizes
[Цены на учетные записи хранения Windows]: /pricing/details/virtual-machines/windows/
[Цены на учетные записи хранения Linux]: /pricing/details/virtual-machines/linux/

<!-- IMG List -->

[CS01]: ./media/azure-toolkit-for-eclipse-managing-storage-accounts-using-azure-explorer/CS01.png
[CS02]: ./media/azure-toolkit-for-eclipse-managing-storage-accounts-using-azure-explorer/CS02.png
[CC01]: ./media/azure-toolkit-for-eclipse-managing-storage-accounts-using-azure-explorer/CC01.png
[CC02]: ./media/azure-toolkit-for-eclipse-managing-storage-accounts-using-azure-explorer/CC02.png

[DS01]: ./media/azure-toolkit-for-eclipse-managing-storage-accounts-using-azure-explorer/DS01.png
[DS02]: ./media/azure-toolkit-for-eclipse-managing-storage-accounts-using-azure-explorer/DS02.png
[DC01]: ./media/azure-toolkit-for-eclipse-managing-storage-accounts-using-azure-explorer/DC01.png
[DC02]: ./media/azure-toolkit-for-eclipse-managing-storage-accounts-using-azure-explorer/DC02.png

