---
title: "Часто задаваемые вопросы об управлении API Azure | Документация Майкрософт"
description: "Ознакомьтесь с ответами на часто задаваемые вопросы, шаблонами и рекомендациями относительно службы управления API Azure."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 2fa193cd-ea71-4b33-a5ca-1f55e5351e23
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.translationtype: HT
ms.sourcegitcommit: 74b75232b4b1c14dbb81151cdab5856a1e4da28c
ms.openlocfilehash: a0bf8995913511b0e14304a1259f13de4aa9e04b
ms.contentlocale: ru-ru
ms.lasthandoff: 07/26/2017

---
# <a name="azure-api-management-faqs"></a>Часто задаваемые вопросы о службе управления API Azure
Ознакомьтесь с ответами на часто задаваемые вопросы, шаблонами и рекомендациями относительно службы управления API Azure.

## <a name="contact-us"></a>Свяжитесь с нами
* [Как задать вопрос рабочей группе службы управления API Microsoft Azure?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question)


## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы
* [Что означает, если функция пребывает в предварительной версии?](#what-does-it-mean-when-a-feature-is-in-preview)
* [Как защитить подключение шлюза управления API к внутренним службам?](#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services)
* [Как скопировать экземпляр службы управления API в новый экземпляр?](#how-do-i-copy-my-api-management-service-instance-to-a-new-instance)
* [Можно ли управлять экземпляром службы управления API программными средствами?](#can-i-manage-my-api-management-instance-programmatically)
* [Как добавить пользователя в группу администраторов?](#how-do-i-add-a-user-to-the-administrators-group)
* [Почему политика, которую я хочу добавить, недоступна в редакторе политик?](#why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor)
* [Как использовать управление версиями в службе управления API?](#how-do-i-use-api-versioning-in-api-management)
* [Как настроить несколько сред в одном API?](#how-do-i-set-up-multiple-environments-in-a-single-api)
* [Можно ли использовать SOAP в управлении API?](#can-i-use-soap-with-api-management)
* [Является ли IP-адрес шлюза управления API постоянным? Можно ли использовать его в правилах брандмауэра?](#is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules)
* [Можно ли настроить на сервере авторизации OAuth 2.0 систему безопасности служб федерации Active Directory?](#can-i-configure-an-oauth-20-authorization-server-with-adfs-security)
* [Какой метод маршрутизации использует служба управления API при развертывании в нескольких географических расположениях?](#what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations)
* [Можно ли использовать шаблон Azure Resource Manager, чтобы создать экземпляр службы управления API?](#can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance)
* [Можно ли использовать самозаверяющий сертификат SSL для сервера?](#can-i-use-a-self-signed-ssl-certificate-for-a-back-end)
* [Почему происходит ошибка проверки подлинности при попытке клонировать репозиторий Git?](#why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository)
* [Работает ли управление API с Azure ExpressRoute?](#does-api-management-work-with-azure-expressroute)
* [Зачем требуется выделенная подсеть в виртуальных сетях типа Resource Manager, если в них развернута служба управления API?](#why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them)
* [Каков минимальный размер подсети, требуемый при развертывании службы управления API в виртуальной сети?](#what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet)
* [Можно ли перенести экземпляр службы управления API из одной подписки в другую?](#can-i-move-an-api-management-service-from-one-subscription-to-another)
* [Существуют ли ограничения на импорт API или известные проблемы, связанные с этим процессом?](#are-there-restrictions-on-or-known-issues-with-importing-my-api)

### <a name="how-can-i-ask-the-microsoft-azure-api-management-team-a-question"></a>Как задать вопрос рабочей группе службы управления API Microsoft Azure?
С нами можно связаться одним из следующих способов:

* Свои вопросы вы можете разместить на нашем [форуме MSDN для службы управления API](https://social.msdn.microsoft.com/forums/azure/home?forum=azureapimgmt).
* Отправьте сообщение электронной почты по адресу <mailto:apimgmt@microsoft.com>.
* Отправьте нам запрос на функцию на [форуме отзывов Azure](https://feedback.azure.com/forums/248703-api-management).

### <a name="what-does-it-mean-when-a-feature-is-in-preview"></a>Что означает, если функция пребывает в предварительной версии?
Это значит, что мы активно изучаем отзывы пользователей о работе функции. Функция в предварительной версии фактически завершена, но, возможно, мы внесем важные изменения в ответ на отзывы клиентов. Мы рекомендуем не закладывать в основу рабочей среды функции в предварительной версии. Если вы хотите оставить отзыв о функциях, доступных в предварительной версии, отправьте нам его одним из способов, описанных в разделе [Как задать вопрос рабочей группе службы управления API Microsoft Azure?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question).

### <a name="how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services"></a>Как защитить подключение шлюза управления API к внутренним службам?
Существует несколько способов защитить такое подключение. Вы можете:

* Использование обычной проверки подлинности HTTP. Дополнительные сведения см. в разделе [Настройка параметров API](api-management-howto-create-apis.md#configure-api-settings).
* использовать взаимную проверку подлинности SSL, как описано в статье [Защита фоновых служб посредством проверки подлинности с помощью сертификата клиента в службе Azure API Management](api-management-howto-mutual-certificates.md).
* использовать разрешенный список IP-адресов во внутренних службах. Если у вас есть экземпляр службы управления API уровня "Стандартный" или "Премиум", IP-адрес шлюза остается неизменным. Можно задать список разрешений и включить в него этот IP-адрес. IP-адрес экземпляра службы управления API можно узнать на панели мониторинга на портале Azure.
* подключить экземпляр службы управления API к виртуальной сети Azure.

### <a name="how-do-i-copy-my-api-management-service-instance-to-a-new-instance"></a>Как скопировать экземпляр службы управления API в новый экземпляр?
Существует несколько способов сделать это. Вы можете:

* использовать функцию архивации и восстановления в службе управления API. Дополнительные сведения см. в статье [Реализация аварийного восстановления с помощью функций резервного копирования и восстановления службы в Azure API Management](api-management-howto-disaster-recovery-backup-restore.md).
* создать функцию архивации и восстановления с помощью [REST API управления API](https://msdn.microsoft.com/library/azure/dn776326.aspx). Используйте REST API, чтобы сохранить и восстановить сущности из необходимого экземпляра службы.
* Скачайте файл конфигурации службы с помощью Git и загрузите этот файл в новый экземпляр. Дополнительные сведения см. в статье [Сохранение и настройка конфигурации службы управления API с помощью Git](api-management-configuration-repository-git.md).

### <a name="can-i-manage-my-api-management-instance-programmatically"></a>Можно ли управлять экземпляром службы управления API программными средствами?
Да, вы можете управлять службой управления API с помощью программных средств:

* [REST API службы управления API](https://msdn.microsoft.com/library/azure/dn776326.aspx);
* [SDK для библиотеки управления службой ApiManagement Microsoft Azure](http://aka.ms/apimsdk);
* командлетов PowerShell для [развертывания служб](https://msdn.microsoft.com/library/mt619282.aspx) и [управления ими](https://msdn.microsoft.com/library/mt613507.aspx).

### <a name="how-do-i-add-a-user-to-the-administrators-group"></a>Как добавить пользователя в группу администраторов?
Есть несколько способов добавления пользователя в эту группу:

1. Войдите на [портал Azure](https://portal.azure.com).
2. Перейдите в группу ресурсов, где содержится экземпляр службы управления API, который нужно обновить.
3. В службе управления API назначьте роль **Api Management Contributor** (Участник управления API) пользователю.

Теперь добавленный участник сможет использовать [командлеты](https://msdn.microsoft.com/library/mt613507.aspx) Azure PowerShell. Войдите в систему от имени администратора:

1. Используйте командлет `Login-AzureRmAccount`, чтобы войти.
2. Задайте контекст для подписки, содержащей службу, используя `Set-AzureRmContext -SubscriptionID <subscriptionGUID>`.
3. Получите URL-адрес единого входа, выполнив `Get-AzureRmApiManagementSsoToken -ResourceGroupName <rgName> -Name <serviceName>`.
4. Используйте этот URL-адрес, чтобы войти на портал администрирования.

### <a name="why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor"></a>Почему политика, которую я хочу добавить, недоступна в редакторе политик?
Если в редакторе политик нужная политика отображается серым цветом или недоступна, убедитесь в правильности области ее действия. Каждый оператор предназначен для использования в конкретной области и разделе политики. Разделы политики и области ее действия см. в подразделе об использовании в разделе [API Management policies](https://msdn.microsoft.com/library/azure/dn894080.aspx) (Политики управления API).

### <a name="how-do-i-use-api-versioning-in-api-management"></a>Как использовать управление версиями в службе управления API?
Управление версиями в службе управлении API предполагает несколько способов:

* В службе управления API вы можете настроить отдельные API, представляющие разные версии. Например, может понадобиться настроить два разных API, MyAPIv1 и MyAPIv2. Разработчик может выбрать версию, которую необходимо использовать.
* Вы также можете настроить для API URL-адрес службы без учета версии, например https://my.api. Затем необходимо настроить сегмент версии для шаблона [перезаписи URL-адреса](https://msdn.microsoft.com/library/azure/dn894083.aspx#RewriteURL) каждой операции. Например, есть операция, в которой используется [шаблон URL-адреса](api-management-howto-add-operations.md#url-template) /resource и [перезаписи URL-адреса](api-management-howto-add-operations.md#rewrite-url-template) /v1/Resource. Вы можете изменить значение сегмента версии для каждой операции по отдельности.
* Если вы хотите оставить в URL-адресе службы API сегмент версии по умолчанию, задайте для выбранных операций политику, использующую политику [задания внутренней службы](https://msdn.microsoft.com/library/azure/dn894083.aspx#SetBackendService), изменяющую путь запроса к внутренней службе.

### <a name="how-do-i-set-up-multiple-environments-in-a-single-api"></a>Как настроить несколько сред в одном API?
Чтобы настроить несколько сред, например тестовую и рабочую, в одном API, используйте один из двух способов. Вы можете:

* разместить различные API в одном клиенте;
* разместить один и тот же API в разных клиентах.

### <a name="can-i-use-soap-with-api-management"></a>Можно ли использовать SOAP в управлении API?
Сейчас поддерживается [сквозная передача SOAP](http://blogs.msdn.microsoft.com/apimanagement/2016/10/13/soap-pass-through/). Администраторы могут импортировать WSDL-файл своей службы SOAP, и служба управления API Azure создаст внешний интерфейс SOAP. Для служб SOAP доступны документация портала для разработчиков, консоль тестирования, политики и средства анализа.

### <a name="is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules"></a>Является ли IP-адрес шлюза управления API постоянным? Можно ли использовать его в правилах брандмауэра?
На уровнях "Стандартный" и "Премиум" общедоступный (виртуальный) IP-адрес клиента управления API является статическим в течение всего времени существования клиента, кроме исключений ниже. IP-адрес изменяется в таких случаях:

* Служба удалена или создана повторно.
* Подписка на службу переведена в состояние [Приостановлено](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) или [С предупреждением](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) (например, из-за неуплаты), а затем ее действие восстановлено.
* Вы добавили или удалили виртуальную сеть Azure (ее можно использовать только на уровне "Разработчик" или "Премиум").

При развертывании в нескольких регионах региональный адрес изменяется, если остановить работу региона, а затем восстановить ее (развертывание в нескольких регионах можно использовать только на только на уровне "Премиум").

Клиентам уровня "Премиум", для которых настроено развертывание в нескольких регионах, назначается один общедоступный IP-адрес на регион.

IP-адрес (или адреса в случае развертывания в нескольких регионах) есть на странице клиента на портале Azure.

### <a name="can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security"></a>Можно ли настроить на сервере авторизации OAuth 2.0 систему безопасности служб федерации Active Directory?
Дополнительные сведения о настройке системы безопасности служб федерации Active Directory (AD FS) на сервере авторизации OAuth 2.0 см. в записи блога [Using ADFS in API Management](https://phvbaars.wordpress.com/2016/02/06/using-adfs-in-api-management/) (Использование служб федерации Active Directory в службе управления API).

### <a name="what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations"></a>Какой метод маршрутизации использует служба управления API при развертывании в нескольких географических расположениях?
При развертывании в нескольких географических расположениях служба управления API применяет [маршрутизацию трафика для повышения производительности](../traffic-manager/traffic-manager-routing-methods.md#priority). Входящий трафик направляется на ближайший шлюз API. Если регион становится недоступен, входящий трафик автоматически маршрутизируется на следующий ближайший шлюз. Дополнительные сведения о методах маршрутизации см. в статье [Методы маршрутизации трафика диспетчером трафика](../traffic-manager/traffic-manager-routing-methods.md).

### <a name="can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance"></a>Можно ли использовать шаблон Azure Resource Manager, чтобы создать экземпляр службы управления API?
Да. Чтобы получить шаблоны быстрого запуска, посетите страницу [службы управления API Azure](http://aka.ms/apimtemplate).

### <a name="can-i-use-a-self-signed-ssl-certificate-for-a-back-end"></a>Можно ли использовать самозаверяющий сертификат SSL для сервера?
Да. Вот как можно использовать его для серверной части:

1. Создайте [серверную](https://msdn.microsoft.com/library/azure/dn935030.aspx) сущность с помощью службы управления API.
2. Задайте для свойства **skipCertificateChainValidation** значение **true**.
3. Если вы хотите запретить использование самозаверяющего сертификата, удалите серверную сущность или задайте для свойства **skipCertificateChainValidation** значение **false**.

### <a name="why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository"></a>Почему происходит ошибка проверки подлинности при попытке клонировать репозиторий Git?
Если вы используете диспетчер учетных данных Git или пытаетесь клонировать репозиторий с помощью Visual Studio, вы можете столкнуться с известной проблемой в диалоговом окне учетных данных Windows. Это диалоговое окно ограничивает длину пароля до 127 символов и усекает пароль, созданный корпорацией Майкрософт. Мы работаем над сокращением пароля. Пока для клонирования используйте Git Bash.

### <a name="does-api-management-work-with-azure-expressroute"></a>Работает ли управление API с Azure ExpressRoute?
Да. Служба управления API работает с Azure ExpressRoute.

### <a name="why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them"></a>Зачем требуется выделенная подсеть в виртуальных сетях типа Resource Manager, если в них развернута служба управления API?
Выделенная подсеть для службы управления API требуется по той причине, что служба основана на классической модели развертывания (уровень PAAS V1). Хотя ее можно развернуть в виртуальной сети Resource Manager (уровень V2), имеются последствия. Классическая модель развертывания в Azure не связана тесно с моделью развертывания с помощью Resource Manager. Поэтому при создании ресурса на уровне V2 уровень V1 не знает об этом, и возможны проблемы. Например, служба управления API может попытаться использовать IP-адрес, который уже выделен сетевому адаптеру (основанному на уровне V2).
Дополнительные сведения о различиях между классической моделью и моделью Resource Manager в Azure см. в разделе [Развертывание с помощью Azure Resource Manager и классическое развертывание](../azure-resource-manager/resource-manager-deployment-model.md).

### <a name="what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet"></a>Каков минимальный размер подсети, требуемый при развертывании службы управления API в виртуальной сети?
Минимальный размер подсети, необходимой для развертывания службы управления API, — [/29](../virtual-network/virtual-networks-faq.md#configuration) (это минимальный размер подсети, поддерживаемый в Azure).

### <a name="can-i-move-an-api-management-service-from-one-subscription-to-another"></a>Можно ли перенести экземпляр службы управления API из одной подписки в другую?
Да. Чтобы узнать, как это сделать, см. статью [Перемещение ресурсов в новую группу ресурсов или подписку](../azure-resource-manager/resource-group-move-resources.md).

### <a name="are-there-restrictions-on-or-known-issues-with-importing-my-api"></a>Существуют ли ограничения на импорт API или известные проблемы, связанные с этим процессом?
[Известные проблемы и ограничения](api-management-api-import-restrictions.md) для форматов Open API (Swagger), WSDL и WADL.

