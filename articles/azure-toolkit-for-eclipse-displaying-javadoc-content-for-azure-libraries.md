---
title: "Отображение в Eclipse содержимого Javadoc для пакета библиотек Azure для Java"
description: "Узнайте, как отобразить содержимое Javadoc для библиотек Azure в Eclipse."
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 30f8b6a1-1d76-4d1c-861b-1db478c46e6b
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 11/01/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a0b0de62a891b579875edbb687e013316c491527


---
# <a name="displaying-javadoc-content-in-eclipse-for-the-azure-libraries-package-for-java"></a>Отображение в Eclipse содержимого Javadoc для пакета библиотек Azure для Java
Содержимое Javadoc для библиотек Azure для Java можно просмотреть в среде Eclipse, сопоставив его с библиотеками Azure для Java. Ниже показано, как использовать эту функциональность в Eclipse.

В данной процедуре подразумевается, что вы уже добавили библиотеку Azure для Java в путь сборки.

## <a name="to-display-javadoc-content-in-eclipse-for-the-azure-libraries-for-java"></a>Порядок отображения в Eclipse содержимого Javadoc для библиотек Azure для Java
* В разделе проекта **Referenced Libraries** (Ссылки на библиотеки) обозревателя проектов Eclipse откройте контекстное меню для JAR-файла библиотеки Azure для Java. Например, **microsoft windowsazure-api 0.1.0.jar** (номер версии может отличаться в зависимости от установленной версии).
* Щелкните **Свойства**.
* В левой области диалогового окна **Свойства** щелкните **Javadoc Location** (Расположение Javadoc). Отображается диалоговое окно **Javadoc Location** (Расположение Javadoc).
* Вы можете указать **Javadoc URL** (URL-адрес Javadoc) или **Javadoc in archive** (Javadoc в архиве) в соответствующих полях.
  * Если вы решите указать URL-адрес Javadoc в поле **Javadoc URL** (URL-адрес Javadoc), используйте такие URL-адреса, как **http://dl.windowsazure.com/javadoc** или **http://dl.windowsazure.com/storage/javadoc**.
  * Если вы решите использовать **Javadoc in archive**(Javadoc в архиве), можете указать внешний файл или файл рабочей области.
    Выберите нужный вариант и при необходимости выполните переход или проверку. В следующем примере библиотеки Azure для Java сопоставляются с соответствующим JAR-файлом Javadoc, скачанным в локальную папку с именем **c:\MyAzureJARs**.
    ![][ic553487]
* *Необязательно*: щелкните **Validate**(Проверить). При этом могут отображаться потенциальные проблемы с JAR-файлом Javadoc.
* Нажмите кнопку **ОК**.

После сопоставления с библиотекой содержимое Javadoc должно отображаться в Eclipse IDE. Например, если `blob` определен в коде с типом `CloudBlockBlob`, ниже приведен пример содержимого Javadoc, которое появляется при вводе `blob.acquireLease` в коде:

![][ic553488]

## <a name="see-also"></a>См. также
[Набор средств Azure для Eclipse][Набор средств Azure для Eclipse]

[Создание приложения Hello World для Azure в Eclipse][Создание приложения Hello World для Azure в Eclipse]

[Установка набора средств Azure для Eclipse][Установка набора средств Azure для Eclipse] 

Дополнительные сведения об использовании Azure с Java можно найти в [центре разработчиков Java для Azure][Центре разработчиков Java для Azure].

<!-- URL List -->

[Центре разработчиков Java для Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Набор средств Azure для Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Создание приложения Hello World для Azure в Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Установка набора средств Azure для Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic553487]: ./media/azure-toolkit-for-eclipse-displaying-javadoc-content-for-azure-libraries/ic553487.png
[ic553488]: ./media/azure-toolkit-for-eclipse-displaying-javadoc-content-for-azure-libraries/ic553488.png

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh698319.aspx -->



<!--HONumber=Nov16_HO3-->


