---
title: Политики в службе управления API Azure | Документация Майкрософт
description: Сведения о создании, изменении и настройке политик в службе управления API.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: apimpm
ms.openlocfilehash: 54fbba197f6609731ffaf3ff15143a28e70a955f
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/28/2018
ms.locfileid: "29712874"
---
# <a name="policies-in-azure-api-management"></a>Политики в Azure API Management

Политики в службе управления API Azure — это эффективная функция системы, которая позволяет издателю настраивать интерфейса API и изменять его поведение. Политика — это коллекция операторов, которые выполняются последовательно по запросу интерфейса API или при получении из него ответа. К часто используемым операторам относятся преобразование формата из XML в JSON, а также ограничение скорости вызовов, позволяющее ограничивать количество входящих вызовов от разработчика. Также существует много готовых политик.

Политики применяются внутри шлюза, который находится между потребителем интерфейса API и управляемым API. Шлюз получает все запросы и обычно отправляет их без изменения в базовый API. Однако политика может применять изменения как для входящего запроса, так и для исходящего ответа.

Выражения политики можно использовать в качестве значений атрибутов или текстовых значений в любой политике управления API, если в ней не указано иное. Некоторые политики (включая [Поток управления][Control flow] и [Задание переменной][Set variable]) основаны на выражениях политики. Чтобы узнать больше, ознакомьтесь с [расширенными политиками][Advanced policies] и [выражениями политики][Policy expressions].

## <a name="sections"> </a>Общая информация о конфигурации политики

Определение политики представляет собой простой XML-документ, который описывает последовательность входящих и исходящих операторов. Файл XML можно редактировать прямо в окне определения. Перечень операторов отображается справа, а операторы, которые применимы к текущей области, включены и выделены.

При щелчке разрешенного оператора будет добавлен соответствующий XML-код в местоположении курсора в виде определения. 

> [!NOTE]
> Если политика, которую требуется добавить, не включена, убедитесь, что вы находитесь в правильной области действия этой политики. Каждая инструкция политики предназначена для использования в определенных областях и разделах политики. Чтобы просмотреть разделы политики и области ее действия, ознакомьтесь с разделом **Использование** для этой политики в [справочнике по политикам][Policy Reference].
> 
> 

Конфигурация состоит из таких операторов: `inbound`, `backend`, `outbound` и `on-error`. Набор указанных операторов политики выполняется для создания запроса и получения ответа.

```xml
<policies>
  <inbound>
    <!-- statements to be applied to the request go here -->
  </inbound>
  <backend>
    <!-- statements to be applied before the request is forwarded to 
         the backend service go here -->
  </backend>
  <outbound>
    <!-- statements to be applied to the response go here -->
  </outbound>
  <on-error>
    <!-- statements to be applied if there is an error condition go here -->
  </on-error>
</policies> 
```

Если во время обработки запроса произошла ошибка, все оставшиеся действия в разделах `inbound`, `backend` или `outbound` пропускаются, и начинают выполняться операторы из раздела `on-error`. Поместив операторы политики в раздел `on-error`, вы можете просмотреть ошибку с помощью свойства `context.LastError`, изучить и настроить ответ на ошибку с помощью политики `set-body`, а также настроить, что именно происходит при возникновении ошибки. Существуют коды ошибок для встроенных действий и для ошибок, которые могут возникать во время обработки оператора политики. Дополнительные сведения см. в статье [Обработка ошибок в политиках управления API](https://msdn.microsoft.com/library/azure/mt629506.aspx).

## <a name="scopes"> </a>Как настраивать политики

Сведения о настройке политик см. в статье [How to set or edit Azure API Management policies](set-edit-policies.md) (Как настроить или изменить политики в службе управления API Azure).

## <a name="policy-reference"></a>Справочник по политикам

Полный перечень операторов политик и их параметров см. в [справочнике по политикам](api-management-policy-reference.md).

## <a name="policy-samples"></a>Примеры политик

Дополнительные примеры кода см. статье [API Management policy samples](policy-samples.md) (Примеры политик в службе управления API Azure).

## <a name="examples"></a>Примеры

### <a name="apply-policies-specified-at-different-scopes"></a>Применение политик, заданных в разных областях

Если у вас есть политика на глобальном уровне и политика, настроенная для API, то при каждом использовании этого API будут применяться обе политики. API Management позволяет детерминированным образом упорядочивать объединенные операторы политик через основной элемент. 

```xml
<policies>
    <inbound>
        <cross-domain />
        <base />
        <find-and-replace from="xyz" to="abc" />
    </inbound>
</policies>
```

В примере определения политики, приведенном выше, оператор `cross-domain` будет выполняться перед любыми более высокими политиками, после которых, в свою очередь, будет следовать политика `find-and-replace`. 

### <a name="restrict-incoming-requests"></a>Ограничение входящих запросов

Чтобы добавить новый оператор для ограничения входящих запросов по указанным IP-адресам, поместите курсор в XML-элемент `inbound` и щелкните оператор **Restrict caller IPs** (Ограничить IP-адреса вызывающего объекта).

![Политики ограничения][policies-restrict]

При этом будет добавлен фрагмент XML-кода в элемент `inbound` , который будет содержать указания о том, как настроить оператор.

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address"/>
</ip-filter>
```

Для ограничения входящих запросов и принятия только тех запросов, которые поступают с IP-адреса 1.2.3.4, измените XML-код следующим образом:

```xml
<ip-filter action="allow">
    <address>1.2.3.4</address>
</ip-filter>
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о работе с политиками см. в следующих статьях:

+ [Преобразование API-интерфейсов](transform-api.md).
+ Полный перечень операторов политик и их параметров см. в [справочнике по политикам](api-management-policy-reference.md).
+ [Примеры политик](policy-samples.md).   

[Policy Reference]: api-management-policy-reference.md
[Product]: api-management-howto-add-products.md
[API]: api-management-howto-add-products.md
[Operation]: api-management-howto-add-operations.md

[Advanced policies]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[Control flow]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[Set variable]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[Policy expressions]: https://msdn.microsoft.com/library/azure/dn910913.aspx

[policies-restrict]: ./media/api-management-howto-policies/api-management-policies-restrict.png
