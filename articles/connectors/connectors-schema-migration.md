---
title: Перенос приложений логики в версию схемы 2015-08-01-preview | Документация Майкрософт
description: Вы можете легко перенести приложения логики в последнюю версию схемы. Просто следуйте приведенным здесь инструкциям.
services: logic-apps
documentationcenter: ''
author: MSFTMAN
manager: erikre
editor: ''
tags: connectors
ms.assetid: 3e177e49-fd69-43e9-9b9b-218abb250c31
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/23/2016
ms.author: deonhe
ms.openlocfilehash: a5a73a9f124e5339b61dbc49021444a208a471f0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
ms.locfileid: "22707130"
---
# <a name="how-to-migrate-logic-apps-to-schema-version-2015-08-01-preview"></a>Перенос приложений логики в версию схемы 2015-08-01-preview
Чтобы переместить существующие приложения логики в новую схему, выполните следующие действия:  

1. Откройте приложение логики на портале Azure.  
2. Нажмите кнопку "Обновить схему".
   
   ![Значок API][step1]   
   На странице "Обновление схемы" отобразятся ссылки на документ, который содержит сведения об улучшениях в новой схеме. ![Значок API][step2]

> [!NOTE]
> Когда вы нажмете кнопку **Обновить схему**, автоматически запустится процесс миграции и отобразится вывод кода. Вы можете использовать эти возможности для обновления своего определения. Но обязательно придерживайтесь рекомендуемых методов программирования, описанных в разделе с **рекомендациями** ниже.
> 
> 

## <a name="best-practices-when-migrating-your-logic-apps-to-the-latest-schema-version"></a>Рекомендации по переносу приложений логики в последнюю версию схемы
* Скопируйте перенесенный сценарий в новое приложение логики. Не перезаписывайте старое приложение, пока не завершите тестирование и не убедитесь, что перенесенное приложение работает правильно.
* Протестируйте приложение логики, **прежде** чем помещать его в рабочую среду.
* Завершив перенос, приступайте к обновлению приложений логики, чтобы использовать [управляемые интерфейсы API](apis-list.md) там, где это возможно. Например, вы можете теперь использовать Dropbox версии 2 там, где использовали Dropbox версии 1.

## <a name="whats-next"></a>Что дальше?
* [Новая версия схемы 2015-08-01-preview](../logic-apps/logic-apps-schema-2015-08-01.md)

<!--Icon references-->
[step1]: ./media/connectors-schema-migration/migrateschema1.png
[step2]: ./media/connectors-schema-migration/migrateschema2.png






