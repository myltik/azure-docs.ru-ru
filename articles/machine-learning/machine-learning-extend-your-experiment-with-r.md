<properties
	pageTitle="Расширение возможностей эксперимента с помощью R | Microsoft Azure"
	description="Рассматривается, как расширить функциональные возможности Студии машинного обучения Microsoft Azure с помощью языка R, используя модуль «Выполнение сценария R»."
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/19/2016"
	ms.author="garye" />


#Расширение возможностей эксперимента с помощью R

Вы можете расширить функциональные возможности Студии машинного обучения Microsoft Azure с помощью языка R, используя модуль [Выполнение сценария R][execute-r-script].

Этот модуль принимает несколько входных наборов данных и выдает один выходной набор данных. Вы можете ввести сценарий R в качестве значения параметра **Сценарий R** в модуле [Выполнение сценария R][execute-r-script].

Для доступа к каждому входному порту модуля используется код, аналогичный приведенному ниже:

    dataset1 <- maml.mapInputPort(1)

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##Вывод списка всех установленных пакетов

Список установленных пакетов может меняться. Чтобы получить полный актуальный список установленных пакетов, включая описание каждого пакета, введите следующий код в модуле [Выполнение сценария R][execute-r-script]\:

    out <- data.frame(installed.packages(,,,fields="Description"))
	maml.mapOutputPort("out")

При этом список пакетов будет отправлен в выходной порт модуля [Выполнение сценария R][execute-r-script]. Чтобы просмотреть список пакетов, подключите модуль преобразования, например [Преобразование в CSV-файл][convert-to-csv], к выходным данным модуля [Выполнение сценария R][execute-r-script] слева, выполните эксперимент, а затем щелкните выходные данные модуля преобразования и выберите **Скачать**.

![](./media/machine-learning-extend-your-experiment-with-r/download-package-list.png)


##Импорт пакетов

Вы также можете импортировать пакеты, которые еще не установлены из промежуточного репозитория Студии машинного обучения Microsoft Azure, с помощью следующих команд в модуле [Выполнение сценария R][execute-r-script] и ZIP-архива пакета:

    install.packages("src/my_favorite_package.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("my_favorite_package", lib.loc = ".", logical.return = TRUE, verbose = TRUE)

`my_favorite_package.zip` здесь содержит ZIP-архив вашего пакета.


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/

<!---HONumber=AcomDC_0824_2016-->