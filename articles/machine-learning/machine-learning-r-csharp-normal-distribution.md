---
title: "Набор веб-служб нормального распределения Azure (устаревшая версия) | Документация Майкрософт"
description: "Сведения о наборе веб-служб нормального распределения (устаревшая версия)."
services: machine-learning
documentationcenter: 
author: ireiter
manager: jhubbard
editor: cgronlun
ms.assetid: aab7b92e-953b-43d8-b0af-031394406bfe
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/06/2017
ms.author: ireiter
ROBOTS: NOINDEX
redirect_url: https://gallery.cortanaintelligence.com/
redirect_document_id: TRUE
ms.translationtype: Human Translation
ms.sourcegitcommit: f6ad106e769c807d1c281c8d19127eabc2048f30
ms.openlocfilehash: da7b70171d13c82e9269064069e69a35b9b2daaa
ms.contentlocale: ru-ru
ms.lasthandoff: 01/11/2017


---
# <a name="deprecated-normal-distribution-suite"></a>Набор нормального распределения (устаревшая версия)

> [!NOTE]
> Работа Microsoft DataMarket прекращается, и этот API больше не поддерживается. 
> 
> Много полезных примеров экспериментов и API можно найти в [коллекции Cortana Intelligence](http://gallery.cortanaintelligence.com). Дополнительные сведения о коллекции см. в статье [Поиск ресурсов в коллекции Cortana Intelligence и обмен ими](machine-learning-gallery-how-to-use-contribute-publish.md).

Набор веб-служб нормального распределения состоит из трех веб-служб ([Генератор](https://datamarket.azure.com/dataset/aml_labs/ndg7), [Калькулятор квантилей](https://datamarket.azure.com/dataset/aml_labs/ndq5), [Калькулятор вероятностей](https://datamarket.azure.com/dataset/aml_labs/ndp5)), предназначенных для создания нормальных распределений и работы с ними. С их помощью можно создать нормальное распределение любой длины, рассчитать квантили для любой заданной вероятности, а также вероятность — для заданного квантиля. Каждая служба выдает собственный набор выходных данных (см. описание ниже). В основе набора веб-служб нормального распределения лежит использование функций qnorm, rnorm и pnorm, которые входят в пакет статистических функций на языке R.

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

> Эту веб-службу можно использовать через мобильное приложение, веб-сайт или на локальном компьютере. Веб-служба также служит примером того, как машинное обучение Azure можно использовать для создания веб-служб на основе кода R. Чтобы создать эксперимент с использованием кода R и опубликовать его как веб-службу, достаточно написать несколько строк кода R и нажать несколько кнопок в студии машинного обучения Azure. Затем веб-службу можно опубликовать в Azure Marketplace, и ее смогут применять пользователи и устройства по всему миру, при этом автору веб-службы не придется настраивать инфраструктуру.  
> 
> 

## <a name="consumption-of-web-service"></a>Использование веб-службы
В состав набора веб-служб нормального распределения входят перечисленные ниже три службы.

### <a name="normal-distribution-quantile-calculator"></a>Калькулятор квантилей нормального распределения
Эта служба принимает четыре аргумента нормального распределения и рассчитывает соответствующий квантиль.

Входные аргументы:

* p — одна вероятность события с нормальным распределением. 
* Mean — среднее значение нормального распределения.
* SD — стандартное отклонение нормального распределения. 
* Side — L для нижней или U для верхней части распределения.

На выходе служба выдает рассчитанный квантиль, связанный с заданной вероятностью.

### <a name="normal-distribution-probability-calculator"></a>Калькулятор вероятности нормального распределения
Эта служба принимает четыре аргумента нормального распределения и рассчитывает соответствующую вероятность.

Входные аргументы:

* q — один квантиль события с нормальным распределением. 
* Mean — среднее значение нормального распределения.
* SD — стандартное отклонение нормального распределения. 
* Side — L для нижней или U для верхней части распределения.

На выходе служба выдает рассчитанную вероятность, связанную с заданным квантилем.

### <a name="normal-distribution-generator"></a>Генератор нормального распределения
Эта служба принимает три аргумента нормального распределения и создает случайную последовательность нормально распределенных чисел. Ниже описаны аргументы, передаваемые этой службе в запросе.

* n — количество наблюдений. 
* Mean — среднее значение нормального распределения.
* SD — стандартное отклонение нормального распределения. 

На выходе служба выдает последовательность чисел длины n с нормальным распределением на основе аргументов mean и sd.

> Эта служба, размещенная в Azure Marketplace, является службой на основе OData. Вызвать ее можно методами POST и GET. 
> 
> 

Автоматизировать использование этой службы можно несколькими способами (примеры приложений: [Генератор](http://microsoftazuremachinelearning.azurewebsites.net/NormalDistributionGenerator.aspx), [Калькулятор вероятности](http://microsoftazuremachinelearning.azurewebsites.net/NormalDistributionProbabilityCalculator.aspx), [Калькулятор квантилей](http://microsoftazuremachinelearning.azurewebsites.net/NormalDistributionQuantileCalculator.aspx)).

### <a name="starting-c-code-for-web-service-consumption"></a>Начало кода C# для использования веб-службы:
### <a name="normal-distribution-quantile-calculator"></a>Калькулятор квантилей нормального распределения
    public class Input
    {
            public string p;
            public string mean;
            public string sd;
            public string side;
    }

    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }

    void Main()
    {
            var input = new Input() { p = TextBox1.Text, mean = TextBox2.Text, sd = TextBox3.Text, side = TextBox4.Text };
            var json = JsonConvert.SerializeObject(input);
            var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
            var httpClient = new HttpClient();

            httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
            httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));

            var response = httpClient.PostAsync(acitionUri, new StringContent(json));
            var result = response.Result.Content;
            var scoreResult = result.ReadAsStringAsync().Result;
    }


### <a name="normal-distribution-probability-calculator"></a>Калькулятор вероятности нормального распределения
    public class Input
    {
            public string q;
            public string mean;
            public string sd;
            public string side;
    }

    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }

    void Main()
    {
            var input = new Input() { q = TextBox1.Text, mean = TextBox2.Text, sd = TextBox3.Text, side = TextBox4.Text };
            var json = JsonConvert.SerializeObject(input);
            var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
            var httpClient = new HttpClient();

            httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
            httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));

            var response = httpClient.PostAsync(acitionUri, new StringContent(json));
            var result = response.Result.Content;
            var scoreResult = result.ReadAsStringAsync().Result;
    }

