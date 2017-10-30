---
title: "Использование свойств в политиках управления API Azure"
description: "Узнайте, как использовать свойства в политиках управления API Azure."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 6f39b00f-cf6e-4cef-9bf2-1f89202c0bc0
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 517f434c8f7fabc1402fb938d5ff5c733b86f2fd
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/16/2017
---
# <a name="how-to-use-properties-in-azure-api-management-policies"></a>Использование свойств в политиках управления API Azure
Политики управления API представляют собой одну из эффективных функций системы, позволяющих издателю изменять поведение интерфейса API путем его настройки. Политика — это коллекция правил, которые выполняются последовательно над запросом или ответом API. Для создания правил политики можно использовать литеральные текстовые значения, выражения политики и свойства. 

Каждый экземпляр службы управления API имеет коллекцию свойств пар "ключ-значение", которые являются глобальными для экземпляра службы. Эти свойства можно использовать для управления постоянными строковыми значениями во всей конфигурации и политиках API. Каждое свойство имеет следующие атрибуты.

| Атрибут | Тип | Описание |
| --- | --- | --- |
| Name (Имя) |строка |Имя свойства. Может содержать только буквы, цифры, точки, тире и знаки подчеркивания. |
| Значение |строка |Значение свойства. Не может быть пустым или состоять только из пробелов. |
| Секрет |Логическое |Определяет, является ли значение секретом и должно ли быть зашифровано. |
| Теги |Массив строк |Необязательные теги, которые (если указаны) можно использовать для фильтрации списка свойств. |

Свойства настраиваются на портале издателя на вкладке **Свойства** . В приведенном далее примере настраиваются три свойства.

![Свойства][api-management-properties]

