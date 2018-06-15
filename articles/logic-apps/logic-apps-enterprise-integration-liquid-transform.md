---
title: Преобразование данных JSON с помощью преобразований Liquid (Azure Logic Apps) | Документация Майкрософт
description: Создание преобразований или сопоставлений для сложных преобразований JSON с помощью Logic Apps и шаблона Liquid.
services: logic-apps
documentationcenter: ''
author: divyaswarnkar
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: LADocs; divswa
ms.openlocfilehash: 42a102c9b2663380a93d56cc5f8fb82abaa83b74
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299517"
---
# <a name="perform-advanced-json-transformations-with-a-liquid-template"></a>Выполнение сложных преобразований JSON с помощью шаблона Liquid

Azure Logic Apps поддерживает основные преобразования JSON через встроенные действия операций с данными, такие как **Создание** или **Синтаксический анализ JSON**. Для более сложных преобразований JSON вы можете применить в приложении логики шаблон Liquid. 
[Liquid](https://shopify.github.io/liquid/) – это язык шаблонов с открытым исходным кодом, позволяющий создавать гибкие веб-приложения.
 
Из этой статьи вы узнаете, как использовать сопоставление или шаблон Liquid, который поддерживает сложные преобразования JSON с применением итерации, потоков управления, переменных и т. д. Перед тем как применять преобразования Liquid в приложении логики, следует определить сопоставления между форматами JSON в сопоставлении Liquid и сохранить созданное сопоставление в учетной записи интеграции.

## <a name="prerequisites"></a>предварительным требованиям

* Подписка Azure. Если у вас нет подписки, вы можете [создать бесплатную пробную версию учетной записи Azure](https://azure.microsoft.com/free/). В противном случае вы можете [зарегистрироваться на получение подписки с оплатой по мере использования](https://azure.microsoft.com/pricing/purchase-options/).

* Базовые знания [создания приложений логики](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* [Учетная запись интеграции](logic-apps-enterprise-integration-create-integration-account.md) (ценовая категория "Базовый")


## <a name="create-a-liquid-template-or-map-for-your-integration-account"></a>Создание шаблона или сопоставления Liquid в учетной записи интеграции

1. Создадим пример шаблона Liquid для нашего руководства. Шаблон Liquid определяет способ преобразования входных данных в формате JSON, как описано здесь:

   ``` json
   {%- assign deviceList = content.devices | Split: ', ' -%}
      {
        "fullName": "{{content.firstName | Append: ' ' | Append: content.lastName}}",
        "firstNameUpperCase": "{{content.firstName | Upcase}}",
        "phoneAreaCode": "{{content.phone | Slice: 1, 3}}",
        "devices" : [
        {%- for device in deviceList -%}
            {%- if forloop.Last == true -%}
            "{{device}}"
            {%- else -%}
            "{{device}}",
            {%- endif -%}
        {%- endfor -%}
        ]
      }
   ```
   > [!NOTE]
   > Если шаблон Liquid использует [фильтры](https://shopify.github.io/liquid/basics/introduction/#filters), имена этих фильтров должны начинаться с заглавной буквы. 

2. Войдите на [портале Azure](https://portal.azure.com).

3. В главном меню Azure выберите элемент **Все ресурсы**. 

4. Найдите с помощью поля поиска и выберите нужную учетную запись интеграции.

   ![Выбор учетной записи интеграции](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

5.  На плитке учетной записи интеграции выберите элемент **Сопоставления**.

   ![Выбор элемента "Сопоставления"](./media/logic-apps-enterprise-integration-liquid-transform/add-maps.png)

6. Выберите **Добавить** и укажите следующие сведения для сопоставления:

   * **Имя**. Это имя сопоставления, например JsontoJsonTemplate в нашем примере.
   * **Тип сопоставления**. Обозначает тип сопоставления. Для преобразования JSON в JSON следует выбрать **Liquid**.
   * **Сопоставление**. Это существующий файл шаблона или сопоставления Liquid, который будет использован для преобразования. В нашем примере это SimpleJsonToJsonTemplate.liquid. Чтобы найти этот файл, используйте средство выбора файлов.

   ![Добавление шаблона Liquid](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)
    
## <a name="add-the-liquid-action-for-json-transformation"></a>Добавление действия Liquid для преобразования JSON

1. [Создание приложения логики](../logic-apps/quickstart-create-first-logic-app-workflow.md).

2. Добавьте [Триггер запроса](../connectors/connectors-native-reqres.md#use-the-http-request-trigger) в свое приложение логики.

3. Выберите **+ Новый шаг > Добавить действие**. В поле поиска введите *Liquid* и выберите элемент **Liquid - преобразование JSON в JSON**.

   ![Поиск и выбор действия Liquid](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

4. В поле **Содержимое** выберите **Текст** из динамического списка содержимого или из списка параметров (в зависимости от того, какой из них отображается).
  
   ![Выбор текста](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)
 
5. Из списка **Сопоставление** выберите нужный шаблон Liquid, в нашем примере это JsonToJsonTemplate.

   ![Выбор сопоставления](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   Если список пуст, значит приложение логики, скорее всего, не сопоставлено с учетной записью интеграции. 
   Чтобы настроить связь с учетной записью интеграции, в которой хранится шаблон или сопоставление Liquid, выполните следующие действия.

   1. В меню приложения логики выберите **Параметры рабочего процесса**.
   2. Из списка **Select an Integration account** (Выбор учетной записи интеграции) выберите нужную учетную запись интеграции и нажмите кнопку **Сохранить**.

   ![Привязка приложения логики к учетной записи интеграции](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

## <a name="test-your-logic-app"></a>Тестирование приложения логики

Передайте в приложение логики входные данные JSON через [Postman](https://www.getpostman.com/postman) или аналогичное средство. Из приложения логики вы получите преобразованные выходные данные JSON примерно такого вида:
  
![Пример выходных данных](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontojson.png)

## <a name="more-liquid-action-examples"></a>Дополнительные примеры действия Liquid
Liquid не ограничивается преобразованием формата JSON. Ниже указаны другие действия преобразования, для которых используется Liquid:

* Преобразование JSON в текст.
  
  Шаблон Liquid, используемый в этом примере:
   
   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```
   Примеры входных и выходных данных:
  
   ![Пример выходных данных преобразования JSON в текст](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontotext.png)

* Преобразование XML в JSON.
  
  Шаблон Liquid, используемый в этом примере:
   
   ``` json
   [{% JSONArrayFor item in content -%}
        {{item}}
    {% endJSONArrayFor -%}]
   ```
   Примеры входных и выходных данных:

   ![Пример выходных данных преобразования XML в JSON](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltojson.png)

* Преобразование XML в текст.
  
  Шаблон Liquid, используемый в этом примере:

   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```

   Примеры входных и выходных данных:

   ![Пример выходных данных преобразования XML в текст](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltotext.png)

## <a name="next-steps"></a>Дополнительная информация

* [Обзор пакета интеграции Enterprise](../logic-apps/logic-apps-enterprise-integration-overview.md "Обзор пакета интеграции Enterprise")  
* [Узнайте больше о картах](../logic-apps/logic-apps-enterprise-integration-maps.md "Узнайте о картах интеграции Enterprise")  