### <a name="normal-distribution-generator"></a>Генератор нормального распределения
    public class Input
    {
            public string n;
            public string mean;
            public string sd;
    }

    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }

    void Main()
    {
            var input = new Input() { n = TextBox1.Text, mean = TextBox2.Text, sd = TextBox3.Text };
            var json = JsonConvert.SerializeObject(input);
            var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
            var httpClient = new HttpClient();

            httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
            httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));

            var response = httpClient.PostAsync(acitionUri, new StringContent(json));
            var result = response.Result.Content;
            var scoreResult = result.ReadAsStringAsync().Result;
    }


## <a name="creation-of-web-service"></a>Создание веб-службы
> Эта веб-служба была создана с помощью системы машинного обучения Azure. Чтобы получить бесплатную пробную версию и вводные видеоматериалы по созданию экспериментов и [публикации веб-служб](machine-learning-publish-a-machine-learning-web-service.md), посетите веб-страницу [azure.com/ml](http://azure.com/ml). 
> 
> 

Ниже приведен снимок экрана эксперимента, в результате которого была создана веб-служба, и пример кода для каждого модуля в эксперименте.

### <a name="normal-distribution-quantile-calculator"></a>Калькулятор квантилей нормального распределения
Ход эксперимента:

![Ход эксперимента][2]

    #Data schema with example data (replaced with data from web service)
    data.set=data.frame(p=0.1,mean=0,sd=1,side='L');
    maml.mapOutputPort("data.set"); #send data to output port

    # Map 1-based optional input ports to variables
    dataset1 <- maml.mapInputPort(1) # class: data.frame

    param = dataset1
    if (param$p < 0 ) {
    print('Bad input: p must be between 0 and 1')
    param$p = 0
    } else if (param$p > 1) {
    print('Bad input: p must be between 0 and 1')
    param$p = 1
    }
    q = qnorm(param$p,mean=param$mean,sd=param$sd)

    if (param$side == 'U'){
    q = 2* param$mean - q
    } else if (param$side =='L') {
    q = q
    } else {
    print("Invalid side choice")
    }

    output = as.data.frame(q)

    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");

### <a name="normal-distribution-probability-calculator"></a>Калькулятор вероятности нормального распределения
Ход эксперимента:

![Ход эксперимента][3]

     #Data schema with example data (replaced with data from web service)
    data.set=data.frame(q=-1,mean=0,sd=1,side='L');
    maml.mapOutputPort("data.set"); #send data to output port

    # Map 1-based optional input ports to variables
    dataset1 <- maml.mapInputPort(1) # class: data.frame

    param = dataset1
    prob = pnorm(param$q,mean=param$mean,sd=param$sd)

    if (param$side == 'U'){
    prob = 1 - prob
    } else if (param$side =='B') {
    prob = ifelse(prob<=0.5,prob * 2, 1)
    } else if (param$side =='L') {
    prob = prob
    } else {
    print("Invalid side choice")
    }

    output = as.data.frame(prob)

    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");

### <a name="normal-distribution-generator"></a>Генератор нормального распределения
Ход эксперимента:

![Ход эксперимента][4]

    #Data schema with example data (replaced with data from web service)
    data.set=data.frame(n=50,mean=0,sd=1);
    maml.mapOutputPort("data.set"); #send data to output port

    # Map 1-based optional input ports to variables
    dataset1 <- maml.mapInputPort(1) # class: data.frame

    param = dataset1
    dist = rnorm(param$n,mean=param$mean,sd=param$sd)

    output = as.data.frame(t(dist))

    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");

## <a name="limitations"></a>Ограничения
Это очень простой пример, выполняющий операции с нормальными распределениями. Как видно из приведенного выше образца кода, в нем практически не отслеживаются ошибки.

## <a name="faq"></a>Часто задаваемые вопросы
Ознакомиться с часто задаваемыми вопросами по использованию веб-службы и публикации в Azure Marketplace можно [здесь](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-normal-distribution/normal-img1.png
[2]: ./media/machine-learning-r-csharp-normal-distribution/normal-img2.png
[3]: ./media/machine-learning-r-csharp-normal-distribution/normal-img3.png
[4]: ./media/machine-learning-r-csharp-normal-distribution/normal-img4.png


