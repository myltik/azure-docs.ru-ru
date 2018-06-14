---
title: Использование Azure AD Connect Health для синхронизации | Документация Майкрософт
description: На этой странице Azure AD Connect Health описывается отслеживание синхронизации Azure AD Connect.
services: active-directory
documentationcenter: ''
author: karavar
manager: mtillman
ms.assetid: 1dfbeaba-bda2-4f68-ac89-1dbfaf5b4015
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/18/2017
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cff8be88e23d57545a9926df366289c6ba264886
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2018
ms.locfileid: "30229954"
---
# <a name="monitor-azure-ad-connect-sync-with-azure-ad-connect-health"></a>Мониторинг синхронизации Azure AD Connect с помощью Azure AD Connect Health
Приведенная ниже документация относится к мониторингу синхронизации Azure AD Connect с помощью Azure AD Connect Health.  Сведения о мониторинге AD FS с помощью Azure AD Connect Health см. в [этой статье](active-directory-aadconnect-health-adfs.md). Кроме того, сведения о мониторинге доменных служб Active Directory с помощью Azure AD Connect Health можно найти [здесь](active-directory-aadconnect-health-adds.md).

![Azure AD Connect Health для синхронизации](./media/active-directory-aadconnect-health-sync/sync-blade.png)

## <a name="alerts-for-azure-ad-connect-health-for-sync"></a>Оповещения Azure AD Connect Health для синхронизации
Раздел оповещений Azure AD Connect Health для синхронизации содержит список активных оповещений. Каждое оповещение содержит соответствующую информацию, действия по устранению и ссылки на связанную документацию. Если выбрать активное или разрешенное оповещение, появится новая колонка с дополнительной информацией, действиями, которые можно предпринять для устранения причин оповещения, и ссылками на дополнительную документацию. Можно также просмотреть данные журнала об оповещениях, которые были разрешены в прошлом.

Если выбрать оповещение, отобразится дополнительная информация, действия, которые можно предпринять для устранения причин оповещения, и ссылки на дополнительную документацию.

![Ошибка синхронизации Azure AD Connect](./media/active-directory-aadconnect-health-sync/alert.png)

### <a name="limited-evaluation-of-alerts"></a>Ограниченное ознакомление с оповещениями
Если Azure AD Connect не использует конфигурацию по умолчанию (например, для фильтрации атрибутов выбрана пользовательская конфигурация), агент Azure AD Connect Health не будет передавать события ошибок, связанные с Azure AD Connect.

Это не позволяет службе в полной мере изучать оповещения. На портале Azure в разделе службы появится соответствующее сообщение.

![Azure AD Connect Health для синхронизации](./media/active-directory-aadconnect-health-sync/banner.png)

Чтобы исправить это, в меню "Параметры" разрешите агенту Azure AD Connect Health передавать все журналы ошибок.

![Azure AD Connect Health для синхронизации](./media/active-directory-aadconnect-health-sync/banner2.png)

## <a name="sync-insight"></a>Sync Insight
Часто администраторы хотят знать, сколько времени требуется для синхронизации изменений в Azure AD, а также количество выполненных изменений. Этот компонент предоставляет удобный способ визуализации этих сведений с помощью следующих диаграмм:   

* задержка операций синхронизации;
* тренд, отображающий изменения объектов.

### <a name="sync-latency"></a>Задержка синхронизации
Этот компонент отвечает за графическое отображение тренда задержек операций синхронизации (импорт, экспорт и т. д.) для соединителей.  Он позволяет не только быстро оценить задержки операций (например, при наличии большого количества происходящих изменений), но также определить аномальные задержки, требующие дополнительного изучения.

![Задержка синхронизации](./media/active-directory-aadconnect-health-sync/synclatency02.png)

По умолчанию для соединителя Azure AD отображаются только задержки операции экспорта.  Чтобы просмотреть дополнительные операции в этом соединителе или операции в других соединителях, щелкните диаграмму правой кнопкой мыши, выберите "Изменить диаграмму" или нажмите кнопку "Изменение диаграммы задержки" и выберите определенную операцию или соединитель.

### <a name="sync-object-changes"></a>Изменения объектов синхронизации
Этот компонент отвечает за графическое отображение тренда количества изменений, которые вычисляются и экспортируются в Azure AD.  Сейчас сбор такой информации из журналов синхронизации затруднен.  Схема — это не только простое средство мониторинга количества изменений, происходящих в вашей среде, она также предоставляет визуальные сведения о происходящих сбоях.

