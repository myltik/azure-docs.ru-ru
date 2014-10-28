<properties linkid="manage-services-biztalk-services-editions-chart" urlDisplayName="Editions chart" pageTitle="Learn about features in BizTalk Services editions | Azure" metaKeywords="BizTalk Services, get started, Azure, editions" description="Compare the capabilities of the BizTalk Services editions: Free, Developer, Basic, Standard, and Premium." metaCanonical="" services="biztalk-services" documentationCenter="" title=" Basic" authors="mandia" solutions="" manager="dwrede" editor="cgronlun" />

<tags ms.service="biztalk-services" ms.workload="integration" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/10/2014" ms.author="mandia"></tags>

# Службы BizTalk: диаграмма выпусков

Доступны следующие выпуски служб BizTalk Azure: Free, Developer, Basic, Standard и Premium.

**Free (предварительный просмотр)**. Предполагает возможность создания гибридных подключений и управления ими. Гибридное подключение — это простой способ соединения веб-сайта Azure с такими локальными системами, как сервер SQL Server.

**Developer**. Включает создание гибридных подключений, обработку сообщений EAI и EDI с помощью простого портала управления для торговых партнеров, поддержку общих схем EDI и обширные возможности обработки EDI через X12 и AS2. Допускает создание типовых служб подключения по сценариям EAI в облаке с протоколами HTTP/S, REST, FTP, WCF и SFTP для чтения и записи сообщений. Использует возможности подключения к локальным бизнес-системам с адаптерами для SAP, Oracle eBusiness, Oracle DB, Siebel и SQL Server. Все это в ориентированной на разработчиков среде с использованием средств Visual Studio, упрощающих разработку и развертывание. Ограничено только для разработки и тестирования без соглашения об уровне обслуживания.

**Basic**. Включает большинство возможностей выпуска Developer с расширением гибридных подключений, мостов EAI, соглашений EDI и подключений пакета BizTalk Adapter Pack. Отличается широкой доступностью и возможностью масштабирования с соглашением об уровне обслуживания.

**Standard**. Включает большинство возможностей выпуска Basic с расширением гибридных подключений, мостов EAI, соглашений EDI и подключений пакета BizTalk Adapter Pack. Отличается широкой доступностью и возможностью масштабирования с соглашением об уровне обслуживания.

**Premium**. Включает большинство возможностей выпуска Standard с расширением гибридных подключений, мостов EAI, соглашений EDI и подключений пакета BizTalk Adapter Pack. Также включает архивирование, широкую доступность и возможность масштабирования с соглашением об уровне обслуживания.

Различия показаны в следующей таблице:

