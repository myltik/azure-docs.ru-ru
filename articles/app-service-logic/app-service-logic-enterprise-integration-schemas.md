<properties 
	pageTitle="Обзор схем и пакета интеграции Enterprise | Служба приложений Microsoft Azure" 
	description="Узнайте, как использовать схемы с пакетом интеграции Enterprise и приложениями логики." 
	services="app-service\logic" 
	documentationCenter=".net,nodejs,java"
	authors="msftman" 
	manager="erikre" 
	editor="cgronlun"/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/29/2016" 
	ms.author="deonhe"/>

# Сведения о схемах и пакете интеграции Enterprise  

## Для чего используется схема
Схемы используются для подтверждения того, что получаемые документы XML являются допустимыми, то есть содержат ожидаемые данные в предопределенном формате.

## Как добавить схему
На портале Azure выполните следующие действия.
1. Щелкните **Обзор**.![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)
2. Введите **integration** в поле фильтра поиска и выберите **Integration Accounts** (Учетные записи интеграции) из списка результатов. ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)
3. Выберите **учетную запись интеграции**, в которую необходимо добавить схему. ![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)
4.  Выберите элемент **Schemas** (Схемы). ![](./media/app-service-logic-enterprise-integration-schemas/schema-1.png)
5. В открывшейся колонке "Schemas" (Схемы) нажмите кнопку **Добавить**. ![](./media/app-service-logic-enterprise-integration-schemas/schema-2.png)
6. Введите **имя** для схемы. Чтобы передать файл схемы, щелкните значок папки в правой части текстового поля **Схема**. После завершения передачи нажмите кнопку **ОК**. ![](./media/app-service-logic-enterprise-integration-schemas/schema-3.png)
7. Щелкните значок уведомления (*колокольчик*), чтобы просмотреть ход передачи схемы. ![](./media/app-service-logic-enterprise-integration-schemas/schema-4.png)
8. Выберите элемент **Schemas** (Схемы). Элемент обновится, и вы увидите, что количество схем возросло, отражая успешное добавление новой схемы. Выбрав элемент **Schemas** (Схемы), в колонке "Schemas" (Схемы) справа вы также увидите только что добавленную схему. ![](./media/app-service-logic-enterprise-integration-schemas/schema-5.png)


## Как использовать схемы
- Схемы используются для проверки сообщений, которые передаются в рамках сценария "бизнес-бизнес".

## Как изменить схему
1. Выберите элемент **Schemas** (Схемы).
2. В открывшейся колонке "Schemas" (Схемы) выберите схему, которую вы хотите изменить.
3. Щелкните ссылку **Передать** в колонке "Schemas" (Схемы). ![](./media/app-service-logic-enterprise-integration-schemas/edit-1.png)
4. Выберите файл схемы, который следует передать, используя открывшееся диалоговое окно выбора файлов.
5. Нажмите кнопку **Открыть** в средстве выбора файлов. ![](./media/app-service-logic-enterprise-integration-schemas/edit-2.png)
6. Вы получите уведомление о том, что передача прошла успешно. ![](./media/app-service-logic-enterprise-integration-schemas/edit-3.png)

## Как удалить схему
1. Выберите элемент **Schemas** (Схемы).
2. В открывшейся колонке "Schemas" (Схемы) выберите схему, которую вы хотите удалить.
3. В колонке "Shemas" (Схемы) в строке меню щелкните ссылку **Удалить**. ![](./media/app-service-logic-enterprise-integration-schemas/delete-1.png)
4. Если вы действительно хотите удалить выбранную схему, нажмите кнопку **Да** в диалоговом окне "Delete schema" (Удаление схемы), чтобы подтвердить это. ![](./media/app-service-logic-enterprise-integration-schemas/delete-2.png)
5. Наконец, обратите внимание, что список схем в колонке обновился и схема, которую вы удалили, больше не отображается. ![](./media/app-service-logic-enterprise-integration-schemas/delete-3.png)

## Дальнейшие действия

- [Узнайте больше о соглашениях](./app-service-logic-enterprise-integration-agreements.md "Узнайте о соглашениях интеграции Enterprise.")

      

<!---HONumber=AcomDC_0706_2016-->