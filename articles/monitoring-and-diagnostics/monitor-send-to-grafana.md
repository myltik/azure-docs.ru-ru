---
title: Мониторинг служб и приложений Azure с использованием Grafana | Документация Майкрософт
description: Маршрутизация данных Azure Monitor и Application Insights для просмотра в Grafana.
services: monitoring-and-diagnostics
keywords: ''
author: rboucher
ms.author: robb
ms.date: 11/06/2017
ms.topic: article
ms.service: monitoring-and-diagnostics
ms.openlocfilehash: 537760554baa542d4cd967d2e1e885f936303175
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2018
---
# <a name="monitor-your-azure-services-in-grafana"></a>Мониторинг служб Azure в Grafana
Теперь также можно отслеживать службы Azure и приложения из [Grafana](https://grafana.com/) с помощью [подключаемого модуля источника данных Azure Monitor](https://grafana.com/plugins/grafana-azure-monitor-datasource). Подключаемый модуль собирает данные производительности приложения, которые собраны пакетом SDK Application Insights, а также данные инфраструктуры, предоставленные Azure Monitor. Затем эти данные можно отобразить на панели мониторинга Grafana.

Сейчас подключаемый модуль доступен в предварительной версии.

Выполните следующие действия, чтобы настроить сервер Grafana из Azure Marketplace и создать панели мониторинга для метрик Azure Monitor и Application Insights.

## <a name="set-up-a-grafana-instance"></a>Настройка экземпляра Grafana
1. Перейдите в Azure Marketplace и выберите Grafana от Grafana Labs.

2. Введите названия и подробные сведения. Создайте новую группу ресурсов. Запомните значения, которые вы выбираете для имени и пароля виртуальной машины, а также пароля администратора сервера Grafana.  

3. Выберите размер виртуальной машины и учетную запись хранения.

4. Настройте параметры конфигурации сети.

5. Просмотрите сводку и выберите **Создать** после принятия условий использования.

## <a name="log-in-to-grafana"></a>Вход в Grafana
1. После завершения развертывания выберите **Перейти к группе ресурсов**. Появится список только что созданных ресурсов.

    ![Объекты группы ресурсов Grafana](.\media\monitor-how-to-grafana\grafana1.png)

    При выборе группы безопасности сети (в этом случае *grafana-nsg*) можно увидеть, что порт 3000 используется для получения доступа к серверу Grafana.

2. Вернитесь в список ресурсов и выберите **Общедоступный IP-адрес**. На основе значений, найденных на этом экране, введите в браузере *http://<IP address>:3000* или *<DNSName>:3000*. Должна появиться страница входа на сервер Grafana, которая была только что создана.

    ![Экран входа в Grafana](.\media\monitor-how-to-grafana\grafana2.png)

3. Войдите с помощью имени пользователя *администратора* и пароля администратора сервера Grafana, созданных ранее.

## <a name="configure-data-source-plugin"></a>Настройка подключаемого модуля источника данных

После успешного входа вы увидите, что подключаемый модуль источника данных монитора Azure уже включен.

![Отображение подключаемого модуля Azure Monitor в Grafana](.\media\monitor-how-to-grafana\grafana3.png)

1. Выберите **Add data source** (Добавить источник данных), чтобы настроить Azure Monitor и Application Insights.

2. Присвойте имя для источника данных и выберите **Azure Monitor** в качестве источника данных из раскрывающегося списка.


## <a name="create-a-service-principal"></a>Создание субъекта-службы

Grafana использует субъект-службу Azure Active Directory для подключения к API Azure Monitor и сбора данных метрик. Необходимо создать субъект-службу для управления доступом к ресурсам Azure.

1. Инструкции по созданию субъекта-службы см. в [этой статье](../azure-resource-manager/resource-group-create-service-principal-portal.md). Скопируйте и сохраните идентификатор арендатора, идентификатор клиента и секрет клиента.

2. Дополнительные сведения о назначении роли читателя приложению Azure Active Directory см. в [этой статье](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#assign-application-to-role).     

3. При использовании Application Insights можно также включить API Application Insights и идентификатор приложения для сбора метрик на основе Application Insights. Дополнительные сведения см. в статье [Getting your API key and Application ID](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID) (Получение ключа API и идентификатора приложения).

4. После ввода этой информации выберите **Save** (Сохранить), чтобы Grafana проверил API. Должно появиться примерно такое сообщение:  

    ![Отображение подключаемого модуля Azure Monitor в Grafana](.\media\monitor-how-to-grafana\grafana4-1.png)

> [!NOTE]
> При настройке подключаемого модуля можно указать, для каких облаков Azure (общедоступное облако, Azure для государственных организаций США,Azure для Германии или Azure для Китая) нужно настроить этот подключаемый модуль.
>
>

## <a name="build-a-grafana-dashboard"></a>Создание панели мониторинга Grafana

1. Перейдите на домашнюю страницу и выберите **New Dashboard** (Новая панель мониторинга).

2. На новой панели мониторинга выберите **Graph**. Можно использовать другие варианты построения графиков, однако в этой статье в качестве примера используется *Graph*.

    ![Новая панель мониторинга Grafana](.\media\monitor-how-to-grafana\grafana5.png)

3. На панели мониторинга появится пустой график.

4. Щелкните заголовок области и выберите **Edit** (Изменить), чтобы ввести сведения о данных, которые нужно представить на этом графике.

5. Когда вы выберите все необходимые виртуальные машины, на панели мониторинга можно будет просматривать метрики.

Ниже приведена простая панель мониторинга с двумя диаграммами. Левая показывает процент использования ЦП на двух виртуальных машинах. Диаграмма справа показывает транзакции в учетной записи хранения Azure с разбивкой по типам API транзакций.

![Пример двух диаграмм Grafana](.\media\monitor-how-to-grafana\grafana6.png)


## <a name="optional-create-dashboard-playlists"></a>Создание списков воспроизведения панелей мониторинга (необязательно)

Одна из многих полезных возможностей Grafana — это список воспроизведения панелей мониторинга. Можно создать несколько панелей мониторинга и добавить их в список воспроизведения, настроив интервал показа каждой панели мониторинга. Выберите **Play** (Воспроизведение), чтобы просматривать панели мониторинга циклично. Их можно отобразить на большом мониторе, чтобы представить "доску состояния" группы.

![Пример списка воспроизведения Grafana](.\media\monitor-how-to-grafana\grafana7.png)


## <a name="optional-monitor-your-custom-metrics-in-the-same-grafana-server"></a>Мониторинг настраиваемых метрик на том же сервере Grafana (необязательно)

Можно также установить Telegraf и InfluxDB, чтобы собирать и представлять на диаграмме настраиваемые метрики и метрики на основе агента в том же экземпляре Grafana. Есть множество подключаемых модулей источников данных, с помощью которых можно объединить эти метрики на панели мониторинга.

Эту настройку можно повторно использовать, чтобы включить метрики с сервера Prometheus. Используйте подключаемый модуль источника данных Prometheus в коллекции подключаемых модулей Grafana.

Вот хорошие справочные статьи о том, как использовать Telegraf, InfluxDB, Prometheus и Docker:
 - [How To Monitor System Metrics with the TICK Stack on Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-monitor-system-metrics-with-the-tick-stack-on-ubuntu-16-04) (Мониторинг метрик системы с помощью TICK Stack в Ubuntu 16.04)

 - [Monitor Docker resource metrics with Grafana, InfluxDB, and Telegraf](https://blog.vpetkov.net/2016/08/04/monitor-docker-resource-metrics-with-grafana-influxdb-and-telegraf/) (Мониторинг метрик ресурса Docker с помощью Grafana, InfluxDB и Telegraf)

 - [A monitoring solution for Docker hosts, containers and containerized services](https://stefanprodan.com/2016/a-monitoring-solution-for-docker-hosts-containers-and-containerized-services/) (Решение мониторинга для служб контейнеров, контейнеров и узлов Docker)

Вот изображение полной панели мониторинга Grafana, на которой есть метрики из Azure Monitor и Application Insights.
![Пример метрик Grafana](.\media\monitor-how-to-grafana\grafana8.png)


## <a name="clean-up-resources"></a>Очистка ресурсов

Плата за запущенные виртуальные машины взимается вне зависимости от того, используются они или нет. Чтобы избежать дополнительных расходов, очистите группу ресурсов, созданную в этой статье.

1. В меню слева на портале Azure щелкните **Группы ресурсов**, а затем выберите **Grafana**.
2. На странице группы ресурсов щелкните **Удалить**, в текстовом поле введите **Grafana** и выберите **Удалить**.

## <a name="next-steps"></a>Дополнительная информация
* [Обзор метрик в Microsoft Azure](monitoring-overview-metrics.md)
