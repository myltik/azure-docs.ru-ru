<properties 
	pageTitle="Обзор схем и пакета интеграции Enterprise | Служба приложений Microsoft Azure | Microsoft Azure" 
	description="Узнайте, как использовать схемы с пакетом интеграции Enterprise и приложениями логики." 
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
	ms.date="07/29/2016" 
	ms.author="deonhe"/>

# Сведения о схемах и пакете интеграции Enterprise  

## Для чего используется схема
Схемы используются для подтверждения того, что получаемые документы XML являются допустимыми, то есть содержат ожидаемые данные в предопределенном формате.

## Как добавить схему
На портале Azure выполните следующие действия.
1. Выберите **Больше служб**. ![](./media/app-service-logic-enterprise-integration-overview/overview-11.png)
2. Введите **integration** в поле фильтра поиска и выберите **Integration Accounts** (Учетные записи интеграции) из списка результатов. ![](./media/app-service-logic-enterprise-integration-overview/overview-21.png)
3. Выберите **учетную запись интеграции**, в которую необходимо добавить схему. ![](./media/app-service-logic-enterprise-integration-overview/overview-31.png)
4.  Выберите элемент **Схемы**. ![](./media/app-service-logic-enterprise-integration-schemas/schema-11.png)
5. В открывшейся колонке "Схемы" нажмите кнопку **Добавить**. ![](./media/app-service-logic-enterprise-integration-schemas/schema-21.png)
6. Введите **имя** для схемы. Чтобы передать файл схемы, щелкните значок папки в правой части текстового поля **Схема**. После завершения передачи нажмите кнопку **ОК**. ![](./media/app-service-logic-enterprise-integration-schemas/schema-31.png)
7. Добавление файла схемы, размер которого превышает 2 МБ (до 8 МБ)
   * Передайте схему в хранилище и скопируйте универсальный код ресурса (URI). ![](./media/app-service-logic-enterprise-integration-schemas/schema-blob.png)
   * Выберите "Large file" (Большой файл) в разделе "Добавление схемы" и укажите универсальный код ресурса (URI) в поле "URI содержимого". ![](./media/app-service-logic-enterprise-integration-schemas/schema-largefile.png) 8. Должна отобразиться добавленная схема. ![](./media/app-service-logic-enterprise-integration-schemas/schema-41.png)

## Как использовать схемы
- Схемы используются для проверки сообщений, которые передаются в рамках сценария "бизнес-бизнес".

## Как изменить схему
1. Выберите элемент **Схемы**.
2. В открывшейся колонке "Schemas" (Схемы) выберите схему, которую вы хотите изменить.
3. Щелкните ссылку **Передать** в колонке "Схемы". ![](./media/app-service-logic-enterprise-integration-schemas/edit-12.png)
4. Выберите файл схемы, который следует передать, используя открывшееся диалоговое окно выбора файлов.
5. Нажмите кнопку **Открыть** в средстве выбора файлов. ![](./media/app-service-logic-enterprise-integration-schemas/edit-31.png)
6. Вы получите уведомление о том, что передача прошла успешно.

## Как удалить схему
1. Выберите элемент **Schemas** (Схемы).
2. В открывшейся колонке "Schemas" (Схемы) выберите схему, которую вы хотите удалить.
3. В колонке "Схемы" в строке меню щелкните ссылку **Удалить**. ![](./media/app-service-logic-enterprise-integration-schemas/delete-12.png)
4. Если вы действительно хотите удалить выбранную схему, нажмите кнопку **Да** в диалоговом окне "Удаление схемы", чтобы подтвердить это. ![](./media/app-service-logic-enterprise-integration-schemas/delete-21.png)
5. Наконец, обратите внимание на то, что список схем в колонке обновился и схема, которую вы удалили, больше не отображается. ![](./media/app-service-logic-enterprise-integration-schemas/delete-31.png)

## Дальнейшие действия

- [Узнайте больше о пакете интеграции Enterprise.](./app-service-logic-enterprise-integration-overview.md "Узнайте о пакете интеграции Enterprise.")

<!---HONumber=AcomDC_0914_2016-->