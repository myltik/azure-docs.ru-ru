<properties
  pageTitle="Часто задаваемые вопросы по Azure IoT Suite | Microsoft Azure"
  description="Часто задаваемые вопросы об IoT Suite"
  services=""
  suite="iot-suite"
  documentationCenter=""
  authors="aguilaaj"
  manager="timlt"
  editor=""/>

<tags
  ms.service="iot-suite"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="na"
  ms.date="06/27/2016"
  ms.author="araguila"/>
   
# Часто задаваемые вопросы об IoT Suite

### В чем разница между удалением группы ресурсов на портале Azure и нажатием кнопки "Удалить" в предварительно настроенном решении на сайте azureiotsuite.com?

- Если удалить предварительно настроенное решение на сайте [azureiotsuite.com][lnk-azureiotsuite], будут удалены все ресурсы, подготовленные при создании этого решения. Если вы добавляли в группу ресурсов дополнительные ресурсы, они также будут удалены.

- Если удалить группу ресурсов на [портале Azure][lnk-azure-portal], будут удалены только ресурсы в этой группе. Вам также придется удалить приложение Azure Active Directory, связанное с предварительно настроенным решением, на [классическом портале Azure][lnk-classic-portal].

### Сколько экземпляров центров IoT можно подготовить в рамках одной подписки? 

Десять. Вы можете [направить запрос в службу поддержки Azure][link-azuresupportticket], чтобы увеличить это количество, но по умолчанию в рамках одной подписки можно подготовить только десять экземпляров центров IoT, как описано в разделе [об ограничениях подписки Azure][link-azuresublimits]. Так как каждое предварительно настроенное решение подготавливает новый центр IoT, в рамках одной подписки можно подготовить до десяти таких решений.

### Сколько экземпляров DocumentDB можно подготовить в рамках одной подписки?

Пятьдесят. Вы можете [направить запрос в службу поддержки Azure][link-azuresupportticket], чтобы увеличить это количество, но по умолчанию в рамках одной подписки можно подготовить только пятьдесят экземпляров DocumentDB.

### Сколько бесплатных API-интерфейсов Карт Bing можно подготовить в рамках одной подписки?

Два. В рамках подписки Azure можно создать только две карты Bing уровня 1 с внутренними транзакциями для корпоративных планов. Решение для удаленного мониторинга по умолчанию подготавливается с помощью плана уровня 1 с внутренними транзакциями. Иными словами, используя одну подписку, в которую не вносились изменения, вы можете подготовить не более двух решений для удаленного мониторинга.

### У меня есть развернутое решение для удаленного мониторинга со статической картой. Как мне добавить интерактивную карту Bing? 
1. Получите на [портале Azure][lnk-azure-portal] ключ QueryKey для Bing Maps API for Enterprise:
 1. На [портале Azure][lnk-azure-portal] перейдите в группу ресурсов с учетной записью Bing Maps API for Enterprise.
 2. Щелкните "Все параметры" и "Управление ключами".
 3. Вы увидите два ключа: MasterKey и QueryKey. Скопируйте значение ключа QueryKey.

     > [AZURE.NOTE] У вас нет учетной записи Bing Maps API for Enterprise? Создайте ее на [портале Azure][lnk-azure-portal], щелкнув "+ Создать". Затем найдите Bing Maps API for Enterprise и следуйте подсказкам по созданию.

2. Разверните последний фрагмент кода из архива [Azure-IoT-Remote-Monitoring][lnk-remote-monitoring-github].

3. Запустите развертывание (локально или в облаке) в соответствии с руководством по развертыванию, которое находится в папке /docs/ архива.

4. Запустив развертывание (локально или в облаке), найдите в корневой папке файл *.user.config, созданный во время развертывания. Откройте этот файл в текстовом редакторе.

5. Измените следующую строку, чтобы включить скопированное значение ключа QueryKey:
   
  `<setting name="MapApiQueryKey" value="" />`

### Можно ли создать предварительно настроенное решение при наличии Microsoft Azure для DreamSpark?
Сейчас нельзя создавать предварительно настроенные решения с использованием учетной записи [Microsoft Azure для DreamSpark][lnk-dreamspark]. Но вы можете создать [бесплатную пробную учетную запись Azure][lnk-30daytrial] всего за несколько минут. Это позволит вам создать предварительно настроенное решение.

### Как удалить клиент AAD?

См. запись блога Эрика Голпа (Eric Golpe) [Пошаговое руководство по удалению клиента Azure AD][lnk-delete-aad-tennant].

## Дальнейшие действия

Вы также можете ознакомиться с другими функциями и возможностями предварительно настроенных решений IoT Suite.

- [Обзор предварительно настроенного решения прогнозного обслуживания][lnk-predictive-overview]
- [Все аспекты безопасности IoT][lnk-security-groundup]

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-security-groundup]: securing-iot-ground-up.md

[link-azuresupportticket]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[link-azuresublimits]: https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/#iot-hub-limits
[lnk-azure-portal]: https://portal.azure.com
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-remote-monitoring-github]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-dreamspark]: https://www.dreamspark.com/Product/Product.aspx?productid=99
[lnk-30daytrial]: https://azure.microsoft.com/free/
[lnk-delete-aad-tennant]: http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx

<!---HONumber=AcomDC_0810_2016-->