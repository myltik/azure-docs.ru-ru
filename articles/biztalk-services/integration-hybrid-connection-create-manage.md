---
title: Создание гибридных подключений и управление ими | Документация Майкрософт
description: Узнайте, как создать гибридное подключение, управлять подключением и установить диспетчер гибридных подключений. MABS, WABS
services: biztalk-services
documentationcenter: ''
author: MandiOhlinger
manager: erikre
editor: ''
ms.assetid: aac0546b-3bae-41e0-b874-583491a395ea
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: ccompy
ms.openlocfilehash: 1751d33b5f6f6a506654daedd15bbd75ae271483
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2017
ms.locfileid: "26736962"
---
# <a name="create-and-manage-hybrid-connections"></a>Создание гибридных подключений и управление ими

> [!IMPORTANT]
> Гибридные подключения BizTalk больше не используются. Они заменены гибридными подключениями службы приложений. Дополнительную информацию, включая сведения об управлении существующими гибридными подключениями BizTalk, см. в статье [Гибридные подключения к службе приложений Azure](../app-service/app-service-hybrid-connections.md).

>[!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

## <a name="overview-of-the-steps"></a>Описание действий
1. Создание гибридного подключения путем ввода **host name** или **FQDN** of the on-premises resource in your private netwилиk.
2. Связывание веб-приложений Azure или мобильных приложений Azure с гибридным подключением.
3. Установка диспетчера гибридных подключений на локальном ресурсе и соединение с конкретным гибридным подключением. Портал Azure предлагает простой интерфейс для установки и подключения.
4. Управление гибридными подключениями и их ключами.

В этом разделе перечислены эти действия. 

> [!IMPORTANT]
> IP-адресу можно задать конечную точку гибридного подключения. Если вы используете IP-адрес, вы можете получить или не получить доступ к локальному ресурсу. Это зависит от вашего клиента. Гибридное подключение зависит от клиента, который выполняет поиск DNS. В большинстве случаев **клиент** — это код вашего приложения. Если клиент не выполняет поиск DNS (не пытается разрешить IP-адрес как имя домена (x.x.x.x)), трафик не отправляется через гибридное подключение.
> 
> Например, вы задали **10.4.5.6** в качестве локального хоста (это псевдокод):
> 
> **Следующий сценарий будет работать:**  
> `Application code -> GetHostByName("10.4.5.6") -> Resolves to 127.0.0.3 -> Connect("127.0.0.3") -> Hybrid Connection -> on-prem host`
> 
> **Следующий сценарий не будет работать:**  
> `Application code -> Connect("10.4.5.6") -> ?? -> No route to host`
> 
> 

## <a name="CreateHybridConnection"></a>Создание гибридного подключения
Гибридное подключение может быть создано с помощью функции [гибридных подключений к Службе приложений Azure](../app-service/app-service-hybrid-connections.md) **или** с использованием [REST API служб BizTalk](https://msdn.microsoft.com/library/azure/dn232347.aspx). 

<!-- **To create Hybrid Connections using Web Apps**, see [Connect Azure Web Apps to an On-Premises Resource](../app-service-web/web-sites-hybrid-connection-get-started.md). You can also install the Hybrid Connection Manager (HCM) from your web app, which is the preferred method.  -->

#### <a name="additional"></a>Дополнительно
* Можно создать несколько гибридных подключений. См. число разрешенных подключений в статье [Службы BizTalk: диаграмма выпусков](biztalk-editions-feature-chart.md). 
* Каждое гибридное подключение создается с помощью пары строк подключения: ключи приложений, которые ОТПРАВЛЯЮТ и локальные ключи, которые ПРОСЛУШИВАЮТ. В каждой паре есть первичный и вторичный ключи. 

## <a name="LinkWebSite"></a>Связывание веб-приложения службы приложений Azure или мобильного приложения
Чтобы связать веб-приложение или мобильное приложение с существующим гибридным подключением, в колонке "Гибридные подключения" выберите элемент **Использовать существующее гибридное подключение**. 
<!-- See [Access on-premises resources using hybrid connections in Azure App Service](../app-service-web/web-sites-hybrid-connection-get-started.md). -->

## <a name="InstallHCM"></a>Локальная установка диспетчера гибридных подключений
После создания гибридного подключения необходимо установить диспетчер гибридных подключений на локальном ресурсе. Его можно скачать из веб-приложения Azure или из службы BizTalk. 

[!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]
 
Рекомендуем воспользоваться [гибридными подключениями к Службе приложений Azure](../app-service/app-service-hybrid-connections.md).

<!--
You can also download the Hybrid Connection Manager MSI file and copy the file to your on-premises resource. Specific steps:

1. Copy the on-premises primary Connection String. See [Manage Hybrid Connections](#ManageHybridConnection) in this topic for the specific steps.
2. Download the Hybrid Connection Manager MSI file. 
3. On the on-premises resource, install the Hybrid Connection Manager from the MSI file. 
4. Using Windows PowerShell, type: 
> Add-HybridConnection -ConnectionString “*Your On-Premises Connection String that you copied*” 
--> 

#### <a name="additional"></a>Дополнительно
* Диспетчер гибридных подключений можно установить в следующих операционных системах:
  
  * Windows Server 2008 R2 (требуются .NET Framework 4.5 или более поздней версии и Windows Management Framework 4.0 или более поздней версии);
  * Windows Server 2012 (требуется Windows Management Framework 4.0 или более поздней версии).
  * Windows Server 2012 R2
* После установки диспетчера гибридных подключений произойдут следующие события: 
  
  * Автоматическая настройка гибридного подключения на платформе Azure для использования основной строки подключения к приложению. 
  * Автоматическая настройка локального ресурса для использования основной локальной строки подключения.
* Для прохождения авторизации диспетчер гибридных подключений должен использовать допустимую локальную строку подключения. Для прохождения авторизации веб-приложение или мобильное приложение Azure должны использовать допустимую строку подключения к приложению.
* Гибридные подключения можно масштабировать путем установки другого экземпляра диспетчера гибридных подключений на другом сервере. Настройте локальный прослушиватель для использования того же адреса в качестве первого локального прослушивателя. В этом случае трафик распределяется случайно (путем циклического перебора) между активными локальными прослушивателями. 

## <a name="ManageHybridConnection"></a>Управление гибридными подключениями

[!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)] 

Рекомендуем воспользоваться [гибридными подключениями к Службе приложений Azure](../app-service/app-service-hybrid-connections.md).

#### <a name="copyregenerate-the-hybrid-connection-strings"></a>Копирование или повторное создание строк гибридных подключений

[!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)] 

Рекомендуем воспользоваться [гибридными подключениями к Службе приложений Azure](../app-service/app-service-hybrid-connections.md).

#### <a name="use-group-policy-to-control-the-on-premises-resources-used-by-a-hybrid-connection"></a>Применение групповой политики для контроля локальных ресурсов, используемых гибридным подключением
1. Скачайте [административные шаблоны диспетчера гибридных подключений](http://www.microsoft.com/download/details.aspx?id=42963).
2. Извлеките файлы.
3. На компьютере, где осуществляется изменение групповой политики, выполните следующие действия.  
   
   * Скопируйте файлы ADMX в папку *%WINROOT%\PolicyDefinitions*.
   * Скопируйте файлы ADMX в папку *%WINROOT%\PolicyDefinitions\en-us*.

После копирования файлов можно изменить политику с помощью редактора групповой политики.

## <a name="next"></a>Далее
[Обзор гибридных подключений](integration-hybrid-connection-overview.md)

## <a name="see-also"></a>См. также
[REST API для управления службами BizTalk в Microsoft Azure](http://msdn.microsoft.com/library/azure/dn232347.aspx)  
[Службы BizTalk: диаграмма выпусков](biztalk-editions-feature-chart.md)  
[Создание службы BizTalk](biztalk-provision-services.md)  
[Службы BizTalk: вкладки «Панель мониторинга», «Монитор» и «Масштаб»](biztalk-dashboard-monitor-scale-tabs.md)

[HybridConnectionTab]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionManageConn.png 