![Задержка синхронизации](./media/active-directory-aadconnect-health-sync/syncobjectchanges02.png)

## <a name="object-level-synchronization-error-report-preview"></a>Отчет об ошибках синхронизации на уровне объектов (предварительная версия)
Этот компонент предоставляет отчет об ошибках синхронизации, которые могут возникать при синхронизации данных удостоверений между Windows Server AD и Azure AD с помощью Azure AD Connect.

* В отчете содержатся ошибки, записанные клиентом синхронизации (Azure AD Connect версии 1.1.281.0 или выше).
* В отчете приводятся ошибки, произошедшие в течение последней операции синхронизации в модуле синхронизации (операция "Экспорт" в соединителе Azure AD).
* Агент Azure AD Connect Health для синхронизации должен иметь исходящее подключение к необходимым конечным точкам. Таким образом, отчет будет содержать актуальные данные.
* Отчет **обновляется каждые 30 минут** на основе данных, полученных от агента Azure AD Connect Health для синхронизации. Этот отчет отличается следующими основными преимуществами:

  * классификация ошибок;
  * вывод списка объектов с ошибками по категориям;
  * хранение данных об ошибках в одном месте;
  * параллельное сравнение объектов с ошибкой, возникшей в результате конфликта;
  * скачивание отчета об ошибках в формате CVS (ожидается в ближайшее время).

### <a name="categorization-of-errors"></a>Классификация ошибок
Имеющиеся ошибки синхронизации группируются в отчете по следующим категориям.

| Категория | ОПИСАНИЕ |
| --- | --- |
| Повторяющийся атрибут |Ошибки при попытках Azure AD Connect создать или обновить объекты с повторяющимися значениями одного или нескольких атрибутов в Azure AD, которые должны быть уникальными в клиенте, такие как proxyAddresses, UserPrincipalName. |
| Несоответствие данных |Ошибки синхронизации, возникшие в результате сбоя мягкого сопоставления объектов. |
| Сбой проверки данных |Ошибки, возникшие из-за недопустимых данных, таких как неподдерживаемые символы в важных атрибутах (например, UserPrincipalName), ошибки формата, не проходящие проверку перед записью в Azure AD. |
| Смена федеративного домена | Ошибки, которые возникают, когда в учетных записях используются разные федеративные домены. |
| Большой атрибут |Ошибки, возникающие, если размер, длина и количество атрибутов превышают установленный предел. |
| Другие |Все другие ошибки, не входящие в категории выше. На основе отзывов эти категории будут разделены на подкатегории. |

![Сводка по отчету об ошибках синхронизации](./media/active-directory-aadconnect-health-sync/errorreport01.png)
![Категории отчета об ошибках синхронизации](./media/active-directory-aadconnect-health-sync/SyncErrorByTypes.PNG)

### <a name="list-of-objects-with-error-per-category"></a>Вывод списка объектов с ошибками по категориям
Перейдя к каждой категории ошибок, вы увидите список объектов с ошибками, которые под нее подпадают.
![Список объектов в отчете об ошибках синхронизации](./media/active-directory-aadconnect-health-sync/errorreport03.png)

### <a name="error-details"></a>Сведения об ошибке
В подробном представлении каждой ошибки содержатся следующие данные:

* идентификаторы затронутого *объекта AD*;
* идентификаторы затронутого *объекта Azure AD* (в соответствующем случае);
* описание ошибки и способы ее устранения.
* Связанные статьи

![Сведения об ошибках в отчете об ошибках синхронизации](./media/active-directory-aadconnect-health-sync/errorreport04.png)

### <a name="download-the-error-report-as-csv"></a>Скачивание отчета об ошибках в формате CVS
Вы можете скачать CSV-файл со сведениями обо всех ошибках с помощью кнопки "Экспорт".

## <a name="related-links"></a>Связанные ссылки
* [Устранение ошибок синхронизации](../connect/active-directory-aadconnect-troubleshoot-sync-errors.md)
* [Синхронизация удостоверений и устойчивость повторяющихся атрибутов](../connect/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md)
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Установка агента Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md)
* [Операции Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
* [Использование Azure AD Connect Health с AD FS](active-directory-aadconnect-health-adfs.md)
* [Using Azure AD Connect Health with AD DS (Использование Azure AD Connect Health с AD DS)](active-directory-aadconnect-health-adds.md)
* [Часто задаваемые вопросы об Azure AD Connect Health](active-directory-aadconnect-health-faq.md)
* [Azure AD Connect Health: история версий](active-directory-aadconnect-health-version-history.md)
