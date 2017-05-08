---

ROBOTS: NOINDEX, NOFOLLOW
redirect_url: https://gallery.cortanaintelligence.com/
translationtype: Human Translation
ms.sourcegitcommit: f6006d5e83ad74f386ca23fe52879bfbc9394c0f
ms.openlocfilehash: 9c7b8e4378cbc19255e60aa177207afe3d552087
ms.lasthandoff: 05/03/2017


---
# <a name="deprecated-lexicon-based-sentiment-analysis"></a>Лексический анализ тональности (устаревшая версия)

> [!NOTE]
> Работа Microsoft DataMarket прекращается, и этот API больше не поддерживается. 
> 
> Много полезных примеров экспериментов и API можно найти в [коллекции Cortana Intelligence](http://gallery.cortanaintelligence.com). Дополнительные сведения о коллекции см. в статье [Поиск ресурсов в коллекции Cortana Intelligence и обмен ими](machine-learning-gallery-how-to-use-contribute-publish.md).

Как можно измерить мнения пользователей и их отношение к торговым маркам или темам в социальных сетях, например публикациях в Facebook, твитах, рецензиях и т. д.? Анализ мнений позволяет изучать такие вопросы.

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Можно выделить два основных метода анализа мнений. Один из них — алгоритм контролируемого обучения, а другой можно рассматривать как неконтролируемое. Алгоритм контролируемого обучения строит модель классификации на основе большой совокупности данных с примечаниями. Его точность в основном зависит от качества примечаний, а процесс обучения обычно занимает долгое время. Кроме того, при применении этого алгоритма к другой предметной области результат, как правило, оказывается не очень хорошим. В отличие от контролируемого обучения, лексический метод неконтролируемого обучения использует словарь мнений и не требует хранения большого объема данных и обучения, что существенно ускоряет весь процесс. 

Наша [служба](https://datamarket.azure.com/dataset/aml_labs/lexicon_based_sentiment_analysis) создана на основе MPQA Subjectivity Lexicon (http://mpqa.cs.pitt.edu/lexicons/subj_lexicon/), одного из самых распространенных лексиконов субъективных мнений. Он включает 5097 слов для отрицательной оценки и 2533 слова — для положительной. Все они обозначены как имеющие сильную или слабую полярность. Весь массив предоставляется на условиях универсальной общедоступной лицензии GNU. Веб-службу можно применять к любым коротким фразам, таким как твиты и записи в Facebook. 

> Пользователи могут работать с этой веб-службой через мобильное приложение, веб-сайт или даже локальный компьютер. Веб-служба также служит примером того, как машинное обучение Azure можно использовать для создания веб-служб на основе кода R. Чтобы создать эксперимент с использованием кода R и опубликовать его как веб-службу, достаточно написать несколько строк кода R и нажать несколько кнопок в студии машинного обучения Azure. Затем веб-службу можно опубликовать в Azure Marketplace, и ее смогут применять пользователи и устройства по всему миру, при этом автору веб-службы не придется настраивать инфраструктуру.
> 
> 

## <a name="consumption-of-web-service"></a>Использование веб-службы
Входными данными может быть любой текст, но веб-служба показывает лучшие результаты для коротких фраз. На выходе она выдает числовое значение в диапазоне от -1 до 1. Любое значение меньше 0 означает отрицательный характер текста, больше 0 — положительный. Абсолютное значение результата определяет степень уверенности данного мнения. 

> Эта служба, размещенная в Azure Marketplace, является службой на основе OData. Вызвать ее можно методами POST и GET. 
> 
> 

Есть несколько способов использования службы в автоматическом режиме (см. пример приложения [здесь](http://microsoftazuremachinelearning.azurewebsites.net/)).

### <a name="starting-c-code-for-web-service-consumption"></a>Начало кода C# для использования веб-службы:
    public class ScoreResult
    {
            [DataMember]
            public double result
            {
                get;
                set;
            }
    }

    void main()
    {
            using (var wb = new WebClient())
            {
                var acitionUri = new Uri("PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score");
                DataServiceContext ctx = new DataServiceContext(acitionUri);
                var cred = new NetworkCredential("PutEmailAddressHere", "ChangeToAPIKey");
                var cache = new CredentialCache();

                cache.Add(acitionUri, "Basic", cred);
                ctx.Credentials = cache;
                var query = ctx.Execute<ScoreResult>(acitionUri, "POST", true, new BodyOperationParameter("Text", TextBox1.Text));
                ScoreResult scoreResult = query.ElementAt(0);
                double result = scoreResult.result;
            }
    }



Входные данные: "Сегодня хороший день". Служба возвращает значение 1, указывая на положительное мнение, высказанное в исходной фразе. 

## <a name="creation-of-web-service"></a>Создание веб-службы
> Эта веб-служба была создана с помощью системы машинного обучения Azure. Чтобы получить бесплатную пробную версию и вводные видеоматериалы по созданию экспериментов и [публикации веб-служб](machine-learning-publish-a-machine-learning-web-service.md), посетите веб-страницу [azure.com/ml](http://azure.com/ml). Ниже приведен снимок экрана эксперимента, в результате которого была создана веб-служба, и пример кода для каждого модуля в эксперименте.
> 
> 

В службах машинного обучения Azure создан пустой эксперимент. На рисунке ниже показан ход эксперимента лексического анализа мнений. Файл sent_dict.csv — это лексикон субъективных мнений MPQA, который передается на ввод модуля [Выполнить сценарий R][execute-r-script]. Еще один входной аргумент — проверяемый образец из набора данных обзоров Amazon, для которого была проведена выборка, изменены названия столбцов и выполнено разделение. Для хранения в памяти лексикона субъективных мнений и ускорения расчета оценки мы использовали пакет функций хэширования. Пакет tm выполняет разметку текста, после чего он сравнивается со словом в словаре мнений. Итоговая оценка рассчитывается путем применения весовой характеристики каждого слова субъективной оценки в тексте. 

### <a name="experiment-flow"></a>Ход эксперимента:
![Ход эксперимента][2]

#### <a name="module-1"></a>Модуль 1:
    # Map 1-based optional input ports to variables
    sent_dict_data<- maml.mapInputPort(1) # class: data.frame
    dataset2 <- maml.mapInputPort(2) # class: data.frame

# <a name="install-hash-package"></a>Установка пакета функций хэширования
    install.packages("src/hash_2.2.6.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("hash", lib.loc = ".", logical.return = TRUE, verbose = TRUE)
    library(tm)
    library(stringr)

    #create sentiment dictionary
    negation_word <- c("not","nor", "no")
    result <- c()
    sent_dict <- hash()
    sent_dict <- hash(sent_dict_data$word, sent_dict_data$polarity)

    #  Compute sentiment score for each document
    for (m in 1:nrow(dataset2)){
    polarity_ratio <- 0
    polarity_total <- 0
    not <- 0
    sentence <- tolower(dataset2[m,1])
    if (nchar(sentence) > 0){
        token_array <- scan_tokenizer(sentence)
        for (j in 1:length(token_array)){
            word = str_replace_all(token_array[j], "[^[:alnum:]]", "")
            for (k in 1:length(negation_word)){
              if (word == negation_word[k]){
                not <- (not+1) %% 2

              }
            }
            if (word != ""){
                if (!is.null(sent_dict[[word]])){
                  polarity_ratio <- polarity_ratio + (-2*not+1)*sent_dict[[word]]
                  polarity_total <- polarity_total + abs(sent_dict[[word]])
                }
            }

        }
    }
    if (polarity_total > 0){
        result <- c(result, polarity_ratio/polarity_total)
    }else{
        result<- c(result,0)
    }
    }

    # Sample operation
    data.set <- data.frame(result)

    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("data.set")



## <a name="limitations"></a>Ограничения
С алгоритмической точки зрения лексический анализ мнений — это общий инструмент анализа мнений, показывающий результаты не лучше, чем метод классификации на основе определенных полей. Проблема отрицания не была решена качественным образом. Мы внесли несколько слов отрицания в код программы, однако вместо этого следовало бы использовать словарь отрицаний в сочетании с некоторым набором правил. Веб-служба показывает лучшие результаты на коротких и простых предложениях, таких как твиты и записи в Facebook, чем на длинных и сложных предложениях, таких как обзоры Amazon. 

## <a name="faq"></a>Часто задаваемые вопросы
Ознакомиться с часто задаваемыми вопросами по использованию веб-службы и публикации в Azure Marketplace можно [здесь](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-lexicon-based-sentiment-analysis/sentiment_analysis_1.png
[2]: ./media/machine-learning-r-csharp-lexicon-based-sentiment-analysis/sentiment_analysis_2.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/



