---
title: Установка опубликованного приложения Datameer в Azure HDInsight | Документация Майкрософт
description: Установка и использование стороннего приложения Datameer на базе платформы Hadoop.
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: ashish
ms.openlocfilehash: 5008056ae2274d058706649f286b91b71feadc27
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31401315"
---
# <a name="install-published-application---datameer"></a>Установка опубликованного приложения Datameer

В этой статье описывается, как установить и запустить опубликованное приложение [Datameer](https://www.datameer.com/) на базе платформы Hadoop в Azure HDInsight. Обзор платформы приложений HDInsight и список доступных опубликованных приложений независимых поставщиков программного обеспечения приведены в статье [Установка сторонних приложений Hadoop в Azure HDInsight](hdinsight-apps-install-applications.md). Инструкции по установке собственного приложения см. в статье [Установка пользовательских приложений HDInsight](hdinsight-apps-install-custom-applications.md).

## <a name="about-datameer"></a>Сведения о приложении Datameer

Datameer — это собственное приложение на платформе Hadoop, расширяющее имеющиеся возможности Azure HDInsight и предоставляющее быструю интеграцию, подготовку и анализ структурированных и неструктурированных данных. Datameer может получить доступ к более чем 70 источникам и форматам (структурированным, частично структурированным и неструктурированным). Вы можете напрямую передать данные или извлекать их по требованию, используя уникальные ссылки на данные. Функциональность самообслуживания и привычный интерфейс электронной таблицы Datameer упрощают технологии больших данных и ускоряют формирование аналитических сведений. Интерфейс электронной таблицы предоставляет простой механизм для ввода декларативных формул, которые затем преобразуются в оптимизированные задания Hadoop. Благодаря Datameer, а также навыкам работы со средствами бизнес-аналитики и Excel вы можете быстро использовать Hadoop в облаке. Дополнительные сведения см. в [документации по Datameer](http://www.datameer.com/documentation/display/DAS50/Home?ls=Partners&lsd=Microsoft&c=Partners&cd=Microsoft).

## <a name="prerequisites"></a>предварительным требованиям

Для установки этого приложения на новый кластер HDInsight или на имеющийся кластер требуется следующая конфигурация:

* Ценовая категория кластера: "Стандартный".
* Тип кластера: Hadoop.
* Версия кластера: 3.4.

## <a name="install-the-datameer-published-application"></a>Установка опубликованного приложения Datameer

Пошаговые инструкции по установке этого и других доступных приложений независимых поставщиков программного обеспечения см. в статье [Установка сторонних приложений Hadoop в Azure HDInsight](hdinsight-apps-install-applications.md).

## <a name="launch-datameer"></a>Запуск Datameer

1. После установки вы можете запустить Datameer из своего кластера на портале Azure. Для этого перейдите на панель **Параметры**, а затем выберите **Приложения** в категории **Общие**. На панели "Установленные приложения" перечислены все установленные приложения.

    ![Установленное приложение Datameer](./media/hdinsight-apps-install-datameer/datameer-app.png)

2. При выборе Datameer отображается ссылка на веб-страницу и путь к конечной точке SSH. Щелкните ссылку на веб-страницу.

3. При первом запуске можно выбрать один из двух вариантов лицензирования: использовать 14-дневную пробную версию или активировать имеющуюся лицензию.

    ![Варианты лицензии](./media/hdinsight-apps-install-datameer/license.png)

4. После выбора варианта лицензии вам будет предоставлена форма входа в систему. Введите учетные данные по умолчанию, отображаемые до появления формы входа в систему. Войдя в систему, примите условия соглашения программного обеспечения, чтобы продолжить работу.

    ![Вход](./media/hdinsight-apps-install-datameer/login.png)

Выполнив следующие действия, вы увидите демонстрацию "Hello World".

1. [Скачайте пример CSV-файла](https://datameer.box.com/s/wzzw27za3agic4yjj8zrn6vfrph0ppnf).

2. Щелкните знак **+** вверху панели мониторинга Datameer и выберите **File Upload** (Передача файлов).

    ![Передача файла](./media/hdinsight-apps-install-datameer/upload.png)

3. В диалоговом окне передачи найдите и выберите загруженный файл **Hello World.csv**. Убедитесь, что для **типа файла** задано значение CSV или TSV. Щелкните **Далее**. Нажимайте кнопку **Next** (Далее) до завершения работы мастера.

    ![Передача файла](./media/hdinsight-apps-install-datameer/upload-browse.png)

4. Назовите файл **Hello World** в новой папке. Задайте для новой папки имя "Demo". Щелкните **Сохранить**.

    ![Сохранить](./media/hdinsight-apps-install-datameer/save.png)

5. Щелкните знак **+** еще раз и выберите **Workbook** (Книга), чтобы создать рабочую книгу для данных.

    ![Добавление книги](./media/hdinsight-apps-install-datameer/add-workbook.png)

6. Разверните папку **Data**, **FileUploads**, а затем папку **Demo**, созданную при сохранении файла "Hello World". Выберите **Hello World** из списка файлов, а затем щелкните **Add Data** (Добавить данные).

    ![Сохранить](./media/hdinsight-apps-install-datameer/select-file.png)

7. Отобразятся данные, загружаемые в интерфейс электронной таблицы. Чтобы выбрать подмножество данных, нажмите кнопку **Filter** (Фильтр) на панели инструментов.

    ![Кнопка фильтра](./media/hdinsight-apps-install-datameer/filter-button.png)

8. В диалоговом окне Apply Filter (Применить фильтр) выберите столбец **City** (Город), оператор **equals** (равно) и введите **Чикаго** в текстовом поле фильтра. Установите флажок **Create filter in new sheet** (Создать фильтр в новом листе), а затем выберите **Create Filter** (Создать фильтр).

    ![Применение фильтра](./media/hdinsight-apps-install-datameer/apply-filter.png)

9. Сохраните книгу, щелкнув **File** (Файл), а затем **Save** (Сохранить). Укажите имя, например "Книга Hello World".

10. Вам будут предоставлены варианты времени и способа запуска книги. Пока оставьте для всех параметров значения по умолчанию, а затем установите флажок **Start calculation process immediately after save** (Начать вычисление сразу после сохранения) и нажмите кнопку **Save** (Сохранить).

    ![Сохранение книги](./media/hdinsight-apps-install-datameer/save-workbook.png)

11. Datameer предоставляет многофункциональные средства визуализации. Чтобы просмотреть данные, создайте инфографику. Щелкните знак **+** вверху панели мониторинга, а затем выберите **Infographic** (Инфографика).

    ![Добавление инфографики](./media/hdinsight-apps-install-datameer/infographic-button.png)

12. Перетащите мини-приложение с линейчатой диаграммой в списке слева, как показано в шаге 1 на следующей диаграмме. Откройте папку Data в обозревателе данных справа, разверните книгу, а затем лист, добавленный вместе с фильтром (на шаге 2). Перетащите столбец **Name** (Имя) поверх линейчатой диаграммы и поместите его в целевое поле **Label** (Метка), чтобы задать столбец Workbook's Name (Имя книги) в качестве поля метки диаграммы (шаг 3).

    ![Инфографика](./media/hdinsight-apps-install-datameer/infographic.png)

13. Чтобы задать время существования в качестве оси Y диаграммы, перетащите столбец **Age** (Время существования) в поле **Data** (Данные) диаграммы.

    ![Инфографика](./media/hdinsight-apps-install-datameer/infographic-age.png)

Поздравляем! Вы создали визуализацию данных без написания кода. Теперь вы можете изучить различные варианты и дополнительные визуализации.

## <a name="next-steps"></a>Дополнительная информация

* [Документация по Datameer](http://www.datameer.com/documentation/display/DAS50/Home?ls=Partners&lsd=Microsoft&c=Partners&cd=Microsoft).
* [Установка пользовательских приложений Hadoop в Azure HDInsight](hdinsight-apps-install-custom-applications.md) — узнайте, как развернуть в HDInsight неопубликованное приложение HDInsight.
* [Публикация приложений HDInsight в Azure Marketplace](hdinsight-apps-publish-applications.md)— узнайте, как опубликовать пользовательские приложения HDInsight в Azure Marketplace.
* [Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx)(Установка приложения HDInsight) — узнайте, как определить приложения HDInsight.
* [Настройка кластеров HDInsight под управлением Linux с помощью действия сценария](hdinsight-hadoop-customize-cluster-linux.md) — узнайте, как использовать действие скрипта для установки дополнительных приложений.
* [Использование пустых граничных узлов в кластерах Hadoop в HDInsight](hdinsight-apps-use-edge-node.md) — узнайте, как использовать пустой граничный узел для доступа к кластерам HDInsight, а также тестирования и размещения приложений HDInsight.
