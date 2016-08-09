<properties 
	pageTitle="Обзор карт и пакета интеграции Enterprise | Служба приложений Microsoft Azure | Microsoft Azure" 
	description="Узнайте, как использовать карты с пакетом интеграции Enterprise и приложениями логики." 
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

# Узнайте о картах и пакете интеграции Enterprise

## Обзор
В интеграции Enterprise карты используются для преобразования данных XML из одного формата в другой.

## Что такое карта?
Карта — это документ XML, который определяет, какие данные в документе должны быть преобразованы в другой формат.

## Для чего используются карты?
Предположим, что вы регулярно получаете заказы или счета от клиентов "бизнес-бизнес", которые используют формат даты ГГГММДД. Однако в вашей организации даты хранятся в формате ММДДГГГ. Вы можете использовать карту, чтобы *преобразовать* формат даты ГГГММДД в ММДДГГГ перед сохранением сведений о заказе или счете в базе данных деловой активности клиента.

## Как передать карту?
На портале Azure выполните следующие действия.
1. Щелкните **Обзор**.![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)
2. Введите **integration** в поле фильтра поиска и выберите **Integration Accounts** (Учетные записи интеграции) из списка результатов. ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)
3. Выберите **учетную запись интеграции**, в которую необходимо добавить карту. ![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)
4.  Выберите элемент **Maps** (Карты). ![](./media/app-service-logic-enterprise-integration-maps/map-1.png)
5. В открывшейся колонке "Maps" (Карты) нажмите кнопку **Добавить**. ![](./media/app-service-logic-enterprise-integration-maps/map-2.png)
6. Введите **имя** для карты. Чтобы передать файл карты, щелкните значок папки в правой части текстового поля **Map** (Карта). После завершения передачи нажмите кнопку **ОК**. ![](./media/app-service-logic-enterprise-integration-maps/map-3.png)
7. Карта будет добавлена в учетную запись интеграции. На экране появится уведомление об успешном выполнении или сбое при добавлении файла карты. После получения уведомления выберите элемент **Maps** (Карты). Вы увидите в колонке "Maps" (Карты) только что добавленную карту. ![](./media/app-service-logic-enterprise-integration-maps/map-4.png)

## Как изменить карту?
Чтобы изменить карту, необходимо передать новый файл карты с нужными изменениями. Можно сначала загрузить карту и изменить ее.

Выполните следующие действия, чтобы отправить новую карту, которая заменит существующую.
1. Выберите элемент **Maps** (Карты).
2. В открывшейся колонке "Maps" (Карты) выберите карту, которую вы хотите изменить.
3. В колонке**Maps** (Карты) щелкните ссылку **Обновить**. ![](./media/app-service-logic-enterprise-integration-maps/edit-1.png)
4. Выберите файл карты, который следует передать, используя появившееся диалоговое окно выбора файлов, затем щелкните **Открыть** в средстве выбора файлов. ![](./media/app-service-logic-enterprise-integration-maps/edit-2.png)
5. После передачи карты отобразится всплывающее уведомление.

## Как удалить карту?
1. Выберите элемент **Maps** (Карты).
2. В открывшейся колонке "Maps" (Карты) выберите карту, которую вы хотите удалить.
3. Щелкните ссылку **Удалить**. ![](./media/app-service-logic-enterprise-integration-maps/delete.png)
4. Подтвердите, что действительно собираетесь удалить карту. ![](./media/app-service-logic-enterprise-integration-maps/delete-confirmation-1.png)

## Дальнейшие действия
- [Узнайте больше о пакете интеграции Enterprise.](./app-service-logic-enterprise-integration-overview.md "Узнайте о пакете интеграции Enterprise.")
- [Узнайте больше о соглашениях](./app-service-logic-enterprise-integration-agreements.md "Узнайте о соглашениях интеграции Enterprise.").
- [Узнайте больше о преобразованиях](./app-service-logic-enterprise-integration-transform.md "Узнайте о преобразованиях в рамках корпоративной интеграции.").

<!---HONumber=AcomDC_0727_2016-->