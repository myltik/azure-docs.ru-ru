---
title: Использование .NET с Hadoop MapReduce в HDInsight на платформе Linux в Azure | Документация Майкрософт
description: Узнайте, как использовать приложения .NET для потоковой передачи MapReduce в HDInsight под управлением Linux.
services: hdinsight
documentationCenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: larryfr
ms.openlocfilehash: 36b8f51122bad6614e63dfc58e09e5c1ca08f83d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31400074"
---
# <a name="migrate-net-solutions-for-windows-based-hdinsight-to-linux-based-hdinsight"></a>Перенос решений .NET из HDInsight под управлением Windows в HDInsight под управлением Linux

Для запуска приложений .NET в кластерах HDInsight под управлением Linux используется [Mono (https://mono-project.com)](https://mono-project.com). Mono позволяет использовать компоненты .NET, такие как приложения MapReduce, с HDInsight под управлением Linux. В этом документе вы узнаете, как перенести решения .NET, созданные для кластеров HDInsight под управлением Windows, на кластеры HDInsight под управлением Linux для работы с Mono.

## <a name="mono-compatibility-with-net"></a>Совместимость Mono с .NET

Mono версии 4.2.1 входит в состав HDInsight версии 3.6. Дополнительные сведения о версии Mono, которая входит в состав HDInsight, см. в разделе [Что представляют собой различные компоненты Hadoop, доступные в HDInsight?](hdinsight-component-versioning.md) Чтобы установить определенную версию Mono, см. статью об [установке или обновлении Mono](hdinsight-hadoop-install-mono.md).

См. дополнительные сведения о [совместимости Mono и .NET (http://www.mono-project.com/docs/about-mono/compatibility/)](http://www.mono-project.com/docs/about-mono/compatibility/).

> [!IMPORTANT]
> Платформа SCP.NET совместима с Mono. Дополнительные сведения об использовании SCP.NET с Mono см. в статье [Разработка топологий для Apache Storm в HDInsight на C# с помощью средств Hadoop для Visual Studio](storm/apache-storm-develop-csharp-visual-studio-topology.md).

## <a name="automated-portability-analysis"></a>Автоматический анализ переносимости

С помощью анализатора [.NET Portability Analyzer](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer) можно создать отчет о несовместимости приложения и Mono. Следуйте приведенным ниже инструкциям по настройке анализатора для проверки приложения на совместимость с Mono.

1. Установите [.NET Portability Analyzer](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer). Во время установки выберите используемую версию Visual Studio.

2. В Visual Studio 2015 выберите __Анализ__ > __Portability Analyzer Settings__ (Параметры анализатора переносимости) и убедитесь, что в разделе __Mono__ установлен флажок __4.5__.

    ![Установленный флажок "4.5" для Mono в разделе параметров анализатора](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-settings.png)

    Нажмите кнопку __ОК__, чтобы сохранить конфигурацию.

3. Выберите __Анализ__ > __Analyze Assembly Portability__ (Анализ переносимости сборки). Выберите сборку, содержащую решение, а затем щелкните __Открыть__, чтобы начать анализ.

4. После завершения анализа выберите __Анализ__ > __View analysis reports__ (Просмотр отчетов об анализе). В разделе __Portability Analysis Results__ (Результаты анализа переносимости) щелкните __Открыть отчет__, чтобы открыть отчет.

    ![Диалоговое окно результатов анализатора переносимости](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-results.png)

> [!IMPORTANT]
> Анализатор не может выявить все проблемы с решением. Например, путь к файлу `c:\temp\file.txt` считается нормальным, если Mono работает под управлением Windows. Тот же путь не является допустимым для платформы Linux.

## <a name="manual-portability-analysis"></a>Ручной анализ переносимости

Вручную выполните аудит кода с помощью информации в документе о [переносимости приложений (http://www.mono-project.com/docs/getting-started/application-portability/)](http://www.mono-project.com/docs/getting-started/application-portability/).

## <a name="modify-and-build"></a>Изменение и выполнение сборки

Можно продолжать использовать Visual Studio для создания решений .NET для HDInsight. Однако необходимо обеспечить настройку проекта для использования .NET Framework 4.5.

## <a name="deploy-and-test"></a>Развертывание и тестирование

После изменения решения с помощью рекомендаций из результатов .NET Portability Analyzer или ручного анализа необходимо протестировать его работу с HDInsight. Тестирование решения в кластере HDInsight под управлением Linux может выявить малозаметные проблемы, которые необходимо устранить. Мы рекомендуем включить дополнительное ведение журнала в приложении во время тестирования.

Дополнительные сведения о доступе к журналам см. в следующих документах:

* [Доступ к журналам приложений YARN в HDInsight под управлением Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md)

## <a name="next-steps"></a>Дополнительная информация

* [Использование языка C# для потоковой передачи MapReduce в Hadoop в HDInsight](hadoop/apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

* [Использование определяемых пользователем функций C# при потоковой передаче Hive и Pig в Hadoop HDInsight](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Разработка топологий для Apache Storm в HDInsight на C# с помощью средств Hadoop для Visual Studio](storm/apache-storm-develop-csharp-visual-studio-topology.md)
