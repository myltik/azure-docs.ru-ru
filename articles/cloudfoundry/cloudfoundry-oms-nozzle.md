---
title: "Развертывание компонента Azure Log Analytics Nozzle для мониторинга Cloud Foundry | Документация Майкрософт"
description: "Пошаговые рекомендации по развертыванию компонента Nozzle системы Cloud Foundry Loggregator для Azure Log Analytics, настройке Azure Log Analytics и консоли OMS, а также по использованию этих инструментов для отслеживания метрик производительности и работоспособности системы Cloud Foundry."
services: virtual-machines-linux
documentationcenter: 
author: ningk
manager: timlt
editor: 
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/22/2017
ms.author: ningk
ms.translationtype: HT
ms.sourcegitcommit: 12c20264b14a477643a4bbc1469a8d1c0941c6e6
ms.openlocfilehash: f704a2638a4b6b57c014d502dd87303a55334672
ms.contentlocale: ru-ru
ms.lasthandoff: 09/07/2017

---

# <a name="deploy-azure-log-analytics-nozzle-for-cloud-foundry-system-monitoring"></a>Развертывание компонента Azure Log Analytics Nozzle для мониторинга системы Cloud Foundry

## <a name="background"></a>Фоновый

[Azure Log Analytics](https://azure.microsoft.com/services/log-analytics/) — это служба в Microsoft [Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/) (OMS). Она помогает собирать и анализировать данные, генерируемые в облачных и локальных средах.

Microsoft Azure Log Analytics Nozzle (Nozzle) — это компонент Cloud Foundry, пересылающий метрики из [Cloud Foundry Loggregator](https://docs.cloudfoundry.org/loggregator/architecture.html) Firehose в Azure Log Analytics. С помощью Nozzle можно собирать, просматривать и анализировать метрики производительности и работоспособности системы Cloud Foundry в нескольких развертываниях.

В этом документе описывается, как развернуть компонент Nozzle в среде Cloud Foundry, а затем обращаться к данным из консоли Azure Log Analytics OMS.

## <a name="prerequisites"></a>Предварительные требования

### <a name="1-deploy-a-cf-or-pcf-environment-in-azure"></a>1. Развертывание среды CF или PCF в Azure

Компонент Nozzle можно использовать для развертывания Cloud Foundry (CF) с открытым кодом или для развертывания Pivotal Cloud Foundry (PCF).

* [Развертывание Cloud Foundry в Azure](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md)

* [Развертывание Pivotal Cloud Foundry в Azure](https://docs.pivotal.io/pivotalcf/1-11/customizing/azure.html)

### <a name="2-install-the-cf-command-line-tools-for-deploying-the-nozzle"></a>2) Установка программ командной строки CF для развертывания Nozzle

Nozzle выполняется как приложение в среде CF, поэтому для развертывания этого приложения необходим интерфейс командной строки CF. Кроме того, требуется разрешение на доступ к Loggregator Firehose и Cloud Controller, а для создания и настройки пользователей нужен клиент командной строки UAA.

* [Установка интерфейса командной строки Cloud Foundry](https://docs.cloudfoundry.org/cf-cli/install-go-cli.html)

* [Установка клиента командной строки UAA для Cloud Foundry](https://github.com/cloudfoundry/cf-uaac/blob/master/README.md)

Перед настройкой клиента командной строки UAA убедитесь в том, что установлен компонент Rubygems.

### <a name="3-create-an-oms-workspace-in-azure"></a>3. Создание рабочей области OMS в Azure

#### <a name="create-the-oms-workspace-manually"></a>Создание рабочей области OMS вручную

Можно вручную создать рабочую область OMS и загрузить предварительно настроенные представления и оповещения OMS после развертывания Nozzle.

1. На портале Azure найдите в списке служб в Marketplace Log Analytics, введя "Log Analytics" в поле поиска.
2. Щелкните "Создать" и задайте следующие параметры.

* "Рабочая область OMS": введите имя рабочей области.
* "Подписка": если у вас несколько подписок, выберите подписку с развертыванием CF.
* "Группа ресурсов": можно создать новую группу ресурсов или использовать группу ресурсов с развертыванием CF.
* Расположение
* "Ценовая категория": нажмите кнопку "ОК" для завершения.
> Дополнительные сведения см. в статье [Начало работы с Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started).

#### <a name="create-the-oms-workspace-through-the-oms-template"></a>Создание рабочей области OMS с помощью шаблона OMS

Можно создать рабочую область OMS и загрузить предварительно настроенные представления и оповещения OMS, воспользовавшись [решением Azure Log Analytics OMS для Cloud Foundry](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-cloudfoundry-solution).

## <a name="deploy-the-nozzle"></a>Развертывание Nozzle

### <a name="deploy-the-nozzle-as-a-pcf-ops-manager-tile"></a>Развертывание Nozzle в качестве элемента PCF Operations Manager

Если вы развернули PCF с помощью Operations Manager, выполните следующие действия по [установке и настройке Nozzle для PCF](http://docs.pivotal.io/partners/azure-log-analytics-nozzle/installing.html), чтобы установить Nozzle в качестве элемента Operations Manager.

### <a name="deploy-the-nozzle-as-an-application-to-cloud-foundry"></a>Развертывание Nozzle в качестве приложения в Cloud Foundry

Если PCF Operations Manager не используется, необходимо отправить Nozzle как приложение.

#### <a name="log-in-to-your-cf-deployment-as-an-admin-through-cf-cli"></a>Вход в развертывание CF с правами администратора с помощью интерфейса командной строки CF

В среде разработки выполните следующую команду.
```
cf login -a https://api.${SYSTEM_DOMAIN} -u ${CF_USER} --skip-ssl-validation
```

> SYSTEM_DOMAIN — это доменное имя CF. Его можно получить, выполнив поиск "SYSTEM_DOMAIN" в файле манифеста развертывания CF. 
> CF_User — это имя администратора CF. Чтобы получить имя и пароль, можно в разделе scim файла манифеста развертывания CF выполнить поиск имени администратора и значения cf_admin_password.

#### <a name="create-a-cf-user-and-grant-required-privileges"></a>Создание пользователя CF и предоставление необходимых привилегий

В среде разработки выполните следующие команды:
```
uaac target https://uaa.${SYSTEM_DOMAIN} --skip-ssl-validation
uaac token client get admin
cf create-user ${FIREHOSE_USER} ${FIREHOSE_USER_PASSWORD}
uaac member add cloud_controller.admin ${FIREHOSE_USER}
uaac member add doppler.firehose ${FIREHOSE_USER}
```

> SYSTEM_DOMAIN — это доменное имя CF. Его можно получить, выполнив поиск "SYSTEM_DOMAIN" в файле манифеста развертывания CF.

#### <a name="download-the-latest-azure-log-analytics-nozzle-release"></a>Скачивание последнего выпуска Azure Log Analytics Nozzle

В среде разработки выполните следующую команду.
```
git clone https://github.com/Azure/oms-log-analytics-firehose-nozzle.git
cd oms-log-analytics-firehose-nozzle
```

#### <a name="set-environment-variables-in-manifestyml-in-your-current-directory"></a>Задание переменных среды в файле manifest.yml в текущем каталоге

Это манифест приложения для Nozzle, в нем необходимо заменить значения переменных своими данными рабочей области OMS.

```
OMS_WORKSPACE             : OMS workspace ID: open OMS portal from your OMS workspace, click "Settings", click "connected sources"
OMS_KEY                   : OMS key: open OMS portal from your OMS workspace, click "Settings", click "connected sources"
OMS_POST_TIMEOUT          : HTTP post timeout for sending events to OMS Log Analytics, default is 10s.
OMS_BATCH_TIME            : Interval for posting a batch to OMS Log Analytics, default is 10s.
OMS_MAX_MSG_NUM_PER_BATCH : The max number of messages in a batch to OMS Log Analytics, default is 1000.
API_ADDR                  : The api URL of the CF environment, refer to "Push the Nozzle as an App to Cloud Foundry" section step 1 on how to retrive your <CF_SYSTEM_DOMAIN>
DOPPLER_ADDR              : Loggregator's traffic controller URL, refer to "Deploy the Nozzle as an App to Cloud Foundry" section step 1 on how to retrive your <CF_SYSTEM_DOMAIN>
FIREHOSE_USER             : CF user you created in "Push the Nozzle as an App to Cloud Foundry" section, who has firehose and Cloud Controller admin access.
FIREHOSE_USER_PASSWORD    : Password of the CF user above.
EVENT_FILTER              : Event types to be filtered out. The format is a comma separated list, valid event types are METRIC,LOG,HTTP
SKIP_SSL_VALIDATION       : If true, allows insecure connections to the UAA and the Trafficcontroller
CF_ENVIRONMENT            : Enter any string value for identifying logs and metrics from different CF environments
IDLE_TIMEOUT              : Keep Alive duration for the firehose consumer, default is 60s.
LOG_LEVEL                 : Logging level of the nozzle, valid levels: DEBUG, INFO, ERROR
LOG_EVENT_COUNT           : If true, the total count of events that the nozzle has received and sent will be logged to OMS Log Analytics as CounterEvents
LOG_EVENT_COUNT_INTERVAL  : The time interval of logging event count to OMS Log Analytics, default is 60s.
```

### <a name="push-the-application-from-your-dev-box"></a>Отправка приложения из среды разработки

Убедитесь, что вы находитесь в папке oms-log-analytics-firehose-nozzle, и выполните следующую команду.
```
cf push
```

## <a name="validate-the-nozzle-installation"></a>Проверка установки Nozzle

### <a name="from-apps-manager-for-pcf"></a>В Apps Manager (для PCF)

1. Войдите в Operations Manager и убедитесь, что соответствующий элемент отображается на панели мониторинга установки.
2. Войдите в Apps Manager и убедитесь, что пространство, созданное для Nozzle, указано в отчете об использовании и находится в нормальном состоянии.

### <a name="from-dev-box"></a>В среде разработки

В среде разработки в окне интерфейса командной строки CF введите следующую команду.
```
cf apps
```
Убедитесь, что приложение Nozzle OMS выполняется.

## <a name="view-the-data-in-oms-portal"></a>Просмотр данных на портале OMS

### <a name="1-import-oms-view"></a>1. Импорт представления OMS

На портале OMS выберите **Конструктор представлений** -> **Импорт** -> **Обзор**, выберите один из OMSVIEW-файлов, например *Cloud Foundry.omsview*, и сохраните это представление. Теперь на главной странице обзора OMS отображается **элемент**. Щелкните этот **элемент**, он показывает визуализированные метрики.

Операторы могут настроить эти представления или создать новые представления в **конструкторе представлений**.

*Cloud Foundry.omsview* является предварительной версией шаблона представления OMS для Cloud Foundry. Полностью настроенный шаблон по умолчанию находится в разработке. Вы можете отправить свои предложения и отзывы в [разделе проблем](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues).

### <a name="2-create-alert-rules"></a>2) Создание правил генерации оповещений

В случае необходимости операторы могут [создавать оповещения](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts), настраивать запросы и пороговые значения. Ниже приведен набор рекомендуемых оповещений.

| Поисковый запрос                                                                  | Создать оповещение на основе | Описание                                                                       |
| ----------------------------------------------------------------------------- | ----------------------- | --------------------------------------------------------------------------------- |
| Type=CF_ValueMetric_CL Origin_s=bbs Name_s="Domain.cf-apps"                   | Число результатов < 1   | **bbs.Domain.cf-apps** указывает, актуальны ли данные домена cf-apps, то есть синхронизированы ли выполняемые запросы приложения CF из Cloud Controller с bbs.LRPsDesired (ИИ Diego-desired). Если данные не получены, это означает, что данные домена cf-apps не являются актуальными в заданный период времени. |
| Type=CF_ValueMetric_CL Origin_s=rep Name_s=UnhealthyCell Value_d>1            | Число результатов > 0   | Для ячеек Diego 0 означает работоспособное состояние, а 1 — неработоспособное. Установите оповещение, срабатывающее при обнаружении нескольких **неработоспособных ячеек Diego** в заданный период времени. |
| Type=CF_ValueMetric_CL Origin_s="bosh-hm-forwarder" Name_s="system.healthy" Value_d=0 | Число результатов > 0 | 1 означает, что система находится в работоспособном состоянии, а 0 — что она находится в неработоспособном состоянии. |
| Type=CF_ValueMetric_CL Origin_s=route_emitter Name_s=ConsulDownMode Value_d>0 | Число результатов > 0   | Consul периодически выдает состояние работоспособности. 0 означает, что система находится в работоспособном состоянии, а 1 означает, что передатчик маршрута обнаружил, что **Consul не работает**. |
| Type=CF_CounterEvent_CL Origin_s=DopplerServer (Name_s="TruncatingBuffer.DroppedMessages" or Name_s="doppler.shedEnvelopes") Delta_d>0 | Число результатов > 0 | Количество сообщений, намеренно **удаленных** Doppler из-за замедленной обратной реакции. |
| Type=CF_LogMessage_CL SourceType_s=LGR MessageType_s=ERR                      | Число результатов > 0   | Loggregator выдает **LGR**, сообщая о проблемах процесса ведения журнала, например, если вывод сообщений журнала слишком интенсивен. |
| Type=CF_ValueMetric_CL Name_s=slowConsumerAlert                               | Число результатов > 0   | Когда компонент Nozzle получает от Loggregator оповещение о низкой производительности потребителя, он отправляет оповещение **slowConsumerAlert** о ValueMetric в OMS. |
| Type=CF_CounterEvent_CL Job_s=nozzle Name_s=eventsLost Delta_d>0              | Число результатов > 0   | Если количество **потерянных событий** достигает порогового значения, это может указывать на проблемы в работе Nozzle. |

## <a name="scale"></a>Масштаб

### <a name="scale-the-nozzle"></a>Масштабирование Nozzle

Мы рекомендуем операторам начать как минимум с двух экземпляров Nozzle. Firehose распределяет рабочую нагрузку между всеми экземплярами Nozzle.
Чтобы убедиться, что компонент Nozzle справляется с трафиком данных из Firehose, оператору необходимо настроить оповещение **slowConsumerAlert**, указанное в разделе "Создание правил генерации оповещений". После получения оповещения следует выполнить [инструкции на случай низкой производительности Nozzle](https://docs.pivotal.io/pivotalcf/1-11/loggregator/log-ops-guide.html#slow-noz), чтобы определить, не требуется ли изменить масштаб.
Чтобы увеличить масштаб Nozzle, используйте Apps Manager или интерфейс командной строки CF для увеличения числа экземпляров или ресурсов памяти или диска для Nozzle.

### <a name="scale-the-loggregator"></a>Масштабирование Loggregator

Loggregator отправляет сообщение журнала **LGR**, чтобы сообщить о проблемах процесса ведения журнала. Оператор может отслеживать это оповещение, чтобы определить, нужно ли увеличить масштаб Loggregator.
Чтобы увеличить масштаб Loggregator, оператор может увеличить размер буфера Doppler или добавить дополнительные экземпляры сервера Doppler в манифест CF. Дополнительные сведения см. в [рекомендациях по масштабированию Loggregator](https://docs.cloudfoundry.org/running/managing-cf/logging-config.html#scaling).

## <a name="update"></a>Блокировка изменений

Чтобы установить более новую версию Nozzle, скачайте новый выпуск Nozzle, выполните указания в разделе "Развертывание" и отправьте приложение еще раз.

Чтобы удалить Nozzle, выполните следующие действия.

### <a name="from-the-ops-manager"></a>В Operations Manager

1. Войдите в Operations Manager.
2. Найдите элемент "Microsoft Azure Log Analytics Nozzle for PCF" ("Microsoft Azure Log Analytics Nozzle для PCF").
3. Щелкните значок корзины и подтвердите удаление.

### <a name="from-the-dev-box"></a>В среде разработки

В окне интерфейса командной строки CF введите следующую команду.
```
cf delete <App Name> -r
```

При удалении Nozzle данные на портале OMS не удаляются автоматически. Срок их действия истечет в соответствии с параметрами хранения Log Analytics OMS.

## <a name="support-and-feedback"></a>Поддержка и обратная связь

Azure Log Analytics Nozzle является компонентом с открытым кодом. Вы можете отправить вопросы и отзывы в [раздел GitHub](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues). Чтобы отправить запрос в службу поддержки Azure, используйте категорию службы "Виртуальная машина со службой Cloud Foundry". 

## <a name="next-step"></a>Дальнейшие действия

Помимо метрик Cloud Foundry, которые используются в Nozzle, можно использовать агент OMS для получения операционных данных уровня виртуальной машины (системный журнал, производительность, оповещения, данные инвентаризации). Он устанавливается на виртуальные машины CF в виде надстройки Bosh.
> Дополнительные сведения см. в разделе [Deploy OMS agent to your Cloud Foundry deployment](https://github.com/Azure/oms-agent-for-linux-boshrelease) (Развертывание агента OMS в развертывании Cloud Foundry).
