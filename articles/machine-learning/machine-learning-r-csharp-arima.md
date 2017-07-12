---

ROBOTS: NOINDEX, NOFOLLOW
redirect_url: https://gallery.cortanaintelligence.com/
ms.translationtype: Human Translation
ms.sourcegitcommit: f6006d5e83ad74f386ca23fe52879bfbc9394c0f
ms.openlocfilehash: be75a60762674ab347ab7adaddfb17c4208a2b9e
ms.contentlocale: ru-ru
ms.lasthandoff: 05/03/2017


---
<a id="deprecated-forecasting---autoregressive-integrated-moving-average-arima" class="xliff"></a>

# (Не рекомендуется.) Прогнозирование. Интегрированное скользящее среднее с авторегрессией (ARIMA)

> [!NOTE]
> Работа Microsoft DataMarket прекращается, и этот API больше не поддерживается. 
> 
> Много полезных примеров экспериментов и API можно найти в [коллекции Cortana Intelligence](http://gallery.cortanaintelligence.com). Дополнительные сведения о коллекции см. в статье [Поиск ресурсов в коллекции Cortana Intelligence и обмен ими](machine-learning-gallery-how-to-use-contribute-publish.md).


Эта [служба](https://datamarket.azure.com/dataset/aml_labs/arima) использует модель ARIMA (интегрированное скользящее среднее с авторегрессией) для формирования прогнозов на основе предоставленных пользователем данных журнала. Увеличится ли в этом году спрос на определенный продукт? Могу ли я предсказать объем продаж в сезон Рождества, чтобы эффективно спланировать товарные запасы? Модели прогнозирования позволяют решать такие задачи. Используя прошлые данные в качестве базы, эти модели анализируют скрытые тенденции и сезонные колебания для прогнозирования будущих тенденций. 

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

> Эту веб-службу можно использовать через мобильное приложение, веб-сайт или на локальном компьютере. Веб-служба также служит примером того, как машинное обучение Azure можно использовать для создания веб-служб на основе кода R. Чтобы создать эксперимент с использованием кода R и опубликовать его как веб-службу, достаточно написать несколько строк кода R и нажать несколько кнопок в студии машинного обучения Azure. Затем веб-службу можно опубликовать в Azure Marketplace, и ее смогут применять пользователи и устройства по всему миру, при этом автору веб-службы не придется настраивать инфраструктуру.
> 
> 

<a id="consumption-of-web-service" class="xliff"></a>

## Использование веб-службы
Эта служба принимает 4 аргумента и рассчитывает прогнозы ARIMA.
Входные аргументы:

* Частота: частота необработанных данных (ежедневно, еженедельно, ежемесячно, ежеквартально или ежегодно).
* Горизонт: временные рамки прогноза на будущее.
* Дата: добавление новых данных временных рядов для времени.
* Значение: добавление новых значений данных временных рядов.

Результатом выполнения службы являются рассчитанные значения прогноза. 

Пример вводимых данных: 

* Частота: 12
* Горизонт: 12
* Дата: 1/15/2012;2/15/2012;3/15/2012;4/15/2012;5/15/2012;6/15/2012;7/15/2012;8/15/2012;9/15/2012;10/15/2012;11/15/2012;12/15/2012; 1/15/2013;2/15/2013;3/15/2013;4/15/2013;5/15/2013;6/15/2013;7/15/2013;8/15/2013;9/15/2013;10/15/2013;11/15/2013;12/15/2013; 1/15/2014;2/15/2014;3/15/2014;4/15/2014;5/15/2014;6/15/2014;7/15/2014;8/15/2014;9/15/2014
* Значение: 3.479;3.68;3.832;3.941;3.797;3.586;3.508;3.731;3.915;3.844;3.634;3.549;3.557;3.785;3.782;3.601;3.544;3.556;3.65;3.709;3.682;3.511; 3.429;3.51;3.523;3.525;3.626;3.695;3.711;3.711;3.693;3.571;3.509

> Эта служба, размещенная в Azure Marketplace, является службой на основе OData. Вызвать ее можно методами POST и GET. 
> 
> 

Есть несколько способов использования службы в автоматическом режиме (см. пример приложения [здесь](http://microsoftazuremachinelearning.azurewebsites.net/ArimaForecasting.aspx)).

<a id="starting-c-code-for-web-service-consumption" class="xliff"></a>

### Начало кода C# для использования веб-службы:
    public class Input
    {
        public string frequency;
        public string horizon;
        public string date;
        public string value;
    }

    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
         byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
         return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }


    void Main()
    {
          var input = new Input() { frequency = TextBox1.Text, horizon = TextBox2.Text, date = TextBox3.Text, value = TextBox4.Text };
        var json = JsonConvert.SerializeObject(input);
        var acitionUri =  "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";

          var httpClient = new HttpClient();
           httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere","ChangeToAPIKey");
           httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
          var query = httpClient.PostAsync(acitionUri,new StringContent(json));
          var result = query.Result.Content;
          var scoreResult = result.ReadAsStringAsync().Result;
      }

<a id="creation-of-web-service" class="xliff"></a>

## Создание веб-службы
> Эта веб-служба была создана с помощью системы машинного обучения Azure. Чтобы получить бесплатную пробную версию и вводные видеоматериалы по созданию экспериментов и [публикации веб-служб](machine-learning-publish-a-machine-learning-web-service.md), посетите веб-страницу [azure.com/ml](http://azure.com/ml). Ниже приведен снимок экрана эксперимента, в результате которого была создана веб-служба, и пример кода для каждого модуля в эксперименте.
> 
> 

В службах машинного обучения Azure создан пустой эксперимент. Загружен образец входных данных с заранее заданной схемой данных. Со схемой данных связан модуль [Выполнить сценарий R][execute-r-script], который создает модель прогнозирования ARIMA с использованием функций auto.arima и forecast языка R. 

<a id="experiment-flow" class="xliff"></a>

### Ход эксперимента:
![Создание рабочей области][2]

<a id="module-1" class="xliff"></a>

#### Модуль 1:
    # Add in the CSV file with the data in the format shown below 
![Создание рабочей области][3]    

<a id="module-2" class="xliff"></a>

#### Модуль 2:
    # data input
    data <- maml.mapInputPort(1) # class: data.frame
    library(forecast)

    # preprocessing
    colnames(data) <- c("frequency", "horizon", "dates", "values")
    dates <- strsplit(data$dates, ";")[[1]]
    values <- strsplit(data$values, ";")[[1]]

    dates <- as.Date(dates, format = '%m/%d/%Y')
    values <- as.numeric(values)

    # fit a time-series model
    train_ts<- ts(values, frequency=data$frequency)
    fit1 <- auto.arima(train_ts)
    train_model <- forecast(fit1, h = data$horizon)
    plot(train_model)

    # produce forecasting
    train_pred <- round(train_model$mean,2)
    data.forecast <- as.data.frame(t(train_pred))
    colnames(data.forecast) <- paste("Forecast", 1:data$horizon, sep="")

    # data output
    maml.mapOutputPort("data.forecast");


<a id="limitations" class="xliff"></a>

## Ограничения
Это очень простой пример использования модели прогнозирования ARIMA. Как видно из приведенного выше образца кода, в нем не отслеживаются ошибки, служба предполагает, что все переменные являются непрерывными или положительными значениями, а частота должна быть целым числом больше 1. Длина даты и векторов значений должна быть одинаковой. Переменная даты должна быть в формате "мм/дд/гггг".

<a id="faq" class="xliff"></a>

## Часто задаваемые вопросы
Ознакомиться с часто задаваемыми вопросами по использованию веб-службы и публикации в магазине Marketplace можно [здесь](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-arima/arima-img1.png
[2]: ./media/machine-learning-r-csharp-arima/arima-img2.png
[3]: ./media/machine-learning-r-csharp-arima/arima-img3.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/


