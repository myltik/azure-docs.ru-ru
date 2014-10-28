<properties linkid="manage-services-biztalk-state-chart" urlDisplayName="BizTalk Services: Service state chart" pageTitle="BizTalk Services: Service state chart | Azure" metaKeywords="" description="" metaCanonical="" services="biztalk-services" documentationCenter="" title="BizTalk Services: Service state chart" authors="mandia" solutions="integration" manager="dwrede" editor="cgronlun" />

<tags ms.service="biztalk-services" ms.workload="integration" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/10/2014" ms.author="mandia"></tags>

# Службы BizTalk: диаграмма состояния службы

В зависимости от текущего состояния службы BizTalk существуют операции, которые можно или нельзя выполнять со службой BizTalk.

Например, можно подготовить новую службу BizTalk на портале управления Azure. После успешного завершения этого процесса служба BizTalk переводится в активное состояние. В активном состоянии можно остановить службу BizTalk. Если остановка произведена успешно, служба BizTalk переходит в состоянии "Остановлена". Если остановка происходит со сбоем, служба BizTalk переходит в состоянии "Сбой остановки". В состоянии сбоя остановки можно перезапустить службу BizTalk. При попытке выполнения неразрешенной операции, например возобновления службы BizTalk, произойдет следующая ошибка:

**Операция не разрешена**

Действия по подготовке службы BizTalk приводятся в разделе [Службы BizTalk: подготовка с использованием портала управления Windows Azure][Службы BizTalk: подготовка с использованием портала управления Windows Azure].

В следующих таблицах перечислены операции, которые могут быть выполнены, когда служба BizTalk находится в том или ином состоянии. Установленный флажок означает, что операция может быть выполнена в этом состоянии. Пустая запись означает, что операция не может быть выполнена в этом состоянии.

#### Операции: запустить, остановить, перезапустить, приостановить, продолжить и удалить

<table border="1">
<tr>
<th colspan="15">
Операция

</th>
</tr>
<tr>
<th rowspan="18">
Состояние службы BizTalk

</th>
</tr>
<tr bgcolor="FAF9F9">
<th>
</th>
<th>
Начало

</th>
<th>
Остановить

</th>
<th>
Перезагрузить

</th>
<th>
Приостановить

</th>
<th>
Продолжить

</th>
<th>
Удалить

</th>
</tr>
<tr>
<td bgcolor="FAF9F9">
**Активно**

</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
<td>
<center>
x

</center>
</td>
<td>
<center>
x

</center>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
**Отключено**

</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
**Приостановлено**

</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
**Остановлена**

</td>
<td>
<center>
x

</center>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
**Обновление службы завершено с ошибкой**

</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
**Сбой отключения**

</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
**Сбой включения**

</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
**Сбой запуска
 Сбой остановки
 Сбой перезапуска**

</td>
<td>
<center>
x

</center>
</td>
<td>
<center>
x

</center>
</td>
<td>
<center>
x

</center>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
**Сбой приостановки
 Сбой продолжения**

</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
<td>
<center>
x

</center>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
**Сбой создания
 Сбой восстановления
**

</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
**Сбой обновления конфигурации**

</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
**Сбой масштабирования**

</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
</table>

#### Операции масштабирования, обновления конфигурации и архивации

<table border="1">
<tr>
<th colspan="15">
Операция

</th>
</tr>
<tr>
<th rowspan="18">
Состояние службы BizTalk

</th>
</tr>
<tr bgcolor="FAF9F9">
<th>
</th>
<th>
Масштаб

</th>
<th>
Конфигурация обновления

</th>
<th>
Архивация

</th>
</tr>
<tr>
<td bgcolor="FAF9F9">
**Активно**

</td>
<td>
<center>
x

</center>
</td>
<td>
<center>
x

</center>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
**Отключено**

</td>
<td>
</td>
<td>
</td>
<td>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
**Приостановлено**

</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
**Остановлена**

</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
**Обновление службы завершено с ошибкой**

</td>
<td>
</td>
<td>
</td>
<td>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
**Сбой отключения**

</td>
<td>
</td>
<td>
</td>
<td>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
**Сбой включения**

</td>
<td>
</td>
<td>
</td>
<td>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
**Сбой запуска
 Сбой остановки
 Сбой перезапуска**

</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
<td>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
**Сбой приостановки
 Сбой продолжения**

</td>
<td>
</td>
<td>
</td>
<td>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
**Сбой создания
 Сбой восстановления
**

</td>
<td>
</td>
<td>
</td>
<td>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
**Сбой обновления конфигурации**

</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
<td>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
**Сбой масштабирования**

</td>
<td>
<center>
x

</center>
</td>
<td>
</td>
<td>
</td>
</tr>
</table>
## См. также

-   [Службы BizTalk: подготовка с использованием портала управления Windows Azure][Службы BizTalk: подготовка с использованием портала управления Windows Azure]
-   [Службы BizTalk: вкладки "Панель мониторинга", "Монитор" и "Масштаб"][Службы BizTalk: вкладки "Панель мониторинга", "Монитор" и "Масштаб"]
-   [Службы BizTalk: диаграмма выпусков Developer, Basic, Standard и Premium][Службы BizTalk: диаграмма выпусков Developer, Basic, Standard и Premium]
-   [Службы BizTalk: архивация и восстановление][Службы BizTalk: архивация и восстановление]
-   [Службы BizTalk: регулирование][Службы BizTalk: регулирование]
-   [Службы BizTalk: имя и ключ издателя][Службы BizTalk: имя и ключ издателя]
-   [Как приступить к работе с пакетом SDK для служб BizTalk Azure][Как приступить к работе с пакетом SDK для служб BizTalk Azure]

  [Службы BizTalk: подготовка с использованием портала управления Windows Azure]: http://go.microsoft.com/fwlink/p/?LinkID=302280
  [Службы BizTalk: вкладки "Панель мониторинга", "Монитор" и "Масштаб"]: http://go.microsoft.com/fwlink/p/?LinkID=302281
  [Службы BizTalk: диаграмма выпусков Developer, Basic, Standard и Premium]: http://go.microsoft.com/fwlink/p/?LinkID=302279
  [Службы BizTalk: архивация и восстановление]: http://go.microsoft.com/fwlink/p/?LinkID=329873
  [Службы BizTalk: регулирование]: http://go.microsoft.com/fwlink/p/?LinkID=302282
  [Службы BizTalk: имя и ключ издателя]: http://go.microsoft.com/fwlink/p/?LinkID=303941
  [Как приступить к работе с пакетом SDK для служб BizTalk Azure]: http://go.microsoft.com/fwlink/p/?LinkID=302335
