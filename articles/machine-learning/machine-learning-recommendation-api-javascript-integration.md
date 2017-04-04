---
title: "Рекомендации по машинному обучению: интеграция JavaScript | Документация Майкрософт"
description: "Рекомендации по машинному обучению Azure — интеграция JavaScript — документация"
services: machine-learning
documentationcenter: 
author: LuisCabrer
manager: jhubbard
editor: cgronlun
ms.assetid: bbbb5bb6-489d-4a62-a2ae-f36237e9e2e1
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 03/31/2017
ms.author: luisca
ROBOTS: NOINDEX, NOFOLLOW
redirect_url: machine-learning-datamarket-deprecation
translationtype: Human Translation
ms.sourcegitcommit: dc07007eec860e0ad7342a4926c6797341719c5b
ms.openlocfilehash: 20791d5729a65497b5f76f929bf331906ac65818
ms.lasthandoff: 12/08/2016


---
# <a name="azure-machine-learning-recommendations---javascript-integration"></a>Рекомендации по машинному обучению Azure — интеграция JavaScript
> [!NOTE]
> Начните использовать когнитивную службу API рекомендаций вместо этой версии. Когнитивная служба рекомендаций заменит эту службу, и все новые функции будут разрабатываться в ней. Она включает в себя новые возможности, такие как поддержка пакетной обработки, улучшенный обозреватель API, более четкое представление API, более согласованные процедуры регистрации и выставления счетов, и т. д.
> Узнайте больше о [переходе на новую когнитивную службу](http://aka.ms/recomigrate).
> 
> 

В этом документе описан процесс интеграции веб-сайта с использованием JavaScript. JavaScript позволяет отправлять события получения данных и использовать рекомендации после построения модели рекомендации. Все операции, которые совершаются при помощи JS, можно выполнить непосредственно на сервере.

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="1-general-overview"></a>1. Общая информация
Интеграция веб-сайта с рекомендациями Azure ML выполняется в 2 этапа.

1. Отправка событий в рекомендации Azure ML. Это позволит построить модель рекомендации.
2. Потребление созданных рекомендаций. После построения модели можно использовать созданные рекомендации. (В этом документе не объясняется, как создавать модель; подробные сведения можно найти в кратком руководстве по началу работы.)

<ins>Этап I</ins>

На первом этапе на ваши HTML-страницы устанавливается небольшая библиотека JavaScript, которая позволяет странице отправлять события по мере их возникновения на HTML-странице на серверы рекомендаций Azure ML (через Data Market):

![Рисунок1][1]

<ins>Этап II</ins>

На втором этапе, когда потребуется отобразить рекомендации на выбранной странице, необходимо задать один из следующих параметров:

1. Ваш сервер (на этапе отрисовки страницы) обращается к серверу рекомендаций Azure ML (через Data Market), чтобы получить рекомендации. В результаты попадает список идентификаторов элементов. Ваш сервер должен дополнить результаты метаданными элементов (например, изображениями, описанием) и отправить созданную страницу в браузер.

![Рисунок2][2]

2. Другой вариант заключается в использовании небольшого файла JavaScript из первого этапа, чтобы получить простой список рекомендованных элементов. Данные, извлекаемые при помощи этого способа, более компактны, чем в первом случае.

![Рисунок3][3]

## <a name="2-prerequisites"></a>2. Предварительные требования
1. Создайте новую модель при помощи интерфейсов API. Чтобы узнать, как это сделать, обратитесь к краткому руководству по началу работы.
2. Закодируйте &lt;dataMarketUser&gt;:&lt;dataMarketKey&gt;, используя base64. (Это будет использоваться для обычной проверки подлинности в целях включения кода JS для вызова интерфейсов API.)

## <a name="3-send-data-acquisition-events-using-javascript"></a>3. Отправка событий получения данных при помощи JavaScript
Следующие шаги обеспечивают отправку событий.

1. Включите в код библиотеку JQuery. Ее можно загрузить в NuGet по следующему URL-адресу.
   
     http://www.nuget.org/packages/jQuery/1.8.2
2. Включите библиотеку рекомендаций Java Script, получив ее по следующему URL-адресу: http://aka.ms/RecoJSLib1.
3. Выполните инициализацию библиотеки рекомендаций Azure ML, используя необходимые параметры.
   
     <script>
         AzureMLRecommendationsStart("<base64encoding of username:key>",       "<model_id>");   </script>
4. Отправьте нужное событие. См. раздел подробностей ниже, чтобы ознакомиться со всеми типами событий (пример события щелчка).
   
     <script>
         if (typeof AzureMLRecommendationsEvent=="undefined") {         
                     AzureMLRecommendationsEvent = []; } AzureMLRecommendationsEvent.push({ event: "click", item: "18321116" });   </script>

### <a name="31----limitations-and-browser-support"></a>3.1.    Ограничения и поддержка браузеров
Данная реализация является справочной и представлена как есть. Она должна поддерживаться во всех основных браузерах.

### <a name="32----type-of-events"></a>3.2.    Тип событий
Существуют 5 типов событий, которые поддерживаются библиотекой: щелчок мышью, щелчок рекомендации, добавление в корзину, удаление из корзины и приобретение. Имеется дополнительное событие, которое используется для установки контекста пользователя под именем "Имя входа".

#### <a name="321-click-event"></a>3.2.1. Событие "Щелчок"
Это событие должно использоваться всякий раз, когда пользователь щелкает элемент. Обычно, когда пользователь щелкает какой-нибудь элемент, открывается новая страница с подробными сведениями о нем; на этой странице и требуется запускать событие.

Параметры

* event (строка, обязательный) — click.
* item (строка, обязательный) — уникальный идентификатор элемента.
* itemName (строка, необязательный) — имя элемента.
* itemDescription (строка, необязательный) — описание элемента.
* itemCategory (строка, необязательный) — категория элемента.
  
        <script>
            if (typeof AzureMLRecommendationsEvent == "undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({ event: "click", item: "3111718" });
        </script>

Или с дополнительными данными:

        <script>
            if (typeof AzureMLRecommendationsEvent === "undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({event: "click", item: "3111718", itemName: "Plane", itemDescription: "It is a big plane", itemCategory: "Aviation"});
        </script>


#### <a name="322-recommendation-click-event"></a>3.2.2. Событие "щелчок рекомендации"
Это событие должно использоваться всякий раз, когда пользователь щелкает элемент, который был получен из рекомендаций Azure ML в качестве рекомендованного элемента. Обычно, когда пользователь щелкает какой-нибудь элемент, открывается новая страница с подробными сведениями о нем; на этой странице и требуется запускать событие.

Параметры

* event (строка, обязательный) — recommendationclick.
* item (строка, обязательный) — уникальный идентификатор элемента.
* itemName (строка, необязательный) — имя элемента.
* itemDescription (строка, необязательный) — описание элемента.
* itemCategory (строка, необязательный) — категория элемента.
* seeds (массив строк, необязательный) — начальные значения, создавшие запрос рекомендации.
* recoList (массив строк, необязательный) — результат запроса рекомендации, создавшего активированный элемент.
  
        <script>
            if (typeof AzureMLRecommendationsEvent=="undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({event: "recommendationclick", item: "18899918" });
        </script>

Или с дополнительными данными:

        <script>
            if (typeof AzureMLRecommendationsEvent == "undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({ event: eventName, item: "198", itemName: "Plane2", itemDescription: "It is a big plane2", itemCategory: "Default2", seeds: ["Seed1", "Seed2"], recoList: ["199", "198", "197"]                 });
        </script>


#### <a name="323-add-shopping-cart-event"></a>3.2.3. Событие добавления товара в корзину
Это событие должно использоваться всякий раз, когда пользователь добавляет элемент в корзину.
Параметры

* event (строка, обязательный) — addshopcart.
* item (строка, обязательный) — уникальный идентификатор элемента.
* itemName (строка, необязательный) — имя элемента.
* itemDescription (строка, необязательный) — описание элемента.
* itemCategory (строка, необязательный) — категория элемента.
  
        <script>
            if (typeof AzureMLRecommendationsEvent == "undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({event: "addshopcart", item: "13221118" });
        </script>

#### <a name="324-remove-shopping-cart-event"></a>3.2.4. Событие удаления товара из корзины
Это событие должно использоваться всякий раз, когда пользователь удаляет элемент из корзины.

Параметры

* event (строка, обязательный) — removeshopcart.
* item (строка, обязательный) — уникальный идентификатор элемента.
* itemName (строка, необязательный) — имя элемента.
* itemDescription (строка, необязательный) — описание элемента.
* itemCategory (строка, необязательный) — категория элемента.
  
        <script>
            if (typeof AzureMLRecommendationsEvent=="undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({ event: "removeshopcart", item: "111118" });
        </script>

#### <a name="325-purchase-event"></a>3.2.5. Событие покупки
Это событие должно использоваться всякий раз, когда пользователь выкупает свою корзину.

Параметры

* event (строка) — purchase.
* items ( Purchased[] ) — массив, содержащий запись для каждого приобретенного элемента.<br><br>
   Формат приобретенных элементов
  * item (строка) — уникальный идентификатор элемента.
  * count (целое число или строка) — количество приобретенных элементов.
  * price (число с плавающей запятой или строка) (дополнительное поле) — цена элемента.

В примере ниже показано приобретение 3 элементов (33, 34, 35), два со всеми заполненными полями (элемент, количество, цена) и одним (элемент 34) без цены.

        <script>
            if ( typeof AzureMLRecommendationsEvent == "undefined"){ AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({ event: "purchase", items: [{ item: "33", count: "1", price: "10" }, { item: "34", count: "2" }, { item: "35", count: "1", price: "210" }] });
        </script>

#### <a name="326-user-login-event"></a>3.2.6. Событие входа пользователя
Библиотека рекомендаций Azure ML создает и использует файл cookie, чтобы определить события, полученные от аналогичного браузера. Чтобы улучшить результаты модели, рекомендации Azure ML позволяют пользователю установить уникальный идентификатор, который переопределит используемые файлы cookie.

Это событие должно использоваться после входа пользователя на ваш веб-сайт.

Параметры

* event (строка) — userlogin.
* user (строка) — уникальный идентификатор пользователя.
  
        <script>
            if (typeof AzureMLRecommendationsEvent=="undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({event: "userlogin", user: “ABCD10AA” });
        </script>

## <a name="4-consume-recommendations-via-javascript"></a>4. Использование рекомендаций с помощью JavaScript
Код, использующий рекомендацию, запускается через то же событие JavaScript на веб-странице клиента. Ответ на рекомендацию включает идентификаторы рекомендуемых элементов, их имена и рейтинги. Рекомендуется использовать этот параметр только для отображения списка рекомендуемых элементов. Более сложная обработка (например, добавление метаданных элемента) должна выполняться при интеграции на стороне сервера.

### <a name="41-consume-recommendations"></a>4.1. Потребление рекомендаций
Чтобы использовать рекомендации, потребуется включить необходимые библиотеки JavaScript в вашу страницу и вызвать метод AzureMLRecommendationsStart. См. раздел 2.

Чтобы использовать рекомендации для одного или нескольких элементов, потребуется вызвать метод AzureMLRecommendationsGetI2IRecommendation.

Параметры

* items (массив строк) — один или несколько элементов, по которым будут получены рекомендации. При использовании сборки Fbt здесь можно задать только один элемент.
* numberOfResults (целое число) — количество необходимых результатов.
* includeMetadata (логический, необязательный) — если задано значение true, указывает, что поле метаданных должно заполняться в результате.
* Функция обработки — функция, которая будет обрабатывать возвращенные рекомендации. Данные возвращаются как массив следующих объектов:
  * item — уникальный идентификатор элемента.
  * name — имя элемента (если существует в каталоге).
  * rating — рейтинг рекомендации.
  * metadata — строка, представляющая метаданные элемента.

Например, в следующем коде выполняется запрос 8 рекомендаций для элемента 64f6eb0d-947a-4c18-a16c-888da9e228ba (при этом значение параметра includeMetadata не указано, а это означает, что возврат метаданных не требуется), после чего результаты помещаются в буфер.

        <script>
             var reco = AzureMLRecommendationsGetI2IRecommendation(["64f6eb0d-947a-4c18-a16c-888da9e228ba"], 8, false, function (reco) {
                 var buff = "";
                 for (var ii = 0; ii < reco.length; ii++) {
                       buff += reco[ii].item + "," + reco[ii].name + "," + reco[ii].rating + "\n";
                 }
                 alert(buff);
            });
        </script>


[1]: ./media/machine-learning-recommendation-api-javascript-integration/Drawing1.png
[2]: ./media/machine-learning-recommendation-api-javascript-integration/Drawing2.png
[3]: ./media/machine-learning-recommendation-api-javascript-integration/Drawing3.png