Значения свойств могут содержать строковые литералы и [выражения политики](https://msdn.microsoft.com/library/azure/dn910913.aspx). В приведенной далее таблице приводятся предыдущие три примера свойств и их атрибуты. Значение `ExpressionProperty` является выражением политики, которое возвращает строку, содержащую текущую дату и время. Свойство `ContosoHeaderValue` помечено как секрет, поэтому его значение не отображается.

| Name (Имя) | Значение | Секрет | Теги |
| --- | --- | --- | --- |
| ContosoHeader |TrackingId |Ложь |Contoso |
| ContosoHeaderValue |•••••••••••••••••••••• |Истина |Contoso |
| ExpressionProperty |@(DateTime.Now.ToString()) |Ложь | |

## <a name="to-use-a-property"></a>Использование свойства
Чтобы использовать свойство в политике, поместите имя свойства внутри пары двойных фигурных скобок `{{ContosoHeader}}`, как показано в следующем примере.

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

В этом примере `ContosoHeader` используется как имя заголовка в политике `set-header`, а `ContosoHeaderValue` — в качестве значения этого заголовка. При оценке этой политики во время запроса или ответа к шлюзу управления API `{{ContosoHeader}}` и `{{ContosoHeaderValue}}` заменяются соответствующими значениями свойств.

Свойства можно использовать как полные значения атрибутов или элементов, как показано в предыдущем примере, но их можно также вставлять в литеральное текстовое выражение или комбинировать с частью этого выражения, как показано в следующем примере: `<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

Свойства могут также содержать выражения политики. В следующем примере используется `ExpressionProperty` .

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

При оценке этой политики `{{ExpressionProperty}}` заменяется своим значением: `@(DateTime.Now.ToString())`. Поскольку значение является выражением политики, выражение проходит оценку и политика продолжает выполнение.

Этот момент можно проверить на портале разработчика путем вызова операции, имеющей политику со свойствами. В приведенном далее примере вызывается операция с двумя политиками `set-header` со свойствами из предыдущего примера. Обратите внимание, что ответ содержит два пользовательских заголовка, которые были настроены с помощью политик со свойствами.

![Портал разработчика][api-management-send-results]

Если взглянуть на [трассировку инспектора API](api-management-howto-api-inspector.md) для вызова, который содержит две политики со свойствами из предыдущего примера, можно увидеть две политики `set-header` со вставленными значениями свойств, а также оценку выражения политики для свойства, содержащего выражение политики.

![Трассировка с помощью инспектора API][api-management-api-inspector-trace]

Обратите внимание, что хотя значения свойств могут содержать выражения политики, они не могут содержать другие свойства. Если текст, содержащий ссылку на свойство, используется для значения свойства, такого как `Property value text {{MyProperty}}`, эта ссылка на свойство не будет заменена и включится в значение свойства.

## <a name="to-create-a-property"></a>Создание свойства
Чтобы создать свойство, нажмите кнопку **Добавить свойство** на вкладке **Свойства**.

![Добавление свойства][api-management-properties-add-property-menu]

Поля **Имя** и **Значение** обязательны для заполнения. Если значением свойства является секрет, установите флажок **Это секрет** . Введите один или несколько необязательных тегов, чтобы упорядочить свойства, и нажмите кнопку **Сохранить**.

![Добавление свойства][api-management-properties-add-property]

После сохранения нового свойства в текстовом поле **Свойство поиска** появляется имя нового свойства и отображается новое свойство. Чтобы отобразить все свойства, очистите поле **Свойство поиска** и нажмите клавишу ВВОД.

![Свойства][api-management-properties-property-saved]

Сведения о создании свойства с помощью REST API см. в [этом разделе](https://msdn.microsoft.com/library/azure/mt651775.aspx#Put).

## <a name="to-edit-a-property"></a>Изменение свойства
Чтобы изменить свойство, щелкните **Изменить** рядом с нужным свойством.

![Изменение свойства][api-management-properties-edit]

Внесите необходимые изменения и нажмите кнопку **Сохранить**. Если изменяется имя свойства, все политики, которые ссылаются на это свойство, автоматически обновляются для использования нового имени.

![Изменение свойства][api-management-properties-edit-property]

Сведения об изменении свойства с помощью REST API см. в [этом разделе](https://msdn.microsoft.com/library/azure/mt651775.aspx#Patch).

## <a name="to-delete-a-property"></a>Удаление свойства
Чтобы удалить свойство, нажмите кнопку **Удалить** рядом с нужным свойством.

![Изменение свойства][api-management-properties-delete]

Чтобы подтвердить действие, нажмите кнопку **Yes, delete it** (Да, удалить).

![Подтверждение удаления][api-management-delete-confirm]

> [!IMPORTANT]
> Если на свойство ссылаются какие-либо политики, его можно удалить только после удаления из всех политик, которые его используют.
> 
> 

Сведения об удалении свойства с помощью REST API см. в [этом разделе](https://msdn.microsoft.com/library/azure/mt651775.aspx#Delete).

## <a name="to-search-and-filter-properties"></a>Поиск и фильтрация свойств
На вкладке **Свойства** находятся функции поиска и фильтрации, упрощающие управление свойствами. Чтобы отфильтровать список свойств по имени свойства, введите условие поиска в поле **Свойство поиска** . Чтобы отобразить все свойства, очистите поле **Свойство поиска** и нажмите клавишу ВВОД.

![Поиск][api-management-properties-search]

Чтобы отфильтровать список свойств по значениям тегов, введите один или несколько тегов в поле **Фильтровать по тегам** . Чтобы отобразить все свойства, очистите поле **Фильтровать по тегам** и нажмите клавишу ВВОД.

![Фильтр][api-management-properties-filter]

## <a name="next-steps"></a>Дальнейшие действия
* Узнайте больше о работе с политиками
  * [Политики в управлении API](api-management-howto-policies.md)
  * [Справочник по политикам](https://msdn.microsoft.com/library/azure/dn894081.aspx)
  * [Выражения политики](https://msdn.microsoft.com/library/azure/dn910913.aspx)

## <a name="watch-a-video-overview"></a>Просмотр видеообзора
> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Use-Properties-in-Policies/player]
> 
> 

[api-management-properties]: ./media/api-management-howto-properties/api-management-properties.png
[api-management-properties-add-property]: ./media/api-management-howto-properties/api-management-properties-add-property.png
[api-management-properties-edit-property]: ./media/api-management-howto-properties/api-management-properties-edit-property.png
[api-management-properties-add-property-menu]: ./media/api-management-howto-properties/api-management-properties-add-property-menu.png
[api-management-properties-property-saved]: ./media/api-management-howto-properties/api-management-properties-property-saved.png
[api-management-properties-delete]: ./media/api-management-howto-properties/api-management-properties-delete.png
[api-management-properties-edit]: ./media/api-management-howto-properties/api-management-properties-edit.png
[api-management-delete-confirm]: ./media/api-management-howto-properties/api-management-delete-confirm.png
[api-management-properties-search]: ./media/api-management-howto-properties/api-management-properties-search.png
[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png