<table>
<colgroup>
<col width="16%" />
<col width="16%" />
<col width="16%" />
<col width="16%" />
<col width="16%" />
<col width="16%" />
</colgroup>
<thead>
<tr class="header">
<th align="left"></th>
<th align="left">Free (предварительный просмотр)</th>
<th align="left">Developer</th>
<th align="left">Basic</th>
<th align="left">Standard</th>
<th align="left">Premium</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><strong>Начальная цена</strong></td>
<td align="left">См. <a href="http://go.microsoft.com/fwlink/p/?LinkID=304011">Сведения о ценах на службы BizTalk Azure</a>.</td>
<td align="left">См. <a href="http://go.microsoft.com/fwlink/p/?LinkID=304011">Сведения о ценах на службы BizTalk Azure</a>.</td>
<td align="left">См. <a href="http://go.microsoft.com/fwlink/p/?LinkID=304011">Сведения о ценах на службы BizTalk Azure</a>.</td>
<td align="left">См. <a href="http://go.microsoft.com/fwlink/p/?LinkID=304011">Сведения о ценах на службы BizTalk Azure</a>.</td>
<td align="left">См. <a href="http://go.microsoft.com/fwlink/p/?LinkID=304011">Сведения о ценах на службы BizTalk Azure</a>.</td>
</tr>
<tr class="even">
<td align="left"><strong>Минимальная конфигурация по умолчанию</strong></td>
<td align="left">Один модуль Free</td>
<td align="left">1 Модуль Developer</td>
<td align="left">1 Модуль Basic</td>
<td align="left">1 Модуль Standard</td>
<td align="left">1 Модуль Premium</td>
</tr>
<tr class="odd">
<td align="left"><strong>Масштаб</strong></td>
<td align="left">Без масштабирования</td>
<td align="left">Без масштабирования</td>
<td align="left">Да, с шагом в 1 модуль Basic</td>
<td align="left">Да, с шагом в 1 модуль Standard</td>
<td align="left">Да, с шагом в 1 модуль Premium</td>
</tr>
<tr class="even">
<td align="left"><strong>Максимально разрешенный масштаб</strong></td>
<td align="left">Без масштабирования</td>
<td align="left">Без масштабирования</td>
<td align="left">До 8 единиц</td>
<td align="left">До 8 единиц</td>
<td align="left">До 8 единиц</td>
</tr>
<tr class="odd">
<td align="left"><strong>Мостов EAI за единицу</strong></td>
<td align="left">Не включено</td>
<td align="left">25</td>
<td align="left">25</td>
<td align="left">125</td>
<td align="left">500</td>
</tr>
<tr class="even">
<td align="left"><strong>EDI, AS2</strong><br /><br /> Включает соглашения об управлении торговыми партнерами.</td>
<td align="left">Не включено</td>
<td align="left">Включено. 10 соглашений на единицу.</td>
<td align="left">Включено. 50 соглашений на единицу.</td>
<td align="left">Включено. 250 соглашений на единицу.</td>
<td align="left">Включено. 1000 соглашений на единицу.</td>
</tr>
<tr class="odd">
<td align="left"><strong>Количество гибридных подключений на единицу</strong></td>
<td align="left">5</td>
<td align="left">5</td>
<td align="left">10</td>
<td align="left">50</td>
<td align="left">100</td>
</tr>
<tr class="even">
<td align="left"><strong>Объем передачи данных гибридных подключений (ГБ) на единицу</strong></td>
<td align="left">5</td>
<td align="left">5</td>
<td align="left">50</td>
<td align="left">250</td>
<td align="left">500</td>
</tr>
<tr class="odd">
<td align="left"><strong>Подключения службы адаптера BizTalk к локальным бизнес-системам</strong></td>
<td align="left">Не включено</td>
<td align="left">1 подключение</td>
<td align="left">2 подключений</td>
<td align="left">5 подключений</td>
<td align="left">25 подключений</td>
</tr>
<tr class="even">
<td align="left"><strong>Поддерживаемые протоколы и системы:</strong>
<ul>
<li>HTTP</li>
<li>HTTPS</li>
<li>FTP</li>
<li>SFTP</li>
<li>WCF</li>
<li>Шина обслуживания (SB)</li>
<li>BLOB-объект Azure</li>
<li>Интерфейсы API REST</li>
</ul></td>
<td align="left">Не включено</td>
<td align="left">Включено</td>
<td align="left">Включено</td>
<td align="left">Включено</td>
<td align="left">Включено</td>
</tr>
<tr class="odd">
<td align="left"><strong>Высокая доступность</strong><br /><br /> Для соглашения об уровне обслуживания (SLA) см. <a href="http://go.microsoft.com/fwlink/p/?LinkID=304011">Сведения о ценах на службы BizTalk Azure</a>.</td>
<td align="left">Не включено</td>
<td align="left">Не включено</td>
<td align="left">Включено</td>
<td align="left">Включено</td>
<td align="left">Включено</td>
</tr>
<tr class="even">
<td align="left"><strong>Архивация и восстановление</strong></td>
<td align="left">Не включено</td>
<td align="left">Включено</td>
<td align="left">Включено</td>
<td align="left">Включено</td>
<td align="left">Включено</td>
</tr>
<tr class="odd">
<td align="left"><strong>Отслеживание</strong></td>
<td align="left">Не включено</td>
<td align="left">Включено</td>
<td align="left">Включено</td>
<td align="left">Включено</td>
<td align="left">Включено</td>
</tr>
<tr class="even">
<td align="left"><strong>Архивация</strong><br /><br /> Включает невозможность отказа на получение (NRR) и возможность загрузки отслеживаемых сообщений.</td>
<td align="left">Не включено</td>
<td align="left">Включено</td>
<td align="left">Не включено</td>
<td align="left">Не включено</td>
<td align="left">Включено</td>
</tr>
<tr class="odd">
<td align="left"><strong>Использование пользовательского кода</strong></td>
<td align="left">Не включено</td>
<td align="left">Включено</td>
<td align="left">Включено</td>
<td align="left">Включено</td>
<td align="left">Включено</td>
</tr>
<tr class="even">
<td align="left"><strong>Использование преобразований, включая настраиваемые XSLT</strong></td>
<td align="left">Не включено</td>
<td align="left">Включено</td>
<td align="left">Включено</td>
<td align="left">Включено</td>
<td align="left">Включено</td>
</tr>
</tbody>
</table>

**Примечание.**
 Для обеспечения устойчивости к сбоям оборудования высокая доступность предполагает наличие нескольких виртуальных машин в одной единице BizTalk.

## Часто задаваемые вопросы

#### Что такое "единица BizTalk"?

"Единица" — это атомарный уровень развертывания служб BizTalk Azure. Выпуски поставляются с модулями, которые различаются вычислительной мощностью и объемом памяти. Например, единица в выпуске Basic имеет больше возможностей, чем в Developer, единица в выпуске Standard — больше возможностей, чем в Basic, и т. д. Масштабирование службы BizTalk выполняется относительно единиц.

#### В чем разница между службами BizTalk и виртуальной машиной BizTalk Azure?

Службы BizTalk предоставляют подлинную архитектуру "платформа как услуга" (PaaS) для создания решений интеграции в облаке. Модель PaaS позволяет вам полностью сосредоточиться на логике приложения и оставить корпорации Майкрософт все аспекты управления инфраструктурой, в том числе:

