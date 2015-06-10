<properties
   pageTitle="Большие вычисления в Azure: технические ресурсы для Пакетных служб и высокопроизводительных вычислительных систем (HPC)"
   description="Это руководство по техническим ресурсам поможет вам выполнять крупномасштабные параллельные, пакетные задания и рабочие нагрузки HPC в Azure."
   services="batch, cloud-services, virtual-machines"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""/>

<tags
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="big-compute"
   ms.date="04/28/2015"
   ms.author="danlep"/>

# Большие вычисления в Azure: технические ресурсы для Пакетных служб и высокопроизводительных вычислительных систем (HPC)
Это руководство по техническим ресурсам поможет вам выполнять крупномасштабные параллельные, пакетные задания и рабочие нагрузки HPC в Azure. Перенесите текущие пакетные задания или рабочие нагрузки HPC в облако Azure или создайте новые решения для больших вычислений в Azure с помощью набора служб Azure.

## Параметры решения


Дополнительные сведения о параметрах больших вычислений в Azure и выбор правильного подхода для ваших рабочих нагрузок и требований бизнеса.

* [Обзор](https://msdn.microsoft.com/library/azure/dn482130.aspx)

* [Видео: Большие вычисления в облаке с Azure и HPC](http://azure.microsoft.com/documentation/videos/teched-europe-2014-big-compute-in-the-cloud-with-high-performance-computing-on-azure/)


## Пакетная служба Azure

[Пакетная служба](http://azure.microsoft.com/services/batch/) (предварительная версия) — это служба платформ, которая помогает легко переводить приложения в облако и выполнять задания без настройки кластера или планировщика и управления ими. Используйте пакет SDK, чтобы интегрировать клиентские приложения с Пакетной службой Azure на различных языках, хранить данные в Azure и создавать конвейеры выполнения заданий.

* Зарегистрироваться для [предварительной версии ](http://azure.microsoft.com/services/preview/)

* [Документация](http://azure.microsoft.com/documentation/services/batch/)

* [Справочник по API](https://msdn.microsoft.com/library/azure/dn820177.aspx)

* [Форум Пакетной службы](https://social.msdn.microsoft.com/Forums/home?forum=azurebatch)

* [Учебник «Приступая к работе с библиотекой Пакетной службы Azure для .NET»](batch-dotnet-get-started.md)

* [Видео: Введение в Пакетную службу Azure](http://azure.microsoft.com/documentation/videos/introduction-to-azure-batch-with-mark-scurrell/)

* [Видео: Демонстрация Пакетной службы Azure (Марк Руссинович (Mark Russinovich))](http://azure.microsoft.com/documentation/videos/azure-batch-demo-blender/)

* [Видео: Создание Пакетной службы](http://azure.microsoft.com/documentation/videos/azure-batch-build-a-batch-service/)

## Кластерные решения HPC

Перенесите или расширьте свой существующий кластер Windows HPC, чтобы выполнять интенсивные вычислительные рабочие нагрузки в Azure.

### Пакет Microsoft HPC

[Пакет HPC](https://technet.microsoft.com/library/cc514029) – это бесплатный диспетчер кластеров корпорации Майкрософт и планировщик заданий для локальных, гибридных и облачных HPC.

* [Расширение в Azure с пакетом HPC](https://technet.microsoft.com/library/gg481749.aspx)

* [Пакет HPC на виртуальных машинах Azure](https://msdn.microsoft.com/library/azure/dn518135.aspx)

* [Учебник: Настройка гибридного кластера с пакетом HPC в Azure](cloud-services-setup-hybrid-hpcpack-cluster.md)

* [Форумы по Windows HPC](https://social.microsoft.com/Forums/home?category=windowshpc)

## Microsoft MPI

[Microsoft MPI](https://msdn.microsoft.com/library/bb524831.aspx) (MS-MPI) – это реализация корпорацией Майкрософт стандарта интерфейса передачи сообщений для разработки и выполнения параллельных приложений на платформе Windows.


* [Скачать MS-MPI](http://go.microsoft.com/FWLink/p/?LinkID=389556)

* [Справочник по MS-MPI](https://msdn.microsoft.com/library/dn473458.aspx)

* [Форум MPI](https://social.microsoft.com/Forums/home?forum=windowshpcmpi)


## Экземпляры с большим объемом вычислений

Azure предлагает [диапазон размеров](https://msdn.microsoft.com/library/azure/dn197896.aspx), в том числе [экземпляры A8, A9, A10 и A11](https://msdn.microsoft.com/library/azure/dn689095.aspx), для запуска пакетных заданий и рабочих нагрузок HPC.


* [Экземпляры A8 и A9: быстрый запуск с пакетом HPC](https://msdn.microsoft.com/library/azure/dn594431.aspx)

* [Запуск приложений MPI на экземплярах A8 и A9](https://msdn.microsoft.com/library/azure/dn592104.aspx)

## Проекты архитектуры

* В документе [Крупномасштабные вычисления — финансовые службы](http://go.microsoft.com/fwlink/?LinkId=536378) (PDF) объясняется, как вводить в действие и управлять крупномасштабными вычислениями и анализом данных в облаке для управления рисками, отчетности и моделирования.

## Примеры и образцы

* [Образцы кода Пакетной службы Azure](https://code.msdn.microsoft.com/site/search?f[0].Type=Topic&f[0].Value=Azure%20Batch&f[0].Text=Azure%20Batch)

* [Образец приложений Пакетной службы Blender](https://github.com/Azure/azure-batch-apps-blender) и [запись блога](http://azure.microsoft.com/blog/2015/01/26/blender-on-azure-batch/)

## Связанные службы Azure

* [Фабрика данных](http://azure.microsoft.com/documentation/services/data-factory/)

* [Машинное обучение](http://azure.microsoft.com/documentation/services/machine-learning/)

* [HDInsight](http://azure.microsoft.com/documentation/services/hdinsight/)

* [Виртуальные машины](http://azure.microsoft.com/documentation/services/virtual-machines/)

* [Облачные службы](http://azure.microsoft.com/documentation/services/cloud-services/)

* [Службы мультимедиа](http://azure.microsoft.com/documentation/services/media-services/)



## Дальнейшие действия

* Последних объявления см. в [блоге группы Microsoft HPC и Пакетной службы](http://blogs.technet.com/b/windowshpc/) и [блоге Azure](http://azure.microsoft.com/blog/tag/hpc/).
* См. также [что нового в Пакетной службе](http://azure.microsoft.com/updates/?service=batch) или подпишитесь на [RSS-канал](http://azure.microsoft.com/updates/feed/?service=batch).

<!---HONumber=58-->