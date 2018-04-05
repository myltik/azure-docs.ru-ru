---
title: Развертывание компонента Azure Log Analytics Nozzle для мониторинга Cloud Foundry | Документация Майкрософт
description: Пошаговые рекомендации по развертыванию компонента Nozzle системы Cloud Foundry Loggregator для Azure Log Analytics. Используйте Nozzle для мониторинга метрик работоспособности и производительности системы Cloud Foundry.
services: virtual-machines-linux
documentationcenter: ''
author: ningk
manager: timlt
editor: ''
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/22/2017
ms.author: ningk
ms.openlocfilehash: b900a42196eedab89af8e55d71a336ed7adc45a4
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2018
---
# <a name="deploy-azure-log-analytics-nozzle-for-cloud-foundry-system-monitoring"></a>Развертывание компонента Azure Log Analytics Nozzle для мониторинга системы Cloud Foundry

[Azure Log Analytics](https://azure.microsoft.com/services/log-analytics/) — это служба в Azure. Она помогает собирать и анализировать данные, генерируемые в облачных и локальных средах.

Log Analytics Nozzle (Nozzle) — это компонент Cloud Foundry (CF), пересылающий метрики из [Cloud Foundry Loggregator](https://docs.cloudfoundry.org/loggregator/architecture.html) Firehose в Log Analytics. С помощью Nozzle можно собирать, просматривать и анализировать метрики производительности и работоспособности системы CF в нескольких развертываниях.

В этом документе объясняется, как развернуть компонент Nozzle в среде CF, а затем получить доступ к данным из консоли Log Analytics.

## <a name="prerequisites"></a>предварительным требованиям

Для развертывания Nozzle необходимо выполнить следующие шаги.

### <a name="1-deploy-a-cf-or-pivotal-cloud-foundry-environment-in-azure"></a>1. Развертывание среды CF или Pivotal Cloud Foundry в Azure

Компонент Nozzle можно использовать для развертывания CF с открытым кодом или для развертывания Pivotal Cloud Foundry (PCF).

* [Развертывание Cloud Foundry в Azure](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md)

* [Развертывание Pivotal Cloud Foundry в Azure](https://docs.pivotal.io/pivotalcf/1-11/customizing/azure.html)

### <a name="2-install-the-cf-command-line-tools-for-deploying-the-nozzle"></a>2. Установка программ командной строки CF для развертывания Nozzle

Nozzle запускается как приложение в среде CF. Для развертывания приложения необходим интерфейс командной строки CF.

В Nozzle также требуется разрешение на доступ к Loggregator Firehose и Cloud Controller. Для создания и настройки пользователя необходимо иметь службу учетной записи пользователя и проверки подлинности (UAA).

* [Установка интерфейса командной строки Cloud Foundry](https://docs.cloudfoundry.org/cf-cli/install-go-cli.html)

* [Установка клиента командной строки UAA для Cloud Foundry](https://github.com/cloudfoundry/cf-uaac/blob/master/README.md)

Перед настройкой клиента командной строки UAA убедитесь в том, что установлен компонент Rubygems.

### <a name="3-create-a-log-analytics-workspace-in-azure"></a>3. Создание рабочей области Log Analytics в Azure

Рабочую область Log Analytics можно создать вручную или с помощью шаблона. После завершения развертывания Nozzle загрузите предварительно настроенные представления и оповещения OMS.

Чтобы создать рабочую область OMS вручную, сделайте следующее:

1. На портале Azure найдите в списке служб в Azure Marketplace службу Log Analytics и выберите ее.
2. Выберите **Создать** и задайте следующие параметры:

   * **Рабочая область OMS**: введите имя рабочей области.
   * **Подписка**: если у вас несколько подписок, выберите ту же подписку, в которой содержится развертывание CF.
   * **Группа ресурсов**: можно создать группу ресурсов или использовать группу ресурсов с развертыванием CF.
   * **Расположение**: введите расположение.
   * **Ценовая категория**: нажмите кнопку **ОК** для завершения.

Дополнительные сведения см. в статье [Начало работы с Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started).

Кроме того, можно создать рабочую область Log Analytics с помощью шаблона OMS. При использовании этого метода шаблон автоматически загружает предварительно настроенные представления и оповещения OMS. Дополнительные сведения см. в разделе о [решении Azure Log Analytics для Cloud Foundry](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-cloudfoundry-solution).

## <a name="deploy-the-nozzle"></a>Развертывание Nozzle

Существует несколько различных способов развертывания Nozzle: как элемент PCF или как приложение CF.

### <a name="deploy-the-nozzle-as-a-pcf-ops-manager-tile"></a>Развертывание Nozzle в качестве элемента PCF Operations Manager

Если вы развернули PCF с помощью Operations Manager, выполните действия для [установки и настройки Nozzle для PCF](http://docs.pivotal.io/partners/azure-log-analytics-nozzle/installing.html). Nozzle будет установлен в качестве элемента Operations Manager.

### <a name="deploy-the-nozzle-as-a-cf-application"></a>Развертывание Nozzle в качестве приложения CF

Если PCF Operations Manager не используется, необходимо развернуть Nozzle как приложение. В следующих разделах описан этот процесс.

#### <a name="sign-in-to-your-cf-deployment-as-an-admin-through-cf-cli"></a>Вход в развертывание CF с правами администратора с помощью интерфейса командной строки CF

Выполните следующую команду:
```
cf login -a https://api.${SYSTEM_DOMAIN} -u ${CF_USER} --skip-ssl-validation
```

SYSTEM_DOMAIN — это доменное имя CF. Его можно получить, выполнив поиск "SYSTEM_DOMAIN" в файле манифеста развертывания CF. 

CF_User — это имя администратора CF. Чтобы получить имя и пароль, можно в разделе scim файла манифеста развертывания CF выполнить поиск имени администратора и значения cf_admin_password.

#### <a name="create-a-cf-user-and-grant-required-privileges"></a>Создание пользователя CF и предоставление необходимых привилегий

Выполните следующие команды:
```
uaac target https://uaa.${SYSTEM_DOMAIN} --skip-ssl-validation
uaac token client get admin
cf create-user ${FIREHOSE_USER} ${FIREHOSE_USER_PASSWORD}
uaac member add cloud_controller.admin ${FIREHOSE_USER}
uaac member add doppler.firehose ${FIREHOSE_USER}
```

SYSTEM_DOMAIN — это доменное имя CF. Его можно получить, выполнив поиск "SYSTEM_DOMAIN" в файле манифеста развертывания CF.

#### <a name="download-the-latest-log-analytics-nozzle-release"></a>Скачивание последнего выпуска Log Analytics Nozzle

Выполните следующую команду:
```
git clone https://github.com/Azure/oms-log-analytics-firehose-nozzle.git
cd oms-log-analytics-firehose-nozzle
```

#### <a name="set-environment-variables"></a>Настройка переменных среды

Теперь в текущем каталоге можно задать переменные среды в файле manifest.yml. Ниже показан манифест приложения для Nozzle. Замените значения своими данными рабочей области Log Analytics.

```
OMS_WORKSPACE             : Log Analytics workspace ID: open OMS portal from your Log Analytics workspace, select Settings, and select connected sources.
OMS_KEY                   : OMS key: open OMS portal from your Log Analytics workspace, select Settings, and select connected sources.
OMS_POST_TIMEOUT          : HTTP post timeout for sending events to Log Analytics. The default is 10 seconds.
OMS_BATCH_TIME            : Interval for posting a batch to Log Analytics. The default is 10 seconds.
OMS_MAX_MSG_NUM_PER_BATCH : The maximum number of messages in a batch to Log Analytics. The default is 1000.
API_ADDR                  : The API URL of the CF environment. For more information, see the preceding section, "Sign in to your CF deployment as an admin through CF CLI."
DOPPLER_ADDR              : Loggregator's traffic controller URL. For more information, see the preceding section, "Sign in to your CF deployment as an admin through CF CLI."
FIREHOSE_USER             : CF user you created in the preceding section, "Create a CF user and grant required privileges." This user has firehose and Cloud Controller admin access.
FIREHOSE_USER_PASSWORD    : Password of the CF user above.
EVENT_FILTER              : Event types to be filtered out. The format is a comma-separated list. Valid event types are METRIC, LOG, and HTTP.
SKIP_SSL_VALIDATION       : If true, allows insecure connections to the UAA and the traffic controller.
CF_ENVIRONMENT            : Enter any string value for identifying logs and metrics from different CF environments.
IDLE_TIMEOUT              : The Keep Alive duration for the firehose consumer. The default is 60 seconds.
LOG_LEVEL                 : The logging level of the Nozzle. Valid levels are DEBUG, INFO, and ERROR.
LOG_EVENT_COUNT           : If true, the total count of events that the Nozzle has received and sent are logged to Log Analytics as CounterEvents.
LOG_EVENT_COUNT_INTERVAL  : The time interval of the logging event count to Log Analytics. The default is 60 seconds.
```

### <a name="push-the-application-from-your-development-computer"></a>Отправка приложения из компьютера разработчика

Убедитесь, что вы находитесь в папке oms-log-analytics-firehose-nozzle. Выполните следующую команду:
```
cf push
```

## <a name="validate-the-nozzle-installation"></a>Проверка установки Nozzle

### <a name="from-apps-manager-for-pcf"></a>Из Apps Manager (для PCF)

1. Войдите в Operations Manager и убедитесь, что соответствующий элемент отображается на панели мониторинга установки.
2. Войдите в Apps Manager и убедитесь, что пространство, созданное для Nozzle, указано в отчете об использовании и находится в нормальном состоянии.

### <a name="from-your-development-computer"></a>Из компьютера разработчика

В окне интерфейса командной строки CF введите:
```
cf apps
```
Убедитесь, что приложение Nozzle OMS выполняется.

## <a name="view-the-data-in-the-oms-portal"></a>Просмотр данных на портале OMS

### <a name="1-import-the-oms-view"></a>1. Импорт представления OMS

На портале OMS выберите **Конструктор представлений** > **Импорт** > **Обзор**, а затем — один из файлов OMSVIEW, например *Cloud Foundry.omsview*, и сохраните представление. Теперь на главной странице **Обзор** отображается элемент. Выберите его, чтобы просмотреть визуализированные метрики.

Можно настроить эти представления или создать другие в **конструкторе представлений**.

Файл *Cloud Foundry.omsview* является предварительной версией шаблона представления OMS для Cloud Foundry. Это полностью настроенный шаблон по умолчанию. Вы можете отправить свои предложения и отзывы в [разделе проблем](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues).

### <a name="2-create-alert-rules"></a>2. Создание правил генерации оповещений

В случае необходимости можно [создавать оповещения](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts), настраивать запросы и пороговые значения. Ниже приведены рекомендуемые оповещения.

| Поисковый запрос                                                                  | Создать оповещение на основе | ОПИСАНИЕ                                                                       |
| ----------------------------------------------------------------------------- | ----------------------- | --------------------------------------------------------------------------------- |
| Type=CF_ValueMetric_CL Origin_s=bbs Name_s="Domain.cf-apps"                   | Число результатов < 1   | **bbs.Domain.cf-apps** указывает, актуальны ли данные домена cf-apps, то есть синхронизированы ли выполняемые запросы приложения CF из Cloud Controller с bbs.LRPsDesired (ИИ Diego-desired). Если данные не получены, это означает, что данные домена cf-apps не являются актуальными в указанный период времени. |
| Type=CF_ValueMetric_CL Origin_s=rep Name_s=UnhealthyCell Value_d>1            | Число результатов > 0   | Для ячеек Diego 0 означает работоспособное состояние, а 1 — неработоспособное. Установите оповещение, срабатывающее при обнаружении нескольких неработоспособных ячеек Diego в указанный период времени. |
| Type=CF_ValueMetric_CL Origin_s="bosh-hm-forwarder" Name_s="system.healthy" Value_d=0 | Число результатов > 0 | 1 означает, что система находится в работоспособном состоянии, а 0 — что она находится в неработоспособном состоянии. |
| Type=CF_ValueMetric_CL Origin_s=route_emitter Name_s=ConsulDownMode Value_d>0 | Число результатов > 0   | Consul периодически выдает состояние работоспособности. 0 означает, что система находится в работоспособном состоянии, а 1 означает, что передатчик маршрута обнаружил, что Consul не работает. |
| Type=CF_CounterEvent_CL Origin_s=DopplerServer (Name_s="TruncatingBuffer.DroppedMessages" or Name_s="doppler.shedEnvelopes") Delta_d>0 | Число результатов > 0 | Количество намеренно удаленных сообщений в Doppler из-за замедленной обратной реакции. |
| Type=CF_LogMessage_CL SourceType_s=LGR MessageType_s=ERR                      | Число результатов > 0   | Loggregator генерирует сообщение **LGR**, чтобы сообщить о проблемах процесса ведения журнала, например, если вывод сообщений журнала слишком интенсивен. |
| Type=CF_ValueMetric_CL Name_s=slowConsumerAlert                               | Число результатов > 0   | Когда компонент Nozzle получает от Loggregator оповещение о низкой производительности потребителя, он отправляет оповещение **slowConsumerAlert** о ValueMetric в Log Analytics. |
| Type=CF_CounterEvent_CL Job_s=nozzle Name_s=eventsLost Delta_d>0              | Число результатов > 0   | Если количество потерянных событий достигает порогового значения, это может указывать на проблемы в работе Nozzle. |

## <a name="scale"></a>Масштаб

Nozzle и Loggregator можно масштабировать.

### <a name="scale-the-nozzle"></a>Масштабирование Nozzle

Нужно начать как минимум с двух экземпляров Nozzle. Firehose распределяет рабочую нагрузку между всеми экземплярами Nozzle.
Чтобы убедиться, что Nozzle справляется с трафиком данных из Firehose, настройте оповещение **slowConsumerAlert** (указанное в предыдущем разделе "Создание правил генерации оповещений"). После получения оповещения следует выполнить [инструкции на случай низкой производительности Nozzle](https://docs.pivotal.io/pivotalcf/1-11/loggregator/log-ops-guide.html#slow-noz), чтобы определить, не требуется ли изменить масштаб.
Чтобы увеличить масштаб Nozzle, используйте Apps Manager или интерфейс командной строки CF для увеличения числа экземпляров либо ресурсов памяти или диска для Nozzle.

### <a name="scale-the-loggregator"></a>Масштабирование Loggregator

Loggregator отправляет сообщение журнала **LGR**, чтобы сообщить о проблемах процесса ведения журнала. Вы можете отслеживать это оповещение, чтобы определить, нужно ли увеличить масштаб Loggregator.
Чтобы увеличить масштаб Loggregator, увеличьте размер буфера Doppler или добавьте дополнительные сущности сервера Doppler в манифест CF. Дополнительные сведения см. в статье [Configuring System Logging](https://docs.cloudfoundry.org/running/managing-cf/logging-config.html#scaling) (Настройка ведения системного журнала).

## <a name="update"></a>Блокировка изменений

Чтобы установить более новую версию Nozzle, скачайте новый выпуск Nozzle, выполните указания в разделе "Развертывание Nozzle" и отправьте приложение еще раз.

### <a name="remove-the-nozzle-from-ops-manager"></a>Удаление Nozzle из Operations Manager

1. Войдите в Operations Manager.
2. Найдите элемент **Microsoft Azure Log Analytics Nozzle for PCF** (Microsoft Azure Log Analytics Nozzle для PCF).
3. Выберите значок корзины и подтвердите удаление.

### <a name="remove-the-nozzle-from-your-development-computer"></a>Удаление Nozzle из компьютера разработчика

В окне интерфейса командной строки CF введите:
```
cf delete <App Name> -r
```

При удалении Nozzle данные на портале OMS не удаляются автоматически. Срок их хранения зависит от настройки периода удержания Log Analytics.

## <a name="support-and-feedback"></a>Поддержка и обратная связь

Azure Log Analytics Nozzle является компонентом с открытым кодом. Отправляйте свои вопросы и отзывы в [разделе GitHub](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues). Чтобы отправить запрос в службу поддержки Azure, выберите категорию службы "Виртуальная машина со службой Cloud Foundry". 

## <a name="next-step"></a>Дальнейшие действия

Помимо метрик Cloud Foundry, которые используются в Nozzle, можно использовать агент OMS для получения операционных данных уровня виртуальной машины (системный журнал, производительность, оповещения, данные инвентаризации). Он устанавливается на виртуальные машины CF в виде надстройки Bosh.

Дополнительные сведения см. в разделе [Deploy OMS agent to your Cloud Foundry deployment](https://github.com/Azure/oms-agent-for-linux-boshrelease) (Развертывание агента OMS в развертывании Cloud Foundry).
