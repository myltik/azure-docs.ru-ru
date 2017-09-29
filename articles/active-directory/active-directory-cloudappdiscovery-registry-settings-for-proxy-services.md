---
title: "Параметры реестра Cloud App Discovery для прокси-служб | Документация Майкрософт"
description: "В этой статье приведены шаги, которые необходимо выполнить, чтобы задать требуемый порт на компьютере с запущенным агентом Cloud App Discovery."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 8d78e925-e331-40ba-904a-e4ef14260cac
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: markvi
ms.reviewer: nigu
ms.translationtype: HT
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: b8584809b76d6be12a6f489f0bb819081d1803d2
ms.contentlocale: ru-ru
ms.lasthandoff: 09/25/2017

---
# <a name="cloud-app-discovery-registry-settings-for-proxy-services"></a>Параметры реестра Cloud App Discovery для прокси-служб
В этом разделе описаны действия для установки требуемого порта на компьютере, на котором запущен агент Cloud App Discovery. По умолчанию агент Cloud App Discovery настроен на использование только портов 80 и 443. Если вы планируете установить Cloud App Discovery в среде с прокси-сервером, который использует другой порт (не 80 или 443), необходимо настроить агенты на использование этого порта. Конфигурация основана на разделе реестра.

> [!TIP] 
> Познакомьтесь с улучшениями Cloud App Discovery (теперь без агента) в Azure Active Directory, которые были достигнуты за счет [интеграции с Microsoft Cloud App Security](https://portal.cloudappsecurity.com).

## <a name="modify-the-port-used-by-the-computer-running-the-cloud-app-discovery-agent"></a>Изменение порта на компьютере, на котором запущен агент Cloud App Discovery

1. Откройте редактор реестра.
  ![Run](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy01.png)
2. Перейдите в следующий раздел реестра или создайте его: **HKLM_LOCAL_MACHINE\Software\Microsoft\Cloud App Discovery\Endpoint**
3. Создайте новый **мультистроковый** параметр **Ports**. 
  ![Создать](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy02.png)
4. Откройте диалоговое окно **Редактирование мультистроки**, дважды щелкнув значение параметра **Ports**.
5. В поле **Данные параметра** введите следующие значения и добавьте все настраиваемые порты, которые используются в вашей организации: <br><br>
   **80** <br>
   **8080** <br>
   **8118** <br>
   **8888** <br>
   **81** <br>
   **12080** <br>
   **6999** <br>
   **30606** <br>
   **31595** <br>
   **4080** <br>
   **443** <br>
   **1110** <br><br>
   ![Редактирование мультистроки](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy03.png)
6. Нажмите кнопку **ОК**, чтобы закрыть диалоговое окно **Редактирование мультистроки**.

## <a name="next-steps"></a>Дальнейшие действия

* [Как обнаруживать несанкционированные облачные приложения, которые используются в моей организации](active-directory-cloudappdiscovery-whatis.md) 


