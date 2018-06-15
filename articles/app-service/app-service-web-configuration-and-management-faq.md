---
title: Часто задаваемые вопросы о настройке веб-приложений Azure | Документация Майкрософт
description: Ознакомьтесь с ответами на часто задаваемые вопросы о проблемах настройки и управления для функции "Веб-приложения" службы приложений Azure.
services: app-service\web
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: e773435d89a511e6f1f369cc3fd5dc6f06507e45
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/06/2018
ms.locfileid: "34808763"
---
# <a name="configuration-and-management-faqs-for-web-apps-in-azure"></a>Часто задаваемые вопросы о настройке и управлении для функции "Веб-приложения" в Azure

В этой статье содержатся ответы на часто задаваемые вопросы о проблемах настройки и управления для [функции "Веб-приложения" службы приложений Azure](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="are-there-limitations-i-should-be-aware-of-if-i-want-to-move-app-service-resources"></a>Есть ли ограничения для перемещения ресурсов службы приложений?

Существует несколько ограничений для перемещения ресурсов службы приложений в новую группу ресурсов или подписку. Дополнительные сведения см. в разделе [Ограничения службы приложений](../azure-resource-manager/resource-group-move-resources.md#app-service-limitations).

## <a name="how-do-i-use-a-custom-domain-name-for-my-web-app"></a>Как использовать имя пользовательского домена для веб-приложения?

Ответы на распространенные вопросы об использовании имени пользовательского домена с веб-приложением Azure см. в нашем видео (7 минут) о [добавлении имени пользовательского домена](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name). Видео содержит пошаговое руководство по добавлению имени пользовательского домена. В нем описано использование собственного URL-адреса вместо URL-адреса *.azurewebsites.net для веб-приложения службы приложений. Кроме того, ознакомьтесь с подробным пошаговым руководством [Сопоставление существующего настраиваемого DNS-имени с веб-приложениями Azure](app-service-web-tutorial-custom-domain.md).


## <a name="how-do-i-purchase-a-new-custom-domain-for-my-web-app"></a>Как приобрести новый пользовательский домен для веб-приложения?

Сведения об этом см. в статье [Приобретение и настройка имени личного домена для службы приложений Azure](custom-dns-web-site-buydomains-web-app.md).


## <a name="how-do-i-upload-and-configure-an-existing-ssl-certificate-for-my-web-app"></a>Как загрузить и настроить существующий сертификат SSL для веб-приложения?

Сведения об этом см. в разделе [Привязка SSL-сертификата](app-service-web-tutorial-custom-ssl.md#upload).


## <a name="how-do-i-purchase-and-configure-a-new-ssl-certificate-in-azure-for-my-web-app"></a>Как приобрести и настроить новый сертификат SSL в Azure для веб-приложения?

Сведения об этом см. в статье [Приобретение и настройка сертификата SSL для службы приложений Azure](web-sites-purchase-ssl-web-site.md).


## <a name="how-do-i-move-application-insights-resources"></a>Как перемещать ресурсы Application Insights?

В настоящее время Azure Application Insights не поддерживает операции перемещения. Если исходная группа ресурсов содержит ресурс Application Insights, этот ресурс переместить нельзя. Если при попытке переместить приложение службы приложений включить ресурс Application Insights, вся операция заканчивается ошибкой. Тем не менее для корректной работы приложения ресурс Application Insights и план службы приложений не должны обязательно находиться в одной группе ресурсов.

Дополнительные сведения см. в разделе [Ограничения службы приложений](../azure-resource-manager/resource-group-move-resources.md#app-service-limitations).

## <a name="where-can-i-find-a-guidance-checklist-and-learn-more-about-resource-move-operations"></a>Где найти контрольный список с инструкциями и дополнительные сведения о перемещении ресурсов?

В разделе [Ограничения службы приложений](../azure-resource-manager/resource-group-move-resources.md#app-service-limitations) объясняется, как переместить ресурсы в новую подписку или в новую группу ресурсов в той же подписке. Также здесь содержатся сведения о контрольном списке для перемещения ресурсов, службах, поддерживающих операции перемещения, ограничениях службы приложений и другая информация.

## <a name="how-do-i-set-the-server-time-zone-for-my-web-app"></a>Как задать часовой пояс сервера для веб-приложения?

Чтобы задать часовой пояс сервера для веб-приложения:

1. На портале Azure в подписке службы приложения перейдите к меню **Параметры приложения** .
2. В разделе **Параметры приложения** добавьте параметр:
    * Ключ = WEBSITE_TIME_ZONE
    * Значение = *требуемый часовой пояс*
3. Щелкните **Сохранить**.

## <a name="why-do-my-continuous-webjobs-sometimes-fail"></a>Почему при выполнении непрерывных веб-заданий иногда происходят сбои?

По умолчанию в случае простоя в течение заданного периода времени веб-приложения выгружаются. Это позволяет сэкономить системные ресурсы. При использовании планов "Базовый" и "Стандартный" вы можете активировать параметр **Всегда включено**, чтобы приложение постоянно загружалось. Если веб-приложение выполняет непрерывные веб-задания, активируйте функцию **Всегда включено**, иначе веб-задания не будут выполняться правильно. Дополнительные сведения см. в разделе [Создание непрерывно выполняющегося веб-задания](web-sites-create-web-jobs.md#CreateContinuous).

## <a name="how-do-i-get-the-outbound-ip-address-for-my-web-app"></a>Как получить исходящий IP-адрес для веб-приложения?

Чтобы просмотреть список исходящих IP-адресов для веб-приложения:

1. На портале Azure в колонке своего веб-приложения перейдите к меню **Свойства**.
2. Введите в поле поиска **исходящий IP-адрес**.

Отобразится список исходящих IP-адресов.

Чтобы узнать, как получить исходящий IP-адрес, если ваш веб-сайт размещен в среде службы приложений, изучите раздел [Исходящие сетевые адреса](environment/app-service-app-service-environment-network-architecture-overview.md#outbound-network-addresses).

## <a name="how-do-i-get-a-reserved-or-dedicated-inbound-ip-address-for-my-web-app"></a>Как получить зарезервированный или выделенный входящий IP-адрес для веб-приложения?

Настройте выделенный или зарезервированный IP-адрес для входящих вызовов, поступающих на веб-сайт приложения Azure, установите и настройте сертификат SSL на основе IP-адресов.

Чтобы использовать выделенный или зарезервированный IP-адрес для входящих вызовов, требуется план службы приложений категории "Базовый" или выше.

## <a name="can-i-export-my-app-service-certificate-to-use-outside-azure-such-as-for-a-website-hosted-elsewhere"></a>Можно ли экспортировать сертификат службы приложений для использования за пределами Azure, например для веб-сайтов в других расположениях? 

Сертификаты службы приложений считаются ресурсами Azure. Они не предназначены для использования за пределами служб Azure. Для использования вне Azure их экспортировать нельзя. Дополнительные сведения см. в статье [FAQs for App Service certificates and custom domains](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview) (Вопросы и ответы по сертификатам службы приложений и личным доменам).

## <a name="can-i-export-my-app-service-certificate-to-use-with-other-azure-cloud-services"></a>Можно ли экспортировать сертификат службы приложений для использования с другими облачными службами Azure?

Портал предоставляет эффективные возможности развертывания сертификата в приложениях службы приложений при помощи Azure Key Vault. Но мы получаем запросы от клиентов на использование этих сертификатов вне платформы службы приложений, например, с виртуальными машинами Azure. Чтобы узнать, как создать локальную копию PFX сертификата службы приложений, которая позволит использовать сертификат с другими ресурсами Azure, см. статью [Create a local PFX copy of an App Service certificate](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/) (Создание локальной копии PFX сертификата службы приложений).

Дополнительные сведения см. в статье [FAQs for App Service certificates and custom domains](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview) (Вопросы и ответы по сертификатам службы приложений и личным доменам).


## <a name="why-do-i-see-the-message-partially-succeeded-when-i-try-to-back-up-my-web-app"></a>Почему при попытке выполнить резервное копирование веб-приложения отображается сообщение "Выполнено частично"?

Часто резервное копирование может завершаться сбоем из-за того, что приложение использует некоторые файлы. Используемые файлы блокируются во время выполнения резервного копирования. Это препятствуют их резервному копированию и может привести к появлению сообщения "Выполнено частично". Можно предотвратить это, исключив файлы из процесса резервного копирования, и создать резервную копию только необходимых данных. Дополнительные сведения см. в статье [Backup just the important parts of your site with Azure web apps](http://www.zainrizvi.io/2015/06/05/creating-partial-backups-of-your-site-with-azure-web-apps/) (Резервное копирование только важных частей сайта с помощью функции "Веб-приложения Azure").

## <a name="how-do-i-remove-a-header-from-the-http-response"></a>Как удалить заголовок ответа HTTP?

Чтобы удалить заголовки из HTTP-ответа, обновите файл сайта web.config. Дополнительные сведения см. в статье [Remove standard server headers on your Azure websites](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/) (Удаление стандартных заголовков сервера на веб-сайтах Azure).

## <a name="is-app-service-compliant-with-pci-standard-30-and-31"></a>Соответствует ли служба приложений стандарту PCI версий 3.0 и 3.1?

В настоящее время функция "Веб-приложения" службы приложений Azure соответствует стандарту безопасности данных (DSS) PCI версии 3.0, уровень 1. Поддержка PCI DSS версии 3.1 запланирована в нашей стратегии. Способ внедрения последнего стандарта уже в процессе разработки.

Для сертификации PCI DSS версии 3.1 требуется отключить протокол TLS 1.0. В настоящее время для большинства планов службы приложений отключение TLS 1.0 выполнить нельзя. Но если вы используете среду службы приложений или планируете перенести в нее рабочую нагрузку, возможности для управления средой расширяются. Например, можно отключить TLS 1.0, обратившись в службу поддержки Azure. В ближайшем будущем мы планируем сделать эту возможность доступной для пользователей.

Дополнительные сведения см. в статье [Microsoft Azure App Service web app compliance with PCI Standard 3.0 and 3.1](https://support.microsoft.com/help/3124528) (Соответствие веб-приложений службы приложений Microsoft Azure стандарту PCI версии 3.0 и 3.1).

## <a name="how-do-i-use-the-staging-environment-and-deployment-slots"></a>Как использовать промежуточную среду и слоты развертывания?

Планы службы приложений "Стандартный" и "Премиум" позволяют развертывать веб-приложения в службе приложений в отдельный слот развертывания вместо рабочего слота по умолчанию. Слоты развертывания являются динамическими веб-приложениями с собственными именами узлов. Содержимое веб-приложений и элементы конфигурации можно переключать между двумя слотами развертывания (включая рабочий).

Дополнительные сведения об использовании слотов развертывания см. в статье [Настройка промежуточных сред в службе приложений Azure](web-sites-staged-publishing.md).

## <a name="how-do-i-access-and-review-webjob-logs"></a>Как получить доступ к веб-заданиям и просмотреть их журналы?

Просмотр журналов веб-задания

1. Перейдите на [веб-сайт Kudu](https://*yourwebsitename*.scm.azurewebsites.net).
2. Выберите веб-задание.
3. Нажмите кнопку **Переключить выходные данные**.
4. Чтобы загрузить выходной файл, выберите ссылку **загрузки**.
5. Для отдельного запуска выберите **Отдельный вызов**.
6. Нажмите кнопку **Переключить выходные данные**.
7. Выберите ссылку загрузки.

## <a name="im-trying-to-use-hybrid-connections-with-sql-server-why-do-i-see-the-message-systemoverflowexception-arithmetic-operation-resulted-in-an-overflow"></a>Я пытаюсь использовать гибридные подключения к SQL Server. Почему отображается сообщение "System.OverflowException: переполнение в результате арифметической операции"?

При использовании гибридных подключений для доступа к SQL Server обновление Microsoft .NET от 10 мая 2016 г. может привести к сбою подключения. Может появиться это сообщение:

```
Exception: System.Data.Entity.Core.EntityException: The underlying provider failed on Open. —> System.OverflowException: Arithmetic operation resulted in an overflow. or (64 bit Web app) System.OverflowException: Array dimensions exceeded supported range, at System.Data.SqlClient.TdsParser.ConsumePreLoginHandshake
```

### <a name="resolution"></a>Способы устранения:

Исключение было порождено из-за проблемы с диспетчером гибридных подключений, которая затем была исправлена. Не забудьте [обновить диспетчер гибридных подключений](https://go.microsoft.com/fwlink/?LinkID=841308), чтобы устранить эту проблему.

## <a name="how-do-i-add-or-edit-a-url-rewrite-rule"></a>Как добавить или изменить правило переопределения URL-адреса?

Добавление или изменение правила переопределения URL-адреса

1. Настройте диспетчер IIS для подключения к веб-приложению службы приложений. Сведения о подключении диспетчера IIS к службе приложения см. в записи блога [Remote administration of Azure websites by using IIS Manager](https://azure.microsoft.com/blog/remote-administration-of-windows-azure-websites-using-iis-manager/) (Удаленное администрирование веб-сайтов Azure с помощью диспетчера IIS).
2. В диспетчере IIS добавьте или измените правило переопределения URL-адреса. Чтобы узнать, как добавить или изменить правило переопределения URLL-адреса, см. статью [Create rewrite rules for the URL rewrite module](https://www.iis.net/learn/extensions/url-rewrite-module/creating-rewrite-rules-for-the-url-rewrite-module) (Создание правил для модуля переопределения URL-адреса).

## <a name="how-do-i-control-inbound-traffic-to-app-service"></a>Как контролировать входящий трафик в службе приложений?

На уровне сайта есть два варианта управления входящим трафиком в службе приложений.

* Включите динамическое ограничение IP-адресов. Дополнительные сведения о включении динамического ограничения IP-адресов см. в записи блога [IP and domain restrictions for Azure websites](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/) (Ограничения IP-адресов и доменов для веб-сайтов Azure).
* Включите Module Security. Дополнительные сведения о включении Module Security см. в записи блога [ModSecurity web application firewall on Azure websites](https://azure.microsoft.com/blog/modsecurity-for-azure-websites/) (Брандмауэр ModSecurity для веб-приложений на веб-сайтах Azure).

В среде службы приложений можно использовать [брандмауэр Barracuda](https://azure.microsoft.com/blog/configuring-barracuda-web-application-firewall-for-azure-app-service-environment/).

## <a name="how-do-i-block-ports-in-an-app-service-web-app"></a>Как блокировать порты в веб-приложении службы приложений?

В среде службы приложений с общим клиентом нельзя блокировать определенные порты из-за особенностей инфраструктуры. TCP-порты 4016, 4018 и 4020 также могут быть открыты для удаленной отладки Visual Studio.

В среде службы приложений можно полностью контролировать входящий и исходящий трафик. Для ограничения трафика в определенных портах или их блокировки можно использовать группы безопасности сети. Дополнительные сведения о среде службы приложений см. в записи блога [Introducing App Service Environment](https://azure.microsoft.com/blog/introducing-app-service-environment/) (Введение в среду службы приложения).

## <a name="how-do-i-capture-an-f12-trace"></a>Как отслеживать трассировку F12?

Трассировку F12 можно отслеживать двумя способами.

* HTTP-трассировка F12
* Вывод F12 на консоль

### <a name="f12-http-trace"></a>HTTP-трассировка F12

1. В браузере Internet Explorer откройте свой веб-сайт. Очень важно войти в систему, прежде чем выполнять следующие шаги. Иначе трассировка F12 захватывает конфиденциальные данные для входа.
2. Нажмите клавишу F12.
3. Откройте вкладку **Сеть** и нажмите зеленую кнопку **Воспроизвести**.
4. Выполните действия для воспроизведения проблемы.
5. Нажмите красную кнопку **Остановить**.
6. Нажмите кнопку **Сохранить** (значок диска) и сохраните HAR-файл (в Internet Explorer и Edge) *или* щелкните правой кнопкой мыши HAR-файл и в браузере Chrome выберите **Save as HAR with content** (Сохранить как HAR-файл с содержимым).

### <a name="f12-console-output"></a>Вывод F12 на консоль

1. Выберите вкладку **Консоль**.
2. Выберите все вкладки с элементами больше нуля (**Ошибка**, **Предупреждение** или **Информация**). Если вкладка не выбрана, при перемещении указателя мыши за пределы значка вкладки он станет черным или серым.
3. Щелкните правой кнопкой мыши в области сообщений на панели и выберите **Копировать все**.
4. Вставьте скопированный текст в файл и сохраните файл.

Чтобы просмотреть HAR-файл, можно использовать [средство просмотра HAR](http://www.softwareishard.com/har/viewer/).

## <a name="why-do-i-get-an-error-when-i-try-to-connect-an-app-service-web-app-to-a-virtual-network-that-is-connected-to-expressroute"></a>Почему при попытке подключить веб-приложение службы приложений к виртуальной сети с ExpressRoute отображается сообщение об ошибке?

При попытке подключить веб-приложение Azure к виртуальной сети с Azure ExpressRoute происходит сбой. Появляется сообщение "Шлюз не является VPN-шлюзом".

В настоящее время нельзя установить VPN-подключения типа "точка — сеть" к виртуальной сети, подключенной к ExpressRoute. VPN типа "точка — сеть" и ExpressRoute не могут действовать в одной и той же виртуальной сети. Дополнительные сведения см. в разделе о [квотах и ограничениях для ExpressRoute и VPN-подключений типа "сеть — сеть"](../expressroute/expressroute-howto-coexist-classic.md#limits-and-limitations).

## <a name="how-do-i-connect-an-app-service-web-app-to-a-virtual-network-that-has-a-static-routing-policy-based-gateway"></a>Как подключить веб-приложение службы приложений к виртуальной сети со шлюзом статической маршрутизации (на основе политики)?

В настоящее время нельзя подключить веб-приложение службы приложений к виртуальной сети со шлюзом статической маршрутизации (на основе политики). Если необходимая виртуальная сеть уже существует, перед подключением приложения в ней необходимо активировать VPN-подключение типа "точка — сеть", использующее шлюз с динамической маршрутизацией. Если шлюз настроен для статической маршрутизации, активировать VPN-подключение "точка-сеть" нельзя. 

Дополнительные сведения см. в разделе [Приступая к работе](web-sites-integrate-with-vnet.md#getting-started).

## <a name="in-my-app-service-environment-why-can-i-create-only-one-app-service-plan-even-though-i-have-two-workers-available"></a>Почему в среде службы приложений можно создать только один план службы приложений, даже если доступны две рабочие роли?

Чтобы обеспечить отказоустойчивость в среде службы приложений, для каждого рабочего пула требуется как минимум один дополнительный вычислительный ресурс. Нельзя присвоить рабочей нагрузке дополнительный вычислительный ресурс.

Дополнительные сведения см. в статье [Создание среды службы приложения](environment/app-service-web-how-to-create-an-app-service-environment.md).

## <a name="why-do-i-see-timeouts-when-i-try-to-create-an-app-service-environment"></a>Почему при попытке создать среду службы приложений отображаются сообщения об истечении времени ожидания?

Иногда при создании среды службы приложений происходят сбои. В таких случаях в журналах действий отображается следующее сообщение об ошибке:
```
ResourceID: /subscriptions/{SubscriptionID}/resourceGroups/Default-Networking/providers/Microsoft.Web/hostingEnvironments/{ASEname}
Error:{"error":{"code":"ResourceDeploymentFailure","message":"The resource provision operation did not complete within the allowed timeout period.”}}
```

Чтобы устранить эту проблему, убедитесь в отсутствии какого-либо из условий ниже.
* Подсеть слишком маленькая.
* Подсеть пуста.
* ExpressRoute не поддерживает требования среды службы приложений к сетевому подключению.
* Группа безопасности сети не поддерживает требования среды службы приложений к сетевому подключению.
* Включено принудительное туннелирование.

Дополнительные сведения см. в записи блога [Most frequent issues when deploying (creating) a new Azure App Service Environment](https://blogs.msdn.microsoft.com/waws/2016/05/13/most-frequent-issues-when-deploying-creating-a-new-azure-app-service-environment-ase/) (Частые проблемы при развертывании (создании) новой среды службы приложений Azure).

## <a name="why-cant-i-delete-my-app-service-plan"></a>Почему не удается удалить план службы приложений?

Нельзя удалить план службы приложений, если с ним связаны какие-либо приложения службы приложений. Прежде чем удалить план служб приложений, удалите из него все связанные приложения службы приложений.

## <a name="how-do-i-schedule-a-webjob"></a>Как запланировать веб-задание?

Создать запланированное веб-задание можно при помощи выражений Cron:

1. Создайте файл settings.job.
2. Включите в этом файле JSON свойство расписания при помощи выражения Cron: 
    ```
    { "schedule": "{second}
    {minute} {hour} {day}
    {month} {day of the week}" }
    ```

Дополнительные сведения о запланированных веб-заданиях см. в разделе [Создание запланированного веб-задания с использованием выражения CRON](web-sites-create-web-jobs.md#CreateScheduledCRON).

## <a name="how-do-i-perform-penetration-testing-for-my-app-service-app"></a>Как выполнить тест безопасности для приложения службы приложений?

Чтобы выполнить тест безопасности, [отправьте запрос](https://portal.msrc.microsoft.com/en-us/engage/pentest).

## <a name="how-do-i-configure-a-custom-domain-name-for-an-app-service-web-app-that-uses-traffic-manager"></a>Как настроить имя пользовательского домена для веб-приложения службы приложений, использующего диспетчер трафика?

Чтобы узнать, как использовать имя пользовательского домена для веб-приложения службы приложений с диспетчером трафика Azure для балансировки нагрузки, см. статью [Настройка личного доменного имени для веб-приложения в службе приложений Azure, использующей диспетчер трафика](web-sites-traffic-manager-custom-domain-name.md).

## <a name="my-app-service-certificate-is-flagged-for-fraud-how-do-i-resolve-this"></a>Мой сертификат службы приложений помечен как мошеннический. Как решить эту проблему?

При проверке домена после покупки сертификата службы приложений может появиться следующее сообщение:

"Сертификат помечен как возможно мошеннический. Запрос находится на рассмотрении. Если сертификат не станет доступным для использования в течение 24 часов, обратитесь в службу поддержки Azure"

Как указано в сообщении, проверка на мошенничество может занять до 24 часов. Сообщение будет отображаться в течение этого периода.

Если для сертификата службы приложений через 24 часа по-прежнему отображается это сообщение, запустите сценарий PowerShell ниже. Сценарий напрямую связывается с [поставщиком сертификата](https://www.godaddy.com/) для устранения проблемы.

```
Connect-AzureRmAccount
Set-AzureRmContext -SubscriptionId <subId>
$actionProperties = @{
    "Name"= "<Customer Email Address>"
    };
Invoke-AzureRmResourceAction -ResourceGroupName "<App Service Certificate Resource Group Name>" -ResourceType Microsoft.CertificateRegistration/certificateOrders -ResourceName "<App Service Certificate Resource Name>" -Action resendRequestEmails -Parameters $actionProperties -ApiVersion 2015-08-01 -Force   
```

## <a name="how-do-authentication-and-authorization-work-in-app-service"></a>Как в службе приложений выполняется проверка подлинности и авторизация?

Подробную документацию по проверке подлинности и авторизации в службе приложений см. в ресурсах для разных поставщиков услуг входа:
* [Azure Active Directory](app-service-mobile-how-to-configure-active-directory-authentication.md)
* [Facebook](app-service-mobile-how-to-configure-facebook-authentication.md)
* [Google](app-service-mobile-how-to-configure-google-authentication.md)
* [Учетная запись Майкрософт](app-service-mobile-how-to-configure-microsoft-authentication.md)
* [Twitter](app-service-mobile-how-to-configure-twitter-authentication.md)

## <a name="how-do-i-redirect-the-default-azurewebsitesnet-domain-to-my-azure-web-apps-custom-domain"></a>Как перенаправить домен по умолчанию *.azurewebsites.net в пользовательский домен моего веб-приложения Azure?

При создании нового веб-сайта с помощью функции "Веб-приложения" в Azure домен *sitename*.azurewebsites.net по умолчанию присваивается вашему сайту. Если вы добавляете к сайту имя пользовательского узла и не хотите предоставлять пользователям доступ к домену по умолчанию *.azurewebsites.net, можно перенаправить URL-адрес по умолчанию. Чтобы узнать, как перенаправлять весь трафик с домена веб-сайта по умолчанию в пользовательский домен, см. статью [Redirect the default domain to your custom domain in Azure web apps](http://www.zainrizvi.io/2016/04/07/block-default-azure-websites-domain/) (Перенаправление домена по умолчанию в пользовательский домен в службе "Веб-приложения" в Azure).

## <a name="how-do-i-determine-which-version-of-net-version-is-installed-in-app-service"></a>Как определить, какая версия платформы .NET установлена в службе приложений?

Проще всего найти версию Microsoft .NET, установленную в службе приложений, при помощи консоли Kudu. Доступ к консоли Kudu можно получить на портале или с помощью URL-адреса приложения службы приложений. Подробные инструкции см. в записи блога [Determine the installed .NET version in App Service](https://blogs.msdn.microsoft.com/waws/2016/11/02/how-to-determine-the-installed-net-version-in-azure-app-services/) (Определение установленной версии .NET в службе приложений).

## <a name="why-isnt-autoscale-working-as-expected"></a>Почему автомасштабирование не работает правильно?

Если функция автомасштабирования Azure не обрабатывает экземпляр веб-приложения правильно, возможно, используется сценарий, в котором эта функция намеренно отключена, чтобы избежать бесконечного цикла из-за нестабильности. Обычно это происходит из-за недостаточного промежутка между пороговыми значениями увеличения и уменьшения и масштаба. Дополнительные сведения о предотвращении нестабильности и другую информацию см. в разделе [Рекомендации по автомасштабированию](../monitoring-and-diagnostics/insights-autoscale-best-practices.md#autoscale-best-practices).

## <a name="why-does-autoscale-sometimes-scale-only-partially"></a>Почему автомасштабирование иногда выполняется только частично?

Автомасштабирование активируется, если метрики превышают предварительно настроенные границы. В некоторых случаях можно заметить, что по сравнению с ожидаемыми показателями емкость используется только частично. Это может произойти, если требуемое количество экземпляров недоступно. В таком случае при автомасштабировании емкость частично заполняется доступным количеством экземпляров. Функция автомасштабирования запускает логику перераспределения для получения дополнительной емкости. При этом выделяется пространство для оставшихся экземпляров. Это может занять несколько минут.

Если через несколько минут не появится ожидаемое количество экземпляров, возможно, частичного повторного заполнения оказалось достаточно, чтобы привести метрики в требуемые границы. Или при автомасштабировании уменьшен масштаб, так как достигнута нижняя граница метрики.

Если проблема обусловлена не этими причинами, отправьте запрос в службу технической поддержки.

## <a name="how-do-i-turn-on-http-compression-for-my-content"></a>Как включить сжатие HTTP для содержимого?

Чтобы включить сжатие для статических и динамических типов содержимого, добавьте следующий код в файл web.config на уровне приложения:

```
<system.webServer>
<urlCompression doStaticCompression="true" doDynamicCompression="true" />
< /system.webServer>
```

Также можно указать определенные динамические и статические типы MIME для сжатия. Дополнительные сведения см. в разделе ответов на вопросы на форуме [httpCompression settings on a simple Azure website](https://social.msdn.microsoft.com/Forums/azure/890b6d25-f7dd-4272-8970-da7798bcf25d/httpcompression-settings-on-a-simple-azure-website?forum=windowsazurewebsitespreview) (Параметры httpCompression для простого веб-сайта Azure).

## <a name="how-do-i-migrate-from-an-on-premises-environment-to-app-service"></a>Как мигрировать из локальной среды в службу приложений?

Чтобы перенести сайты с веб-серверов Windows и Linux в службу приложений, можно воспользоваться средством Migration Assistant службы приложений Azure. Средство миграции в соответствии с требованиями создает веб-приложения и базы данных в Azure и публикует содержимое. Дополнительные сведения см. на странице [Azure App Service Migration Assistant](https://www.migratetoazure.net/) (Средство Migration Assistant службы приложений Azure).
