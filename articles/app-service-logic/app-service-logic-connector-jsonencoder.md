---
title: Использование кодировщика JSON BizTalk в приложениях логики | Microsoft Docs
description: 'Как создать и настроить кодировщик JSON BizTalk или приложение API и использовать его в приложении логики в службе приложений Azure '
services: logic-apps
documentationcenter: .net,nodejs,java
author: rajeshramabathiran
manager: erikre
editor: ''

ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 04/20/2016
ms.author: rajram

---
# Приступая к работе с кодировщиком JSON BizTalk: добавление в приложение логики
[!INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

Соединитель для кодирования и декодирования позволяет вашему приложению взаимодействовать с данными JSON и XML. Он может преобразовать экземпляр JSON в XML, и наоборот.

Кодировщик JSON BizTalk можно добавить в рабочий процесс компании и обрабатывать данные в рамках этого процесса в приложении логики.

## Использование кодировщика JSON BizTalk
Чтобы использовать кодировщик JSON BizTalk, необходимо сначала создать экземпляр приложения API для кодировщика JSON BizTalk. Это можно сделать либо в процессе, при создании приложения логики, либо выбрав приложение API для кодировщика JSON BizTalk на сайте Azure Marketplace.

## Использование кодировщика JSON BizTalk в области конструктора приложений логики
Выполните шаги из статьи [Создание приложения логики]. Кодировщик JSON BizTalk можно использовать как действие. У него нет никаких триггеров.

### Действие
* На правой панели щелкните кодировщик JSON BizTalk.
  
    ![Настройки действия][3]
* Щелкните ->
  
    ![Список действий][4]
* Кодировщик JSON BizTalk поддерживает два действия. Выберите *Xml в JSON*
  
    ![Входные данные для преобразования XML в JSON][5]
* Введите входные данные для действия и настройте их
  
    ![Кодирование и отправка настроены][6]

| Параметр | Тип | Описание параметра |
| --- | --- | --- |
| Входной XML-файл |object |Содержимое входного XML-файла |
| Удаление внешнего конверта |string |Флаг для удаления корневого узла из XML-содержимого |

Действие возвращает представление JSON входного содержимого.

## Дополнительные возможности соединителя
После создания соединителя его можно добавить в бизнес-процесс с помощью приложения логики. См. раздел [Что такое приложения логики?](app-service-logic-what-are-logic-apps.md).

> [!NOTE]
> Если вы хотите начать работу с приложениями логики Azure до создания учетной записи Azure, перейдите на веб-сайт [пробного использования приложений логики](https://tryappservice.azure.com/?appservice=logic). На этом сайте вы сможете быстро создать кратковременное приложение логики начального уровня в службе приложений. Никаких кредитных карт и обязательств.
> 
> 

Справку по API REST Swagger см. в статье [Справочные материалы по соединителям и приложениям API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

<!--References -->
[1]: app-service-logic-connector-tpm
[2]: app-service-logic-create-a-trading-partner-agreement
[3]: ./media/app-service-logic-json-encoder/ActionSettings.PNG
[4]: ./media/app-service-logic-json-encoder/ListOfActions.PNG
[5]: ./media/app-service-logic-json-encoder/EncodeInput.PNG
[6]: ./media/app-service-logic-json-encoder/EncodeConfigured.PNG

<!--Links -->
[Создание приложения логики]: app-service-logic-create-a-logic-app.md

<!---HONumber=AcomDC_0803_2016-->