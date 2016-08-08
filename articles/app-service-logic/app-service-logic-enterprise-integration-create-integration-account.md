<properties 
	pageTitle="Обзор учетных записей интеграции и пакета интеграции Enterprise | Служба приложений Microsoft Azure | Microsoft Azure" 
	description="Узнайте все об учетных записях интеграции, пакете интеграции Enterprise и приложениях логики." 
	services="app-service\logic" 
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

# Обзор учетных записей интеграции

## Что такое учетная запись интеграции?
Учетная запись интеграции — это учетная запись Azure, позволяющая приложениям интеграции Enterprise управлять артефактами, включая схемы, карты, сертификаты, партнеры и соглашения. Любому создаваемому вами приложению интеграции потребуется учетная запись интеграции, например, для доступа к схеме, карте или сертификату.

## Создание учетной записи интеграции 
1. Щелкните **Обзор**. ![](./media/app-service-logic-enterprise-integration-accounts/account-1.png)
2. Введите **integration** в поле фильтра поиска и выберите **Integration Accounts** (Учетные записи интеграции) из списка результатов. ![](./media/app-service-logic-enterprise-integration-accounts/account-2.png)
3. В меню в верхней части страницы нажмите кнопку *Добавить*. ![](./media/app-service-logic-enterprise-integration-accounts/account-3.png)
4. Введите **имя**, выберите **подписку**, которую вы хотите использовать, создайте новую **группу ресурсов** или выберите существующую, выберите **расположение** для размещения учетной записи интеграции, выберите **ценовую категорию**, а затем нажмите кнопку **Создать**.

  На этом этапе в выбранном расположении будет подготовлена учетная запись интеграции. На это потребуется не больше 1 минуты. ![](./media/app-service-logic-enterprise-integration-accounts/account-4.png)
5. Обновите страницу. Вы увидите новую учетную запись интеграции в списке. Поздравляем! ![](./media/app-service-logic-enterprise-integration-accounts/account-5.png)

## Как связать учетную запись интеграции с приложением логики
Чтобы предоставить приложению логики доступ к картам, схемам, соглашениям и другим артефактам, которые находятся в вашей учетной записи интеграции, необходимо сначала связать ее с этим приложением логики.

### Ниже приведены инструкции о том, как связать учетную запись интеграции с приложением логики. 

#### Предварительные требования
- Учетная запись интеграции.
- Приложение логики.

>[AZURE.NOTE]Прежде чем начать, убедитесь, что учетная запись интеграции и приложение логики находятся в **одном расположении Azure**.

1. Щелкните ссылку **Параметры** в меню приложения логики. ![](./media/app-service-logic-enterprise-integration-accounts/linkaccount-1.png)
2. В колонке "Параметры" выберите элемент **Integration Account** (Учетная запись интеграции). ![](./media/app-service-logic-enterprise-integration-accounts/linkaccount-2.png)
3. Выберите учетную запись интеграции, которую необходимо связать с приложением логики, из раскрывающегося списка **Select an Integration account** (Выбор учетной записи интеграции). ![](./media/app-service-logic-enterprise-integration-accounts/linkaccount-3.png)
4. Сохраните результаты своих действий. ![](./media/app-service-logic-enterprise-integration-accounts/linkaccount-4.png)
5. Вы увидите уведомление о том, что учетная запись интеграции связана с вашим приложением логики, а все артефакты в вашей учетной записи интеграции доступны для этого приложения логики. ![](./media/app-service-logic-enterprise-integration-accounts/linkaccount-5.png)

Теперь, когда учетная запись интеграции связана с приложением логики, можно перейти в приложение логики и использовать для создания приложений с функциями "бизнес-бизнес" такие соединители "бизнес-бизнес", как проверка XML, кодирование и декодирование неструктурированных файлов и преобразование.
    
## Как удалить учетную запись интеграции?
1. Щелкните **Обзор**. ![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)
2. Введите **integration** в поле фильтра поиска и выберите **Integration Accounts** (Учетные записи интеграции) из списка результатов. ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)
3. Выберите **учетную запись интеграции**, которую нужно удалить. ![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)
4. Щелкните ссылку **Удалить**, которая находится в меню. ![](./media/app-service-logic-enterprise-integration-accounts/delete.png)
5. Подтвердите свой выбор.

## Как переместить учетную запись интеграции?
Можно легко переместить учетную запись интеграции в новую подписку и новую группу ресурсов. Если необходимо переместить учетную запись интеграции, выполните следующие действия.

>[AZURE.IMPORTANT] После перемещения учетной записи интеграции потребуется обновить все сценарии, чтобы в них использовались новые идентификаторы ресурсов.

1. Щелкните **Обзор**.![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)
2. Введите **integration** в поле фильтра поиска и выберите **Integration Accounts** (Учетные записи интеграции) из списка результатов. ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)
3. Выберите **учетную запись интеграции**, которую нужно переместить. ![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)
4. Щелкните ссылку **Переместить**, которая находится в меню. ![](./media/app-service-logic-enterprise-integration-accounts/move.png)
5. Подтвердите свой выбор.

## Дальнейшие действия
- [Узнайте больше о соглашениях](./app-service-logic-enterprise-integration-agreements.md "Узнайте о соглашениях интеграции Enterprise.").


 

<!---HONumber=AcomDC_0727_2016-->