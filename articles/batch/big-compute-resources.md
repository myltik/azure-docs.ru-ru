<properties
   pageTitle="Большие вычисления в Azure: технические ресурсы для Пакетных служб и высокопроизводительных вычислительных систем (HPC)"
   description="В этой статье приведены технические ресурсы, которые помогут вам выполнять крупномасштабные параллельные и пакетные задания, а также рабочие нагрузки HPC в Azure."
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
   ms.date="05/29/2015"
   ms.author="danlep"/>

# Большие вычисления в Azure: технические ресурсы для Пакетных служб и высокопроизводительных вычислительных систем (HPC)
Это руководство по техническим ресурсам поможет вам выполнять крупномасштабные параллельные и пакетные задания, а также рабочие нагрузки HPC в Azure. Перенесите свои текущие пакетные задания или рабочие нагрузки HPC в облако Azure или создайте в Azure новые решения для больших вычислений с помощью набора служб Azure.

## Варианты решений


Узнайте, какие решения для больших вычислений существуют в Azure, и выберите вариант, который подходит для рабочих нагрузок и потребностей вашей организации.

* [Обзор](https://msdn.microsoft.com/library/azure/dn482130.aspx)

* [Большие вычисления в облаке с помощью Azure и HPC (видео)](http://azure.microsoft.com/documentation/videos/teched-europe-2014-big-compute-in-the-cloud-with-high-performance-computing-on-azure/)


## Пакетная служба Azure

[Пакетная служба](http://azure.microsoft.com/services/batch/) (предварительная версия) — это платформа как услуга, которая позволяет легко переводить приложения в облако и выполнять задания без настройки кластера или планировщика и управления ими. С помощью пакета SDK вы можете интегрировать клиентские приложения в Пакетную службу Azure, используя различные языки, размещать данные в Azure и создавать конвейеры выполнения заданий.

* Регистрация в [предварительной версии](http://azure.microsoft.com/services/preview/)

* [Документация](http://azure.microsoft.com/documentation/services/batch/)

* [Справочник по API](https://msdn.microsoft.com/library/azure/dn820177.aspx)

* [Форум по Пакетной службе](https://social.msdn.microsoft.com/Forums/home?forum=azurebatch)

* [Учебник «Приступая к работе с библиотекой Пакетной службы Azure для .NET»](batch-dotnet-get-started.md)

* [Видеоролики о Пакетной службе](https://azure.microsoft.com/documentation/videos/index/?services=batch)

## Кластерные решения HPC

Перенесите или расширьте свой существующий кластер HPC под управлением Windows в службу Azure и выполняйте ресурсоемкие вычислительные рабочие нагрузки в облаке.

### Пакет Microsoft HPC

[Пакет HPC](https://technet.microsoft.com/library/cc514029) — это бесплатный диспетчер кластеров и планировщик заданий от Майкрософт для локальных, гибридных и облачных систем HPC.

* [Использование пакета HPC совместно с Azure](https://technet.microsoft.com/library/gg481749.aspx)

* [Пакет HPC на виртуальных машинах Azure](https://msdn.microsoft.com/library/azure/dn518135.aspx)

* [Учебник: настройка гибридного кластера с пакетом HPC в Azure](../cloud-services-setup-hybrid-hpcpack-cluster.md)

* [Форумы по Windows HPC](https://social.microsoft.com/Forums/home?category=windowshpc)

## Microsoft MPI

[Microsoft MPI](https://msdn.microsoft.com/library/bb524831.aspx) (MS-MPI) — это реализация стандарта MPI (Message Passing Interface — интерфейс передачи сообщений) от Майкрософт, предназначенная для разработки и запуска параллельных приложений на платформе Windows.


* [Скачать MS-MPI](http://go.microsoft.com/FWLink/p/?LinkID=389556)

* [Справочник по MS-MPI](https://msdn.microsoft.com/library/dn473458.aspx)

* [Форум по MPI](https://social.microsoft.com/Forums/home?forum=windowshpcmpi)


## Экземпляры для ресурсоемких вычислений

В Azure предлагается [целый ряд экземпляров разных размеров](https://msdn.microsoft.com/library/azure/dn197896.aspx), включая [размеры A8, A9, A10 и A11](https://msdn.microsoft.com/library/azure/dn689095.aspx), для выполнения пакетных заданий и рабочих нагрузок HPC.


* [Экземпляры A8 и A9: краткое руководство для пакета HPC](https://msdn.microsoft.com/library/azure/dn594431.aspx)

* [Запуск приложений MPI на экземплярах A8 и A9](https://msdn.microsoft.com/library/azure/dn592104.aspx)

## Проекты архитектуры

* В PDF-документе [Крупномасштабные вычисления: финансовые услуги](http://go.microsoft.com/fwlink/?LinkId=536378) описывается ввод в эксплуатацию систем для крупномасштабных вычислений и анализа данных в облаке и управление такими системами. В качестве примера рассматриваются такие направления, как управление рисками, отчетность и моделирование.

## Примеры и образцы

* [Примеры кода для Пакетной службы Azure](https://github.com/Azure/azure-batch-samples)

* [Образец приложения Blender для приложений Пакетной службы](https://github.com/Azure/azure-batch-apps-blender) и [запись блога](http://azure.microsoft.com/blog/2015/01/26/blender-on-azure-batch/)

## Связанные службы Azure

* [Фабрика данных](http://azure.microsoft.com/documentation/services/data-factory/)

* [Машинное обучение](http://azure.microsoft.com/documentation/services/machine-learning/)

* [HDInsight](http://azure.microsoft.com/documentation/services/hdinsight/)

* [Виртуальные машины](http://azure.microsoft.com/documentation/services/virtual-machines/)

* [Облачные службы](http://azure.microsoft.com/documentation/services/cloud-services/)

* [Службы мультимедиа](http://azure.microsoft.com/documentation/services/media-services/)



## Дальнейшие действия

* Последние объявления см. в [блоге группы Microsoft HPC и Пакетной службы](http://blogs.technet.com/b/windowshpc/), а также в [блоге Azure](http://azure.microsoft.com/blog/tag/hpc/).
* Следите также за лентой [новостей о Пакетной службе](http://azure.microsoft.com/updates/?service=batch) или подпишитесь на [RSS-канал](http://azure.microsoft.com/updates/feed/?service=batch).

<!---HONumber=58_postMigration-->