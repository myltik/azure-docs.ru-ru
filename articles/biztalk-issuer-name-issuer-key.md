<properties linkid="manage-services-biztalk-services-issuer-name-issuer-key" urlDisplayName="Issuer name and issuer key" pageTitle="Issuer Name and Issuer Key in BizTalk Services | Azure" metaKeywords="BizTalk Services, BizTalk, issuer name, issuer key, Azure" description="Learn how to retrieve Issuer Name and Issuer Key for either Service Bus or Access Control (ACS) in BizTalk Services." metaCanonical="" services="biztalk-services" documentationCenter="" title="BizTalk Services: Issuer Name and Issuer Key" authors="mandia" solutions="" manager="dwrede" editor="susanjo" />

<tags ms.service="biztalk-services" ms.workload="integration" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/10/2014" ms.author="mandia" />

# Службы BizTalk: Имя и ключ издателя

В службах BizTalk в Azure используются имя и ключ издателя Service Bus, а также имя и ключ издателя службы Access Control. В частности:

<table border="1">
<tr bgcolor="FAF9F9">
<td><strong>Задача</strong></td>
<td><strong>Используемые имя и ключ издателя</strong></td>
</tr>
<tr>
<td>Развертывание приложения из Visual Studio</td>
<td>Имя и ключ издателя для службы Access Control</td>
</tr>
<tr>
<td>Настройка портала служб BizTalk в Azure</td>
<td>Имя и ключ издателя для службы Access Control</td>
</tr>
<tr>
<td>Создание бизнес-ретрансляций со службами адаптера BizTalk в Visual Studio</td>
<td>Имя и ключ издателя шины обслуживания</td>
</tr>
</table>

В этом разделе перечислены шаги для извлечения имени и ключа издателя.

## Имя и ключ издателя для службы Access Control

Имя и ключ издателя для службы Access Control используются в следующих областях:

-   Приложение службы BizTalk в Azure, созданное в Visual Studio: чтобы развернуть свое приложение службы BizTalk в Visual Studio в Azure, введите имя и ключ издателя ACS.
-   Портал служб Azure BizTalk: в первый раз при входе на портал служб BizTalk следует ввести имя службы BizTalk в качестве поставщика службы, имя издателя службы Access Control и ключ издателя службы Access Control.

### Чтобы извлечь имя и ключ издателя для службы Access Control

1.  Выполните вход на [портал управления Azure][портал управления Azure].
2.  В области навигации слева щелкните **Службы BizTalk**.
3.  Выберите службу BizTalk.
4.  Выберите **Сведения о подключении** на панели задач. Отображается список сведений о пространстве имен управления доступом Access Control, издателе по умолчанию (имя издателя) и ключе по умолчанию (ключ издателя), которые можно скопировать в буфер обмена.
    Итого:
    имя издателя = издатель по умолчанию
    ключ издателя = ключ по умолчанию

Чтобы извлечь значения службы Access Control, также можно щелкнуть элемент **Открыть портал управления ACS**:

1.  Выполните вход на [портал управления Azure][портал управления Azure].
2.  В области навигации слева щелкните **Службы BizTalk**.
3.  Выберите службу BizTalk.
4.  Нажмите кнопку "Сведения о подключении" и выберите **Открыть портал управления ACS**.
5.  В разделе **Параметры службы** портала щелкните **Удостоверения службы**. Здесь отображается удостоверение службы, которое является именем издателя для службы Access Control. Щелкните ссылку на идентификатор службы, чтобы просмотреть пароль, который является значением ключа издателя. Их значения можно скопировать.
    Например, в поле **Удостоверения службы** отображается "владелец". "Владелец" — это имя издателя для службы Access Control. При щелчке ссылки "владелец" откроется поле **Пароль**. При щелчке ссылки "Пароль" будет отображено соответствующее значение. Это значение пароля является ключом издателя для службы Access Control.
    Итого:
    имя издателя = имя идентификатора службы
    ключ издателя = значение пароля

Чтобы извлечь значения службы Access Control, также можно выбрать **Active Directory** в области навигации слева.

