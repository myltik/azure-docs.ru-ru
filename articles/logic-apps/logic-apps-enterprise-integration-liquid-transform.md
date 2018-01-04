---
title: "Преобразование данных JSON с преобразованиями жидкости - приложения логики Azure | Документы Microsoft"
description: "Создание преобразования или карты для дополнительные преобразования JSON, использование логики приложения и жидкости шаблона."
services: logic-apps
documentationcenter: 
author: divyaswarnkar
manager: anneta
editor: 
ms.assetid: 
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: LADocs; divswa
ms.openlocfilehash: cd177b1ebcb5d236ce265dc153ee6a02125a69df
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2017
---
# <a name="advanced-json-transformations-using-liquid-template"></a>Дополнительные преобразования JSON с помощью шаблона жидкости
Приложения логики Azure поддерживает основные преобразования JSON через собственный операций операции с данными, как создать или синтаксического анализа JSON. Логика приложения теперь также поддерживает дополнительные преобразования JSON с шаблонами жидкости. [Ликвидный](https://shopify.github.io/liquid/) – это язык шаблона с открытым исходным кодом для гибкой веб-приложений.
 
В этой статье сведения об использовании жидкости карты или шаблон, который может поддерживать более сложные преобразования JSON, например итераций, потоков управления, переменных и т. д. Определение JSON для сопоставления JSON с помощью этой схемы жидкости и хранения, сопоставленные в вашей учетной записи интеграции, перед тем как выполнять преобразование жидкости в приложении логику.

## <a name="prerequisites"></a>Технические условия
Ниже приведены необходимые компоненты жидкости действия.

* Подписка Azure. Если у вас нет подписки, вы можете [создать бесплатную пробную версию учетной записи Azure](https://azure.microsoft.com/free/). В противном случае вы можете [зарегистрироваться на получение подписки с оплатой по мере использования](https://azure.microsoft.com/pricing/purchase-options/).

* Базовые знания о [Создание приложения логики](../logic-apps/logic-apps-create-a-logic-app.md).

* Базовый [учетной записи интеграции](logic-apps-enterprise-integration-create-integration-account.md).


## <a name="create-and-add-liquid-template-or-map-to-integration-account"></a>Создание и добавление жидкости шаблона или сопоставления учетной записи интеграции

1. Создание образца жидкости шаблона для этого примера. Ликвидный шаблон определяет способ преобразования входных данных JSON, как описано здесь:

   ```
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
    > Если используется шаблон жидкости любой [фильтры](https://shopify.github.io/liquid/basics/introduction/#filters), эти фильтры должно начинаться с верхнего регистра. 

2. Войдите на [портале Azure](https://portal.azure.com).

3. В главном меню Azure выберите **все ресурсы**. 

4. В поле поиска предоставить учетной записи интеграции. Выберите свою учетную запись.

   ![Выберите учетную запись интеграции](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

5.  На плитке интеграции учетной записи выберите **карты**.

   ![Выбор карты](./media/logic-apps-enterprise-integration-liquid-transform/add-maps.png)

6. Выберите **добавить** и указав следующие сведения для сопоставления:
  * **Имя**: имя для карты, который является «JsontoJsonTemplate» в этом примере.
  * **Тип карты**: тип для сопоставления. JSON для преобразования JSON, необходимо выбрать **жидкости**.
  * **Карта**: существующий жидкости файл шаблона или карту для преобразования, который является «SimpleJsonToJsonTemplate.liquid» в этом примере. Чтобы найти этот файл можно использовать средство выбора файлов.

    ![Добавить шаблон жидкости](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)

    
## <a name="add-the-liquid-action-to-transform-json-in-your-logic-app"></a>Добавить действие жидкости для преобразования JSON в приложении логики

1. [Создание приложения логики](logic-apps-create-a-logic-app.md).

2. Добавить [триггер запроса](../connectors/connectors-native-reqres.md#use-the-http-request-trigger) в приложение логику.

3. Выберите **+ новый шаг > Добавить действие**. Поиск *жидкости* в поле поиска. Выберите **жидкости - преобразования JSON в JSON**.

  ![Поиск — действие жидкости](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

4. В **содержимого** выберите **текст** из динамического содержимого списка или список параметров, какое значение отображается. 
  
  ![Выберите текст](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)
 
5. Из **карты** раскрывающегося списка выберите ваш жидкости шаблона, являющегося JsonToJsonTemplate в этом примере.

  ![Выбор карты](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   Если список пуст, скорее всего, логика приложения не связан с учетной записью интеграции. Чтобы связать учетную запись интеграции с жидкости шаблона или карты логики приложения, выполните следующие действия:

   1. Выберите в меню приложения логики **параметры рабочего процесса**. 
   2. Из **выберите учетную запись интеграции** выберите свою учетную запись интеграции, а затем выберите **Сохранить**.

     ![Приложение логики ссылку для учетной записи интеграции](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)


## <a name="test-your-logic-app"></a>Тестирование приложения логики

   Входные данные JSON в приложение логику из блога [почтальон](https://www.getpostman.com/postman) или аналогичное средство. Преобразованный выходных данных JSON из логики приложения выглядит как в следующем примере:
  
   ![Пример выходных данных](./media/logic-apps-enterprise-integration-liquid-transform/example-output.png)


## <a name="next-steps"></a>Дальнейшие действия
* [Обзор пакета интеграции Enterprise](../logic-apps/logic-apps-enterprise-integration-overview.md "Обзор пакета интеграции Enterprise")  
* [Узнайте больше о картах](../logic-apps/logic-apps-enterprise-integration-maps.md "Узнайте о картах интеграции Enterprise")  

