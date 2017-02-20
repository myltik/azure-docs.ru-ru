---
title: "Преобразование XML с помощью карт XSLT — Azure Logic Apps | Документация Майкрософт"
description: "Добавление карт XSLT для преобразования XML-данных с помощью Azure Logic Apps и пакета интеграции Enterprise"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: 90f5cfc4-46b2-4ef7-8ac4-486bb0e3f289
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: estfan
translationtype: Human Translation
ms.sourcegitcommit: 7729890157900d0211b3a7ec05096ca315018875
ms.openlocfilehash: 23d79fb83c1c4b103407c001dcafb8b1f3cfa5a7


---
# <a name="add-maps-for-xml-data-transform"></a>Добавление карт для преобразования данных XML

В интеграции Enterprise используются карты для преобразования данных XML из одного формата в другой. Карта — это документ XML, который определяет, какие данные в документе должны быть преобразованы в другой формат. 

## <a name="why-use-maps"></a>Для чего используются карты?

Предположим, что вы регулярно получаете заказы или счета от клиентов "бизнес — бизнес", которые используют формат даты ГГГММДД. Однако в вашей организации даты хранятся в формате ММДДГГГ. Вы можете использовать карту, чтобы *преобразовать* формат даты ГГГММДД в ММДДГГГ перед сохранением сведений о заказе или счете в базе данных деловой активности клиента.

## <a name="how-do-i-create-a-map"></a>Как создать карту?

Вы можете создать проекты интеграции BizTalk с помощью [пакета интеграции Enterprise](logic-apps-enterprise-integration-overview.md "Узнайте о пакете интеграции Enterprise") для Visual Studio 2015. Затем создайте файл карты интеграции, чтобы визуально сопоставить элементы между двумя XML-файлами схем. После создания этого проекта вы получите документ XSLT.

## <a name="how-do-i-add-a-map"></a>Добавление карты

1. На портале Azure щелкните **Обзор**.

    ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)

2. Введите **интеграция** в поле фильтра поиска, затем выберите **Учетные записи интеграции** в списке результатов.

    ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)

3. Выберите учетную запись интеграции, в которую необходимо добавить карту.

    ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)

4. Выберите плитку **Карты** .

    ![](./media/logic-apps-enterprise-integration-maps/map-1.png)

5. Когда откроется колонка карт, нажмите кнопку **Добавить**.

    ![](./media/logic-apps-enterprise-integration-maps/map-2.png)  

6. Введите **имя** карты. Чтобы передать файл карты, щелкните значок папки в правой части текстового поля **Карта**. После завершения передачи нажмите кнопку **ОК**.

    ![](./media/logic-apps-enterprise-integration-maps/map-3.png)

7. Когда Azure добавит карту в вашу учетную запись интеграции, вы увидите сообщение о том, что файл добавлен или не добавлен. Получив это сообщение, выберите плитку **Карты**, чтобы просмотреть добавленную карту.

    ![](./media/logic-apps-enterprise-integration-maps/map-4.png)

## <a name="how-do-i-edit-a-map"></a>Изменение карты

Передайте новый файл карты с нужными изменениями. Сначала можно загрузить карту и изменить ее.

Чтобы отправить новую карту, которая заменит существующую, выполните следующие действия.

1. Выберите плитку **Карты** .

2. В открывшейся колонке карт выберите карту, которую вы хотите изменить.

3. В колонке **Карты** нажмите кнопку **Обновить**.

    ![](./media/logic-apps-enterprise-integration-maps/edit-1.png)

4. В окне выбора файлов выберите файл карты, который требуется отправить, а затем нажмите кнопу **Открыть**.

    ![](./media/logic-apps-enterprise-integration-maps/edit-2.png)

## <a name="how-to-delete-a-map"></a>Как удалить карту?

1. Выберите плитку **Карты** .

2. В открывшейся колонке карт выберите карту, которую вы хотите удалить.

3. Щелкните **Удалить**.

    ![](./media/logic-apps-enterprise-integration-maps/delete.png)

4. Подтвердите, что вы действительно хотите удалить карту.

    ![](./media/logic-apps-enterprise-integration-maps/delete-confirmation-1.png)

## <a name="next-steps"></a>Дальнейшие действия
* [Обзор пакета интеграции Enterprise](logic-apps-enterprise-integration-overview.md "Обзор пакета интеграции Enterprise")  
* [Узнайте о соглашениях и Пакете интеграции Enterprise](../logic-apps/logic-apps-enterprise-integration-agreements.md "Узнайте о соглашениях и Пакете интеграции Enterprise")  
* [Интеграция Enterprise с преобразованием данных XML](logic-apps-enterprise-integration-transform.md "Интеграция Enterprise с преобразованием данных XML")  




<!--HONumber=Feb17_HO1-->


