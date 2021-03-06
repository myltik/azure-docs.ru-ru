---
title: Настройка подключения к гибридному облаку в Azure и Azure Stack | Документация Майкрософт
description: Сведения о настройке подключения к гибридному облаку в Azure и Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 048e2636aabe406728c8fe1b93ef861f13346256
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
---
# <a name="tutorial-configure-hybrid-cloud-connectivity-with-azure-and-azure-stack"></a>Руководство по настройке подключения к гибридному облаку в Azure и Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Доступ к ресурсам безопасности в глобальных Azure и Azure Stack можно получить, используя модель гибридного подключения. 

В рамках этого руководства вы создадите пример среды, чтобы:

> [!div class="checklist"]
> - Хранить данные локально для соблюдения требований конфиденциальности или нормативных требований, а также для доступа к глобальным ресурсам Azure.
> - Сохранять устаревшую систему при использовании ресурсов и развертывании приложения в масштабе облака, а также хранить ресурсы в глобальной среде Azure.

## <a name="prerequisites"></a>предварительным требованиям

Для создания гибридного развертывания подключения требуется несколько компонентов. Этот процесс может занять некоторое время. 

Партнер Azure OEM или поставщик оборудования могут развернуть производственный Azure Stack, а все пользователи могут развертывать набор разработки Azure Stack (ASDK). Оператор Azure Stack также должен развернуть службу приложений, создать планы и предложения, создать подписку клиента и добавить образ Windows Server 2016. 

Если у вас уже есть некоторые из этих компонентов, перед началом убедитесь, что они отвечают требованиям.

