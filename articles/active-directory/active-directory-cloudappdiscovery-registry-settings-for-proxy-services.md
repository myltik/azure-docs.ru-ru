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
ms.date: 10/10/2016
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 6adb1dd25c24b18b834dd921c2586ef29d56dc81
ms.openlocfilehash: 7f2a3c4bf8fda9dd235986be8b0ceaa8eb8313e0


---
# <a name="cloud-app-discovery-registry-settings-for-proxy-services"></a>Параметры реестра Cloud App Discovery для прокси-служб
По умолчанию агент Cloud App Discovery настроен на использование только портов 80 и 443. Если вы планируете установить Cloud App Discovery в среде с прокси-сервером, который использует другой порт (не 80 или 443), необходимо настроить агенты на использование этого порта. Конфигурация основана на разделе реестра.

В этой статье приведены шаги, которые необходимо выполнить, чтобы задать требуемый порт на компьютере с запущенным агентом Cloud App Discovery.

**Чтобы изменить порт, используемый на компьютере с запущенным агентом Cloud App Discovery, выполните следующие действия:**

1. Откройте редактор реестра. <br> ![Выполнить](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy01.png)
2. Перейдите в следующий раздел реестра или создайте его:  <br> **HKLM_LOCAL_MACHINE\Software\Microsoft\Cloud App Discovery\Endpoint** 
3. Создайте новый **мультистроковый** параметр **Ports**. ![Создать](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy02.png)
4. Откройте диалоговое окно **Редактирование мультистроки** , дважды щелкнув значение Ports.
5. В текстовом поле «Данные параметра» введите следующие значения и добавьте все настраиваемые порты, которые используются в вашей организации:  <br><br>
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

**Дополнительные ресурсы**

* [Как обнаруживать несанкционированные облачные приложения, которые используются в моей организации](active-directory-cloudappdiscovery-whatis.md) 




<!--HONumber=Dec16_HO4-->


