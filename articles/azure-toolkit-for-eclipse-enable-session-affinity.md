---
title: "Включение сходства сеансов с помощью набора средств Azure для Eclipse"
description: "Узнайте, как включить сходство сеансов с помощью набора средств Azure для Eclipse."
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 88c595ec-7d85-40bd-9078-8d6be7b3f0fa
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 04/14/2017
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: ab8623d6f9751ed6d71d9a5b1c0d5e939c442862
ms.lasthandoff: 04/22/2017


---
# <a name="enable-session-affinity"></a>Включение сходства сеансов
В наборе средств Azure для Eclipse вы можете включить для ролей сходство сеансов HTTP (называемое также прикрепленным сеансами). На следующем рисунке показано диалоговое окно свойств **Балансировка нагрузки** , используемое для включения функции сходства сеансов:

![][ic719492]

## <a name="to-enable-session-affinity-for-your-role"></a>Включение сходства сеансов для роли
1. Щелкните правой кнопкой мыши роль в обозревателе проектов Eclipse, выберите пункт **Azure**, а затем щелкните **Балансировка нагрузки**.

2. В диалоговом окне **Properties for WorkerRole1 Load Balancing** (Свойства для балансировки нагрузки WorkerRole1) выполните следующие действия.

   А. Установите флажок **Enable HTTP session affinity (sticky sessions) for this role**

   b. Для параметра **Input endpoint to use** (Используемая входная конечная точка) выберите используемую входную конечную точку, например **http (public:80, private:8080)**. Ваше приложение должно использовать эту конечную точку в качестве своей конечной точки HTTP. Вы можете включить для роли несколько конечных точек, однако лишь одну из них можно выбрать для поддержки прикрепленных сеансов.

   c. Перестройте приложение.

Если после включения используется несколько экземпляров роли, HTTP-запросы, поступающие от конкретного клиента, будут по-прежнему обрабатываться в том же экземпляре роли.

Набор средств Eclipse позволяет реализовать это путем установки специального модуля IIS — маршрутизация запросов приложений (ARR) — в каждом из экземпляров роли. Модуль ARR перенаправляет HTTP-запросы в соответствующий экземпляр роли. Набор средств автоматически перенастраивает выбранную конечную точку, чтобы входящий HTTP-трафик сначала перенаправлялся в программное обеспечение ARR. Набор средств также создает новую внутреннюю конечную точку, на прослушивание которой настроен ваш сервер Java. Это конечная точка, используемая ARR для перенаправления HTTP-трафика в соответствующий экземпляр роли. Таким образом, каждый экземпляр роли в развертывании с несколькими экземплярами служит в качестве обратного прокси-сервера для всех других экземпляров, реализуя функцию прикрепленных сеансов.

## <a name="notes-about-session-affinity"></a>Заметки о сходстве сеансов
* Сходство сеансов не работает в эмуляторе вычислений. Соответствующие параметры можно применить в эмуляторе вычислений, не затрагивая процесс сборки или работу эмулятора вычислений, однако сама эта функция в эмуляторе вычислений не работает.

* Включение сходства сеансов приведет к увеличению пространства на диске, занимаемого вашим развертыванием в Azure, так как будет скачано дополнительное программное обеспечение, устанавливаемое в экземплярах роли при запуске службы в облаке Azure.

* Инициализация каждой роли будет занимать больше времени.

* Добавляется внутренняя конечная точка, служащая для перенаправления трафика, как было указано выше.


## <a name="see-also"></a>См. также
[Набор средств Azure для Eclipse][Azure Toolkit for Eclipse]

[Создание приложения Hello World для Azure в Eclipse][Creating a Hello World Application for Azure in Eclipse]

[Установка набора средств Azure для Eclipse][Installing the Azure Toolkit for Eclipse] 

Дополнительные сведения об использовании Azure с Java можно найти в [Центре разработчиков Java для Azure][Azure Java Developer Center].

<!-- URL List -->

[Azure Java Developer Center]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Creating a Hello World Application for Azure in Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[How to Maintain Session Data with Session Affinity]: http://go.microsoft.com/fwlink/?LinkID=699539
[Installing the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic719492]: ./media/azure-toolkit-for-eclipse-enable-session-affinity/ic719492.png

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690950.aspx -->