В этой статье также предполагается, что у вас есть определенный набор знаний об Azure и Azure Stack. Если вы хотите узнать больше, прежде чем продолжить, ознакомьтесь с такими статьями:
 - [Введение в Azure](https://azure.microsoft.com/overview/what-is-azure/).
 - [Основные понятия Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features).

### <a name="azure"></a>Таблицы Azure
 - Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.
 - Создайте [веб-приложение](https://docs.microsoft.com/vsts/build-release/apps/cd/azure/aspnet-core-to-azure-webapp?view=vsts&tabs=vsts#create-an-azure-web-app-using-the-portal) в Azure. Запишите новый URL-адрес веб-приложения, так как он будет использоваться позднее.

### <a name="azure-stack"></a>Azure Stack
 - Используйте производственный Azure Stack или разверните пакет средств разработки Azure Stack по ссылке, представленной ниже:
    - https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1 
    - Обычно установка занимает несколько часов, поэтому планируйте ее соответствующим образом.
 - Разверните службы PaaS [службы приложений](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) в Azure Stack. 
 - Создайте [план и предложения](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) в рамках среды Azure Stack. 
 - Создайте [подписку клиента](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) в рамках среды Azure Stack. 


### <a name="before-you-begin"></a>Перед началом работы

Перед началом настройки убедитесь, что удовлетворены следующие требования:

 - Убедитесь, что у вас есть общедоступный IPv4–адрес для вашего VPN–устройства. Этот IP-адрес не может располагаться вне преобразования сетевых адресов (NAT).
 - Убедитесь, что все ресурсы развернуты в том же регионе или расположении.

#### <a name="example-values"></a>Примеры значений

В примерах этой статьи мы используем следующие значения. Эти значения можно использовать для создания тестовой среды или для лучшего понимания примеров в этой статье. Общие сведения о параметрах VPN-шлюза см. в статье [Сведения о параметрах конфигурации VPN-шлюза](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings).

Спецификации подключения:
 - **Тип VPN**: на основе маршрутов
 - **Тип подключения**: "сеть — сеть" (IPsec)
 - **Тип шлюза**: VPN
 - **Имя подключения Azure**: Azure-Gateway-AzureStack-S2SGateway (портал заполнит это значение автоматически)
 - **Имя подключения Azure Stack**: AzureStack-Gateway-Azure-S2SGateway (портал заполнит это значение автоматически)
 - **Общий ключ**: все совместимые с оборудованием VPN с соответствующими значениями на обеих сторонах подключения
 - **Подписка**: любая подписка
 - **Группа ресурсов**: инфраструктура для тестирования

| Подключение Azure или Azure Stack | ИМЯ | Подсеть | IP-адрес |
|-------------------------------------|---------------------------------------------|---------------------------------------|-----------------------------|
| Виртуальная сеть Azure | ApplicationvNet<br>10.100.102.9/23 | ApplicationSubnet<br>10.100.102.0/24 |  |
|  |  | Подсеть шлюза<br>10.100.103.0/24 |  |
| Виртуальная сеть Azure Stack | ApplicationvNet<br>10.100.100.0/23 | ApplicationSubnet <br>10.100.100.0/24 |  |
|  |  | Подсеть шлюза <br>10.100101.0/24 |  |
| Шлюз виртуальной сети Azure | Azure-Gateway |  |  |
| Шлюз виртуальной сети Azure Stack | AzureStack-Gateway |  |  |
| Общедоступный IP-адрес Azure | Azure-GatewayPublicIP |  | Определяется в момент создания |
| Общедоступный IP-адрес Azure Stack | AzureStack-GatewayPublicIP |  | Определяется в момент создания |
| Шлюз локальной сети Azure | AzureStack-S2SGateway<br>   10.100.100.0/23 |  | Значение общедоступного IP-адреса Azure Stack |
| Шлюз локальной сети Azure Stack | Azure-S2SGateway<br>10.100.102.0/23 |  | Значение общедоступного IP-адреса Azure |

## <a name="create-a-virtual-network-in-global-azure-and-azure-stack"></a>Создание виртуальной сети в глобальных Azure и Azure Stack

> [!Note]  
> Вам необходимо убедиться в отсутствии перекрытия IP-адресов в адресных пространствах виртуальной сети Azure или Azure Stack. 

Создайте виртуальную сеть на основе модели развертывания Resource Manager, используя портал Azure. Если вы работаете с этими инструкциями как с руководством, используйте [примеры значений](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#values). В противном случае замените значения собственными. 

1. В браузере откройте [портал Azure](http://portal.azure.com/) и выполните вход с помощью учетной записи Azure.
2. Щелкните **Создать ресурс**. В поле **Поиск в Marketplace** введите `virtual network`. В результатах поиска найдите и щелкните **Виртуальная сеть**, и откройте страницу **Виртуальная сеть**.
3. В нижней части страницы "Виртуальная сеть" из списка **Выберите модель развертывания** выберите **Resource Manager** и щелкните **Создать**. Откроется страница "Создание виртуальной сети".
4. На странице **Создание виртуальной сети** настройте параметры виртуальной сети. Если введенные в полях значения допустимы, красный восклицательный знак сменится зеленой галочкой.
5. Повторите эти шаги на **портале клиента** Azure Stack.

## <a name="add-a-gateway-subnet"></a>Добавление подсети шлюза

Прежде чем подключать шлюз к виртуальной сети, нужно создать подсеть шлюза для виртуальной сети, к которой необходимо подключиться. Службы шлюза используют IP-адреса, указанные в подсети шлюза.

На [портале](http://portal.azure.com/) перейдите к виртуальной сети Resource Manager, для которой необходимо создать шлюз.

1. На странице виртуальной сети в разделе **Параметры** щелкните **Подсети**, чтобы открыть страницу **Подсети**.
2. На странице **Подсети** щелкните **+Gateway subnet** (+Подсеть шлюза). Откроется страница **Добавление подсети**.

    ![](media/azure-stack-solution-hybrid-connectivity/image4.png)

3. В поле **Имя** автоматически добавляется значение GatewaySubnet. По этому имени Azure идентифицирует подсеть как подсеть шлюза. Настройте автоматическое заполнение значений **диапазона адресов** для соответствия требованиям конфигурации, а затем щелкните **ОК** в нижней части страницы, чтобы создать подсеть.

## <a name="create-a-virtual-network-gateway-in-azure-and-azure-stack"></a>Создание шлюза виртуальной сети в Azure и Azure Stack

1. На странице портала слева щелкните "+" и выполните поиск по запросу "Шлюз виртуальной сети". В разделе **результатов** найдите и щелкните **Шлюз виртуальной сети**.
2. Щелкните **Создать** в нижней части страницы **Шлюз виртуальной сети**. Откроется страница **Создание шлюза виртуальной сети**.
3. На странице **Создание шлюза виртуальной сети** укажите значения для шлюза виртуальной сети, как описано в примере значений, а также дополнительные значения, описанные ниже.
    - **Номер SKU**. Базовый.
    - **Виртуальная сеть**. Выберите созданную ранее виртуальную сеть. Будет автоматически выбрана созданная ранее подсеть шлюза. 
    - **Первая IP-конфигурация**. Общедоступный IP-адрес шлюза. 
        - Щелкните **Создать IP-конфигурацию шлюза**. Откроется страница **Выбрать общедоступный IP-адрес**.
        - Щелкните **+ Создать**, чтобы открыть страницу **Создать общедоступный IP-адрес**.
        - Укажите **имя** для общедоступного IP-адреса. Задайте для номера SKU значение **Базовый**, а затем щелкните **ОК** в нижней части этой страницы, чтобы сохранить изменения.

    > [!Note]  
    > В настоящее время VPN-шлюз поддерживает только динамическое выделение общедоступных IP-адресов. Однако это не означает, что IP-адрес изменяется после назначения VPN-шлюзу. Общедоступный IP-адрес изменяется только после удаления и повторного создания шлюза. При изменении размера, сбросе или других внутренних операциях обслуживания или обновления IP-адрес VPN-шлюза не изменяется.

4. Проверьте параметры. 
5. Щелкните **Создать**, чтобы начать создание VPN-шлюза. Параметры будут проверены, и на панели мониторинга появится плитка "Развертывание шлюза виртуальной сети". Создание шлюза может занять до 45 минут. Вам понадобится обновить страницу портала, чтобы увидеть готовое состояние.

    Создав шлюз, вы можете просмотреть назначенный ему IP-адрес в разделе сведений о виртуальной сети на портале. Шлюз будет отображаться как подключенное устройство. Вы можете щелкнуть подключенное устройство (шлюз виртуальной сети), чтобы получить дополнительные сведения.
6. Повторите эти шаги для развертывания Azure Stack.

## <a name="create-the-local-network-gateway-in-azure-and-azure-stack"></a>Создание шлюза локальной сети в Azure и Azure Stack

Обычно термин "шлюз локальной сети" означает локальное расположение. Присвойте сайту имя, по которому Azure или Azure Stack может обращаться к этому сайту, а затем укажите IP-адрес локального VPN-устройства, к которому вы подключитесь. Вы можете также указать префиксы IP-адресов, которые будут направляться через VPN-шлюз к VPN-устройству. Указываемые префиксы адресов расположены в локальной сети. Если вы внесли изменения в локальной сети или вам нужно изменить общедоступный IP-адрес для VPN-устройства, значения можно легко обновить позже.

1. На портале выберите **+ Создать ресурс**.
2. В поле поиска введите **шлюз локальной сети** и нажмите клавишу **ВВОД** для выполнения поиска. Будет возвращен список результатов. Щелкните **Шлюз локальной сети**, а затем нажмите кнопку **Создать**, чтобы открыть страницу **Создание шлюза локальной сети**.
3. На странице **Создание шлюза локальной сети** укажите значения для шлюза локальной сети, как описано в примере значений, а также дополнительные значения, описанные ниже.
    - **IP-адрес** — это общедоступный IP-адрес VPN-устройства, к которому вы хотите подключить Azure или Azure Stack. Укажите допустимый общедоступный IP-адрес. Этот IP-адрес не может быть скрыт за NAT и должен быть доступен для Azure. Если у вас сейчас нет IP-адреса, вы можете использовать значения, показанные в примере. Но позже вам нужно будет заменить заполнитель IP-адреса общедоступным IP-адресом устройства VPN. Иначе Azure не удастся установить подключение.
    - **Адресное пространство** обозначает диапазон адресов для сети, которую представляет эта локальная сеть. Можно добавить несколько диапазонов пространства адресов. Убедитесь, что указанные диапазоны не перекрывают диапазоны других сетей, к которым необходимо подключиться. Azure будет маршрутизировать указанный диапазон адресов на IP-адрес локального VPN-устройства. Чтобы подключиться к локальному сайту, используйте здесь собственные значения, а не значения из примера.
    - **Настроить параметры BGP** — используйте только при настройке BGP. В противном случае не выбирайте этот параметр.
    - **Подписка** — убедитесь, что указана правильная подписка.
    - **Группа ресурсов** — выберите нужную группу ресурсов. Можно создать новую группу ресурсов или выбрать уже созданную.
    - **Расположение** — укажите расположение, в котором будет создан этот объект. Вы можете выбрать расположение, в котором размещена виртуальная сеть, но это не обязательно.
4. Завершив ввод значений, щелкните **Создать** в нижней части страницы, чтобы создать шлюз локальной сети.
5. Повторите эти шаги для развертывания Azure Stack.

## <a name="configure-your-connection"></a>Настройка подключения

Для подключения типа "сеть — сеть" к локальной сети требуется VPN-устройство. На этом этапе вы настроите VPN-устройство, известное как подключение. Чтобы настроить подключение, вам потребуется следующее:
    - Общий ключ. Это тот же общий ключ, который указывается при создании VPN-подключения "сеть — сеть". В наших примерах мы используем простые общие ключи. Для практического использования рекомендуется создавать более сложные ключи.
    - Общедоступный IP-адрес шлюза виртуальной сети. Общедоступный IP-адрес можно просмотреть с помощью портала Azure, PowerShell или CLI. Чтобы найти общедоступный IP-адрес VPN-шлюза с помощью портала Azure, перейдите к разделу "Шлюзы виртуальной сети" и щелкните имя шлюза.
Создайте VPN-подключение типа "сеть — сеть" между шлюзом виртуальной сети и локальным VPN-устройством.
1. На портале выберите **+ Создать ресурс**.
2. В поле поиска введите **Подключения** и нажмите клавишу **ВВОД** для выполнения поиска. Будет возвращен список результатов. Щелкните **Подключения**, нажмите кнопку "Создать", чтобы открыть страницу **Создать подключения**.
3. На странице **Создать подключения** задайте значения для подключения.
     - Основные сведения:
        1. **Тип подключения**. Выберите "сеть — сеть" (IPSec).
        2. **Группа ресурсов**. Выберите группу ресурсов для тестирования.
     - Параметры
        1. **Шлюз виртуальной сети**. Выберите созданный ранее шлюз виртуальной сети.
        2. **Шлюз локальной сети**. Выберите созданный ранее шлюз локальной сети. 
        3. **Имя подключения**. Будет заполняться автоматически значениями из двух шлюзов. 
        4. **Общий ключ**. Это значение должно соответствовать ключу локального VPN-устройства. В этом примере используется abc123, но вы можете (это рекомендуется) создать и использовать что-нибудь посложнее. Важно, чтобы заданное здесь значение совпадало со значением, указываемым при настройке вашего VPN-устройства.
    - Остальные значения для **подписки**, **группы ресурсов**, и **расположения** являются фиксированными.
4. Щелкните **ОК** для создания подключения. На экране появится надпись "Идет создание подключения".
5. Данные созданного подключения появятся на странице **"Подключения" шлюза виртуальной сети. Состояние изменится с **Unknown** (Неизвестно) на **Подключение**, а затем — **Успешно**.

## <a name="next-steps"></a>Дополнительная информация

- Дополнительные сведения о шаблонах для облака Azure см. в статье [Конструктивные шаблоны облачных решений](https://docs.microsoft.com/azure/architecture/patterns).