-   Вам не требуется управлять виртуальными машинами или настраивать их.
-   Майкрософт обеспечивает доступность.
-   Управлять масштабом легко — просто запросите необходимую нагрузку на портале Azure.

BizTalk Server на виртуальных машинах Azure предоставляет архитектуру "инфраструктура как услуга" (IaaS). Вы создаете виртуальные машины и настраиваете их точно так же, как локальную среду, упрощая запуск существующих приложений в облаке без изменения кода. При использовании модели IaaS вы сами отвечаете за настройку виртуальных машин, управление виртуальными машинами (например, за установку исправлений ПО и ОС) и обеспечение высокой доступности приложения.

Если вы хотите создавать новые решения интеграции, требующие минимальных издержек на управление инфраструктурой, используйте службы BizTalk. Если вы хотите быстро перенести существующие решения BizTalk или вам нужна среда по запросу для разработки и тестирования приложений BizTalk Server, используйте BizTalk Server на виртуальной машине Azure.

#### В чем разница между службой адаптера BizTalk и гибридными подключениями?

Служба адаптера BizTalk используется службой BizTalk Azure, что необходимо для подключения к локальной бизнес-системе с помощью пакета BizTalk Adapter Pack. Гибридное подключение — это простой и удобный способ запуска приложений веб-сайтов и мобильных служб на локальном ресурсе.

#### Почему, когда я создаю соглашение в службах BizTalk, число мостов увеличивается на два, а не на один?

Каждое соглашение состоит из двух разных мостов: моста стороны отправки и моста стороны приема.

#### Что происходит при достижении ограничения квоты для числа мостов или соглашений?

Вы не сможете развернуть новые мосты или создать новые соглашения. Для дополнительных развертываний вам потребуется масштабировать среду до большего числа единиц службы BizTalk или выполнить обновление до выпуска более высокого уровня.

#### Как перейти от одного уровня служб BizTalk к другому?

Используйте резервное копирование и восстановление данных для перехода с одного уровня на другой. Поддерживаются только некоторые пути миграции. Более подробную информацию о поддерживаемых путях миграции см. в разделе [Службы BizTalk: архивация и восстановление][Службы BizTalk: архивация и восстановление].

#### Включена ли сюда служба адаптера BizTalk? Как получить это программное обеспечение?

Да, служба адаптера BizTalk вместе с пакетом BizTalk Adapter Pack включена в пакет SDK для служб BizTalk Azure [загрузить][загрузить].

## Далее

Сведения о подготовке служб BizTalk Windows Azure на портале управления Windows Azure см. в разделе [Службы BizTalk: подготовка с использованием портала управления Azure][Службы BizTalk: подготовка с использованием портала управления Azure]. Чтобы приступить к созданию приложений, перейдите по ссылке [Службы BizTalk Azure][Службы BizTalk Azure].

## См. также

-   [Службы BizTalk: подготовка с использованием портала управления Azure][Службы BizTalk: подготовка с использованием портала управления Azure]
-   [Службы BizTalk: диаграмма состояния подготовки][Службы BizTalk: диаграмма состояния подготовки]
-   [Службы BizTalk: вкладки "Панель мониторинга", "Монитор" и "Масштаб"][Службы BizTalk: вкладки "Панель мониторинга", "Монитор" и "Масштаб"]
-   [Службы BizTalk: архивация и восстановление][Службы BizTalk: архивация и восстановление]
-   [Службы BizTalk: регулирование][Службы BizTalk: регулирование]
-   [Службы BizTalk: имя и ключ издателя][Службы BizTalk: имя и ключ издателя]
-   [Как приступить к работе с пакетом SDK для служб BizTalk Azure][Как приступить к работе с пакетом SDK для служб BizTalk Azure]

  [Сведения о ценах на службы BizTalk Azure]: http://go.microsoft.com/fwlink/p/?LinkID=304011
  [Службы BizTalk: архивация и восстановление]: http://go.microsoft.com/fwlink/p/?LinkID=329873
  [загрузить]: http://www.microsoft.com/download/details.aspx?id=39087
  [Службы BizTalk: подготовка с использованием портала управления Azure]: http://go.microsoft.com/fwlink/p/?LinkID=302280
  [Службы BizTalk Azure]: http://go.microsoft.com/fwlink/p/?LinkID=235197
  [Службы BizTalk: диаграмма состояния подготовки]: http://go.microsoft.com/fwlink/p/?LinkID=329870
  [Службы BizTalk: вкладки "Панель мониторинга", "Монитор" и "Масштаб"]: http://go.microsoft.com/fwlink/p/?LinkID=302281
  [Службы BizTalk: регулирование]: http://go.microsoft.com/fwlink/p/?LinkID=302282
  [Службы BizTalk: имя и ключ издателя]: http://go.microsoft.com/fwlink/p/?LinkID=303941
  [Как приступить к работе с пакетом SDK для служб BizTalk Azure]: http://go.microsoft.com/fwlink/p/?LinkID=302335
