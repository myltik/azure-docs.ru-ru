---
title: "Конечные точки службы Azure"
description: "Описываются параметры конечных точек службы Azure в наборе средств Azure для Eclipse."
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 9c6125ec-7278-461e-b69c-ed56e844f742
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 04/14/2017
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: ff60ebaddd3a7888cee612f387bd0c50799496ac
ms.openlocfilehash: 9f664b07566fe78d93b1e35a2e0f7e5513960fad
ms.lasthandoff: 01/05/2017


---
# <a name="azure-service-endpoints"></a>Конечные точки службы Azure
Конечные точки службы определяют, где будет развернуто приложение и где будет управляться: на глобальной платформе Azure, на Azure под управлением 21Vianet в Китае или на частной платформе Azure. Диалоговое окно **Конечные точки службы** позволяет указать, какие конечные точки службы вы хотите использовать. Чтобы открыть диалоговое окно **Конечные точки службы** в Eclipse, щелкните **Окно**, **Настройки**, разверните **Azure** и выберите **Конечные точки службы**. Указав значение в поле **Active Set** (Активный набор), можно определить, какие конечные точки службы Azure будут использоваться для проектов Azure в текущей рабочей области.

Ниже приведено диалоговое окно **Конечные точки службы** .

![][ic719493]

## <a name="to-set-the-service-endpoints"></a>Задание конечных точек службы
В диалоговом окне **Конечные точки службы** выполните одно из следующих действий.

* Если вы хотите использовать глобальную платформу Azure, в раскрывающемся списке **Active Set** (Активный набор) выберите **windowsazure.com** и нажмите кнопку **ОК**.

* Если вы хотите использовать платформу Azure под управлением 21Vianet в Китае, в раскрывающемся списке **Active Set** (Активный набор) выберите **windowsazure.cn (China)** и нажмите кнопку **ОК**.

* Если вы хотите использовать частную платформу Azure:

  1. Нажмите кнопку **Изменить**.

  2. Открывается диалоговое окно с информацией о том, что диалоговое окно **Конечные точки службы** будет закрыто и будет открыт файл наборов настроек. Нажмите кнопку **ОК**.

  3. В файле preferencesets.xml создайте новый элемент `preferenceset`. Для этого нового элемента создайте атрибуты `name`, `blob`, `management`, `portalURL` и `publishsettings` и добавьте для них значения, соответствующие вашей частной платформе Azure. Вы можете использовать значения, предоставленные для существующих элементов `preferenceset`, в качестве шаблона. **Примечание.** Используемое для атрибута `blob` значение должно содержать текст blob в URL-адресе.

  4. Сохраните и закройте файл preferencesets.xml.

  5. Снова откройте диалоговое окно **Конечные точки службы** .

  6. В раскрывающемся списке **Active Set** (Активный набор) выберите созданный активный набор и нажмите кнопку **ОК**.

  7. После создания элемента `preferenceset` частной платформы Azure можно изменить назначенные ему значения, нажав кнопку **Изменить** в диалоговом окне **Конечные точки службы**. Кроме того, при необходимости вы можете создать несколько элементов `preferenceset` частной платформы Azure.

## <a name="see-also"></a>См. также
[Набор средств Azure для Eclipse][Azure Toolkit for Eclipse]

[Установка набора средств Azure для Eclipse][Installing the Azure Toolkit for Eclipse] 

[Создание приложения Hello World для Azure в Eclipse][Creating a Hello World Application for Azure in Eclipse]

Дополнительные сведения об использовании Azure с Java можно найти в [Центре разработчиков Java для Azure][Azure Java Developer Center].

<!-- URL List -->

[Azure Java Developer Center]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Creating a Hello World Application for Azure in Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installing the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic719493]: ./media/azure-toolkit-for-eclipse-azure-service-endpoints/ic719493.png

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/dn268600.aspx -->

