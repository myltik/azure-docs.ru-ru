<properties urlDisplayName="BizTalk Services: Service state chart" pageTitle="Службы BizTalk: Диаграмма состояния службы | Azure" metaKeywords="" description="" metaCanonical="" services="biztalk-services" documentationCenter="" title="BizTalk Services: Service state chart" authors="mandia" solutions="integration" manager="dwrede" editor="cgronlun" />

<tags ms.service="biztalk-services" ms.workload="integration" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/10/2014" ms.author="mandia" />



# Службы BizTalk: диаграмма состояния службы
В зависимости от текущего состояния службы BizTalk существуют операции, которые можно или нельзя выполнять со службой BizTalk.

Например, можно подготовить новую службу BizTalk на портале управления Azure. После успешного завершения этого процесса служба BizTalk переводится в активное состояние. В активном состоянии можно остановить службу BizTalk. Если остановка произведена успешно, служба BizTalk переходит в состоянии "Остановлена". Если остановка происходит со сбоем, служба BizTalk переходит в состоянии "Сбой остановки". В состоянии сбоя остановки можно перезапустить службу BizTalk. При попытке выполнения неразрешенной операции, например возобновления службы BizTalk, произойдет следующая ошибка.

**Операция не разрешена**

Действия по подготовке службы BizTalk приводятся в разделе [Службы BizTalk:  подготовка с использованием портала управления Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280).

В следующих таблицах перечислены операции, которые могут быть выполнены, когда служба BizTalk находится в том или ином состоянии. Установленный флажок означает, что операция может быть выполнена в этом состоянии. Пустая запись означает, что операция не может быть выполнена в этом состоянии.

#### Операции: запустить, остановить, перезапустить, приостановить, продолжить и удалить
<table border="1">
<tr>
        <th colspan="15">Операция</th>
</tr>

<tr>
        <th rowspan="18">Состояние службы BizTalk</th>
</tr>
<tr bgcolor="FAF9F9">
        <th> </th>
        <th>Начало</th>
        <th>Остановить</th>
        <th>Перезагрузить</th>
        <th>Приостановить</th>
        <th>Продолжить</th>
        <th>Удалить</th>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Активно</b></td>
<td> </td>
<td><center>x</center></td>
<td><center>x</center></td>
<td><center>x</center></td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Отключено</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Приостановлено</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>x</center></td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Остановлена</b></td>
<td><center>x</center></td>
<td> </td>
<td><center>x</center></td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Обновление службы завершено с ошибкой</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Сбой отключения</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Сбой включения</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Сбой запуска<br/>
Сбой остановки<br/>
Сбой перезапуска</b></td>
<td><center>x</center></td>
<td><center>x</center></td>
<td><center>x</center></td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Сбой приостановки<br/>
Сбой продолжения</b></td>
<td> </td>
<td> </td>
<td> </td>
<td><center>x</center></td>
<td><center>x</center></td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Сбой создания<br/>
Сбой восстановления <br/></b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Сбой обновления конфигурации</b></td>
<td> </td>
<td> </td>
<td><center>x</center></td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Сбой масштабирования</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
</table>
<br/>

####Операции масштабирования, обновления конфигурации и архивации
<table border="1">
<tr>
        <th colspan="15">Операция</th>
</tr>

<tr>
        <th rowspan="18">Состояние службы BizTalk</th>
</tr>
<tr bgcolor="FAF9F9">
        <th> </th>
        <th>Масштаб</th>
        <th>Конфигурация обновления</th>
        <th>Архивация</th>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Активно</b></td>
<td><center>x</center></td>
<td><center>x</center></td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Отключено</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Приостановлено</b></td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Остановлена</b></td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Обновление службы завершено с ошибкой</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Сбой отключения</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Сбой включения</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Сбой запуска<br/>
Сбой остановки<br/>
Сбой перезапуска</b></td>
<td> </td>
<td><center>x</center></td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Сбой приостановки<br/>
Сбой продолжения</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Сбой создания<br/>
Сбой восстановления<br/></b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Сбой обновления конфигурации</b></td>
<td> </td>
<td><center>x</center></td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Сбой масштабирования</b></td>
<td><center>x</center></td>
<td> </td>
<td> </td>
</tr>
</table>

## См. также
- [Службы BizTalk: подготовка с использованием портала управления Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [Службы BizTalk: вкладки "Панель мониторинга", "Монитор" и "Масштаб"](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [Службы BizTalk: диаграмма выпусков Developer, Basic, Standard и Premium](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [Службы BizTalk: архивация и восстановление](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [Службы BizTalk: регулирование](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
- [Службы BizTalk: имя и ключ издателя](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>
- [Как приступить к работе с пакетом SDK для служб BizTalk Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)


