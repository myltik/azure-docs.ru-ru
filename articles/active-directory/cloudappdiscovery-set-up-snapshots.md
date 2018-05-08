---
title: Создание отчетов о снимках Cloud App Discovery в Azure Active Directory | Документы Майкрософт
description: Содержит сведения о поиске приложений и управлении ими с помощью Cloud App Discovery, а также преимуществах и принципах работы Cloud App Discovery.
services: active-directory
keywords: cloud app discovery, управление приложениями
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 09/22/2017
ms.author: curtand
ms.reviewer: nigu
ms.custom: it-pro
ms.openlocfilehash: ad4591223c72893a4488f5515d8ceb83e0d7f8cf
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
---
# <a name="create-cloud-app-discovery-snapshot-reports"></a>Создание отчетов о снимках для Cloud App Discovery

Перед настройкой автоматического сборщика журналируемых данных отправьте журнал вручную и позвольте Cloud App Security проанализировать его. Если у вас пока нет журнала и вы хотите просмотреть, как именно должен выглядеть подобный журнал, воспользуйтесь описанной ниже процедурой, чтобы скачать пример файла журнала.

## <a name="to-create-a-snapshot-report"></a>Создание отчета о снимке

1. Соберите файлы журналов из брандмауэра и с прокси-сервера, через который пользователи в вашей организации осуществляют доступ в Интернет. Сбор журналов производите во время передачи пиковых объемов трафика, характеризующего активность пользователей в вашей организации.
2. В [строке меню Cloud App Security](https://portal.cloudappsecurity.com) выберите **Обнаружение**, а затем **Создать отчет о снимке**.
  
  ![Создание отчета о снимке](./media/cloudappdiscovery-set-up-snapshots/create-snapshot-command.png)
3. Введите **Имя отчета** и **Описание**.
    
  ![Новый отчет о снимке](./media/cloudappdiscovery-set-up-snapshots/create-snapshot-form.png)
4. Выберите **Источник данных**, откуда требуется отправить файлы журнала.
5. Убедитесь, что формат журнала соответствует скачанному примеру. Выберите **Просмотреть и проверить**, а затем **Скачать журнал-пример**. После этого сравните журнал с примером и убедитесь в их соответствии.
  
  ![Проверка формата журнала](./media/cloudappdiscovery-set-up-snapshots/create-snapshot-verify.png)
  >  [!NOTE]
  > Формат примера FTP поддерживается для моментальных снимков и автоматической отправки, а системный журнал — только для автоматической отправки. При скачивании примера журнала скачивается пример журнала FTP.
6. **Выберите журналы трафика**, которые нужно отправить. Одновременно можно отправить до 20 файлов. Также поддерживаются сжатые и ZIP-файлы.
  
7. Нажмите кнопку **Создать**. После отправки файлов журнала их анализ может занять некоторое время. После завершения обработки файлов журналов вы получите от Cloud App Discovery уведомление об этом по электронной почте.

8. Выберите **Управление отчетами о снимках** и выберите отчет о снимке.
  
  ![Управление отчетами о снимке](./media/cloudappdiscovery-set-up-snapshots/create-snapshot-manage.png)

## <a name="next-steps"></a>Дополнительная информация

* [Приступая к работе с Cloud App Discovery в Azure AD](cloudappdiscovery-get-started.md)
* [Настройка автоматической отправки для непрерывных отчетов](https://docs.microsoft.com/cloud-app-security/discovery-docker)
* [Использование настраиваемого средства синтаксического анализа журналов](https://docs.microsoft.com/cloud-app-security/custom-log-parser)
