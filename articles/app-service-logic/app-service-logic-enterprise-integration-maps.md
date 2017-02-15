---
title: "Обзор карт и Пакета интеграции Enterprise | Документация Майкрософт"
description: "Узнайте, как использовать карты с пакетом интеграции Enterprise и приложениями логики."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: cgronlun
ms.assetid: 90f5cfc4-46b2-4ef7-8ac4-486bb0e3f289
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 542278b38584d8e87a5dbf88af020dee3941cfa1


---
# <a name="learn-about-maps-and-the-enterprise-integration-pack"></a>Узнайте о картах и пакете интеграции Enterprise
## <a name="overview"></a>Обзор
В интеграции Enterprise карты используются для преобразования данных XML из одного формата в другой. 

## <a name="what-is-a-map"></a>Что такое карта?
Карта — это документ XML, который определяет, какие данные в документе должны быть преобразованы в другой формат. 

## <a name="why-use-maps"></a>Для чего используются карты?
Предположим, что вы регулярно получаете заказы или счета от клиентов "бизнес-бизнес", которые используют формат даты ГГГММДД. Однако в вашей организации даты хранятся в формате ММДДГГГ. Вы можете использовать карту, чтобы *преобразовать* формат даты ГГГММДД в ММДДГГГ перед сохранением сведений о заказе или счете в базе данных деловой активности клиента.

## <a name="how-do-i-create-a-map"></a>Как создать карту?
[Пакет интеграции Enterprise](app-service-logic-enterprise-integration-overview.md "Узнайте о Пакете интеграции Enterprise") для Visual Studio 2015 позволяет создавать проекты Biztalk Integration.  Создание файла карты интеграции позволяет визуально сопоставить элементы между двумя XML-файлами схем.  После выполнения сборки этого проекта создается документ в формате XSLT.

## <a name="how-to-upload-a-map"></a>Как передать карту?
На портале Azure выполните следующие действия.  

1. Щелкните **Обзор**.  
   ![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)    
2. Введите **integration** в поле фильтра поиска и выберите **Учетные записи интеграции** из списка результатов.     
   ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)  
3. Выберите **учетную запись интеграции**, в которую необходимо добавить карту.  
   ![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)  
4. Выберите элемент **Карты** .  
   ![](./media/app-service-logic-enterprise-integration-maps/map-1.png)  
5. В открывшейся колонке "Карты" нажмите кнопку **Добавить**.  
   ![](./media/app-service-logic-enterprise-integration-maps/map-2.png)  
6. Введите **имя** для карты в соответствующем поле. Чтобы передать файл карты, щелкните значок папки в правой части текстового поля **Карта**. После завершения передачи нажмите кнопку **ОК**.  
   ![](./media/app-service-logic-enterprise-integration-maps/map-3.png)  
7. Карта будет добавлена в учетную запись интеграции. На экране появится уведомление об успешном выполнении или сбое при добавлении файла карты. После получения уведомления выберите элемент **Карты**. Вы увидите в колонке "Карты" только что добавленную карту.    
   ![](./media/app-service-logic-enterprise-integration-maps/map-4.png)  

## <a name="how-to-edit-a-map"></a>Как изменить карту?
Чтобы изменить карту, необходимо передать новый файл карты с нужными изменениями. Можно сначала загрузить карту и изменить ее. 

Выполните следующие действия, чтобы отправить новую карту, которая заменит существующую.  

1. Выберите элемент **Карты** .  
2. В открывшейся колонке "Maps" (Карты) выберите карту, которую вы хотите изменить.  
3. В колонке **Карты** щелкните ссылку **Обновить**.  
   ![](./media/app-service-logic-enterprise-integration-maps/edit-1.png)   
4. Выберите файл карты, который следует передать, используя появившееся диалоговое окно выбора файлов, затем щелкните **Открыть** в средстве выбора файлов.   
   ![](./media/app-service-logic-enterprise-integration-maps/edit-2.png)   
5. После передачи карты отобразится всплывающее уведомление.    

## <a name="how-to-delete-a-map"></a>Как удалить карту?
1. Выберите элемент **Карты** .  
2. В открывшейся колонке "Maps" (Карты) выберите карту, которую вы хотите удалить.  
3. Щелкните ссылку **Удалить**.    
   ![](./media/app-service-logic-enterprise-integration-maps/delete.png)   
4. Подтвердите, что действительно собираетесь удалить карту.  
   ![](./media/app-service-logic-enterprise-integration-maps/delete-confirmation-1.png)   

## <a name="next-steps"></a>Дальнейшие действия
* [Обзор пакета интеграции Enterprise](app-service-logic-enterprise-integration-overview.md "Обзор пакета интеграции Enterprise")  
* [Узнайте о соглашениях и Пакете интеграции Enterprise](app-service-logic-enterprise-integration-agreements.md "Узнайте о соглашениях и Пакете интеграции Enterprise")  
* [Интеграция Enterprise с преобразованием данных XML](app-service-logic-enterprise-integration-transform.md "Интеграция Enterprise с преобразованием данных XML")  




<!--HONumber=Nov16_HO3-->


