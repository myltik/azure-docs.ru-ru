<properties 
	pageTitle="Обзор проверки XML и пакета интеграции Enterprise | Служба приложений Microsoft Azure | Microsoft Azure" 
	description="Узнайте, как функционирует проверка в пакете интеграции Enterprise и приложениях логики." 
	services="logic-apps" 
	documentationCenter=".net,nodejs,java"
	authors="msftman" 
	manager="erikre" 
	editor="cgronlun"/>

<tags 
	ms.service="logic-apps" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/08/2016" 
	ms.author="deonhe"/>

# Интеграция Enterprise с проверкой данных XML

## Обзор
Часто в сценариях "бизнес-бизнес" партнерам по соглашению необходимо проверять допустимость сообщений, которыми они обмениваются между собой, перед началом обработки данных. В пакете интеграции Enterprise можно использовать соединитель проверки XML, чтобы проверять документы с помощью предопределенной схемы.

## Как проверить документ с помощью соединителя проверки XML
1. Создайте приложение логики и [свяжите его с учетной записью интеграции](./app-service-logic-enterprise-integration-accounts.md "Узнайте, как связать учетную запись интеграции с приложением логики."), содержащей схему, которая будет использоваться для проверки данных XML.
2. Добавьте триггер **Request - When an HTTP request is received** (Запрос: при получении HTTP-запроса) в свое приложение логики. ![](./media/app-service-logic-enterprise-integration-xml/xml-1.png)
3. Добавьте действие **XML Validation** (Проверка XML), щелкнув **Добавить действие**.
4. Введите слово *xml* в поле поиска, чтобы отфильтровать все действия и оставить только те, которые вы хотите использовать.
5. Выберите действие **XML Validation** (Проверка XML). ![](./media/app-service-logic-enterprise-integration-xml/xml-2.png)
6. Выберите текстовое поле **СОДЕРЖИМОЕ**. ![](./media/app-service-logic-enterprise-integration-xml/xml-1-5.png)
7. Выберите тег body в качестве содержимого, которое следует проверять. ![](./media/app-service-logic-enterprise-integration-xml/xml-3.png)
8. Выберите поле списка **ИМЯ СХЕМЫ** и выберите схему, которую вы хотите использовать для проверки входного *содержимого*, указанного выше. ![](./media/app-service-logic-enterprise-integration-xml/xml-4.png)
9. Сохраните результаты своих действий. ![](./media/app-service-logic-enterprise-integration-xml/xml-5.png)

На этом настройка соединителя проверки завершена. В случае реального приложения может потребоваться сохранить проверенные данные в бизнес-приложении, например Salesforce. Можно легко добавить действие для отправки выходных данных проверки в Salesforce.

Теперь можно протестировать действие проверки, выполнив запрос к конечной точке HTTP.

## Дальнейшие действия

[Узнайте больше о пакете интеграции Enterprise](./app-service-logic-enterprise-integration-overview.md "Узнайте о пакете интеграции Enterprise.").

<!---HONumber=AcomDC_0803_2016-->