<div class="dev-callout"> 
<b>Важно!</b> 
<p>Если при создании пространства имен службы Access Control был выбран параметр <strong>Active Directory</strong>, удостоверение службы <strong>не создается</strong> автоматически. При подготовке службы BizTalk автоматически создаются пространство имен Access Control, удостоверение службы &quot;владелец&quot; (имя издателя), пароль (ключ издателя) и симметричный ключ.</p> 
<p><a href="http://go.microsoft.com/fwlink/p/?LinkID=303942">Практическое руководство. Использование службы управления ACS для настройки идентификаторов служб</a> содержит дополнительные сведения об идентификаторах служб Access Control.</p>
</div>

## Имя и ключ издателя шины обслуживания

Имя и ключ издателя Service Bus используются службами адаптера BizTalk. В проекте служб BizTalk в Visual Studio можно использовать службы адаптера BizTalk для подключения к локальной бизнес-системе. Для подключения создайте бизнес-ретранслятор и введите сведения о бизнес-системе. При этом также вводится имя и ключ издателя Service Bus.

### Чтобы извлечь имя и ключ издателя шины обслуживания

1.  Выполните вход на [портал управления Azure][портал управления Azure].
2.  В левой области навигации щелкните **Шина обслуживания**.
3.  Щелкните по пространству имен. На панели задач щелкните **Сведения о подключении**. Появятся поля **Издатель по умолчанию** (имя издателя) и **Ключ по умолчанию** (ключ издателя). Их значения могут быть скопированы.
    Итого:
    имя издателя = издатель по умолчанию
    ключ издателя = ключ по умолчанию

## Далее

Дополнительные разделы по службам BizTalk в Azure:

-   [Установка пакета служб BizTalk в Azure][Установка пакета служб BizTalk в Azure]
-   [Учебники. Службы Azure BizTalk][Учебники. Службы Azure BizTalk]
-   [Как приступить к работе с пакетом SDK для служб BizTalk Azure][Как приступить к работе с пакетом SDK для служб BizTalk Azure]
-   [Службы Azure BizTalk][Службы Azure BizTalk]

## См. также

-   [Практическое руководство. Использование служб управления ACS для настройки идентификаторов службы][Практическое руководство. Использование службы управления ACS для настройки идентификаторов служб]
-   [Службы BizTalk: диаграмма выпусков Developer, Basic, Standard и Premium][Службы BizTalk: диаграмма выпусков Developer, Basic, Standard и Premium]
-   [Службы BizTalk: подготовка с использованием портала управления Azure][Службы BizTalk: подготовка с использованием портала управления Azure]
-   [Службы BizTalk: диаграмма состояния подготовки][Службы BizTalk: диаграмма состояния подготовки]
-   [Службы BizTalk: вкладки "Панель мониторинга", "Монитор" и "Масштаб"][Службы BizTalk: вкладки "Панель мониторинга", "Монитор" и "Масштаб"]
-   [Службы BizTalk: архивация и восстановление][Службы BizTalk: архивация и восстановление]
-   [Службы BizTalk: регулирование][Службы BizTalk: регулирование]

  [портал управления Azure]: http://go.microsoft.com/fwlink/p/?LinkID=213885
  [Практическое руководство. Использование службы управления ACS для настройки идентификаторов служб]: http://go.microsoft.com/fwlink/p/?LinkID=303942
  [Установка пакета служб BizTalk в Azure]: http://go.microsoft.com/fwlink/p/?LinkID=241589
  [Учебники. Службы Azure BizTalk]: http://go.microsoft.com/fwlink/p/?LinkID=236944
  [Как приступить к работе с пакетом SDK для служб BizTalk Azure]: http://go.microsoft.com/fwlink/p/?LinkID=302335
  [Службы Azure BizTalk]: http://go.microsoft.com/fwlink/p/?LinkID=303664
  [Службы BizTalk: диаграмма выпусков Developer, Basic, Standard и Premium]: http://go.microsoft.com/fwlink/p/?LinkID=302279
  [Службы BizTalk: подготовка с использованием портала управления Azure]: http://go.microsoft.com/fwlink/p/?LinkID=302280
  [Службы BizTalk: диаграмма состояния подготовки]: http://go.microsoft.com/fwlink/p/?LinkID=329870
  [Службы BizTalk: вкладки "Панель мониторинга", "Монитор" и "Масштаб"]: http://go.microsoft.com/fwlink/p/?LinkID=302281
  [Службы BizTalk: архивация и восстановление]: http://go.microsoft.com/fwlink/p/?LinkID=329873
  [Службы BizTalk: регулирование]: http://go.microsoft.com/fwlink/p/?LinkID=302282
