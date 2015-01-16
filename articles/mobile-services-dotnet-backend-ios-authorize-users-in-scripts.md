<properties pageTitle="Авторизация с помощью службы (iOS) | Центр мобильных разработок" metaKeywords="" description="Узнайте, как осуществить авторизацию пользователей в серверной части .NET мобильных служб Azure." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Service-side authorization of Mobile Services users" authors="krisragh" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />

# Авторизация пользователей мобильных служб на стороне службы

[WACOM.INCLUDE [mobile-services-selector-service-auth-users](../includes/mobile-services-selector-service-auth-users.md)]

В этом разделе показано, как проводить авторизацию пользователей, прошедших проверку подлинности, для доступа к данным в мобильных службах Azure из приложения iOS. В этом учебнике показано, как добавить код к методам доступа к данным в своем контроллере, который фильтрует запросы с учетом идентификатора пользователя, прошедшего проверку подлинности, чтобы каждый пользователь мог видеть только собственные данные.

Этот учебник описывает быстрый запуск мобильных служб и основан на материале предыдущего учебника [Приступая к работе с проверкой подлинности]. Перед началом работы с учебником необходимо пройти задания учебника [Приступая к работе с проверкой подлинности].  

## <a name="register-scripts"></a>Изменение методов доступа к данным

[WACOM.INCLUDE [mobile-services-filter-user-results-dotnet-backend](../includes/mobile-services-filter-user-results-dotnet-backend.md)]


## Тестирование приложения

1. В Xcode откройте проект, который был изменен при прохождении учебника [Начало работы с проверкой подлинности].

2. Нажмите кнопку **Выполнить**, чтобы построить проект, запустите приложение в эмуляторе iPhone, затем войдите в систему с помощью выбранного поставщика идентификации.

   	Обратите внимание, что несмотря на наличие элементов, уже сохраненных в таблице TodoItem после прохождения предыдущих учебников, возврат элементов не происходит. Причина этого состоит в том, что предыдущие элементы были вставлены без столбца идентификатора пользователя и теперь имеют значения NULL.

3. В приложении введите текст в окне **Вставка TodoItem**, затем щелкните **Сохранить**.

   	![][3]

   	Это приведет к вставке текста и идентификатора пользователя в таблицу TodoItem в мобильной службе. Новый элемент имеет правильное значение идентификатора пользователя, поэтому происходят его возврат мобильной службой и отображение во втором столбце.

5. Вернувшись к таблице **todoitem** на [Портале управления][Портала управления Azure], щелкните **Обзор** и проверьте, имеет ли каждый вновь добавленный элемент соответствующее значение идентификатора пользователя.

6. (Необязательно) При наличии дополнительных учетных записей можно проверить, что пользователи могут просмотреть только свои собственные данные, закрыв приложение и затем запустив его снова. Открыв диалоговое окно ввода учетных данных для входа, задайте другое имя входа, затем проверьте, не отображаются ли элементы, введенные в предыдущей учетной записи.

<!--## Next steps

This concludes the tutorials that demonstrate the basics of working with authentication. Consider finding out more about the following Mobile Services topics:

* [Get started with data]
  <br/>Узнайте больше о хранении и передаче запросов к данным с использованием мобильных служб.

* [Приступая к работе с push-уведомлениями]
  <br/>Отправка очень простого push-уведомления в приложение.

* [Справочник принципов использования мобильных служб .NET]
  <br/>Использование мобильных служб с .NET.
-->

<!-- Anchors. -->
[Регистрация скриптов сервера]: #register-scripts
[Следующие шаги]:#next-steps

<!-- Images. -->

[3]: ./media/mobile-services-dotnet-backend-ios-authorize-users-in-scripts/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Начало работы с мобильными службами]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-get-started
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data
[Приступая к работе с проверкой подлинности]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users
[Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push

[Концептуальный справочник по мобильным службам на платформе .NET]: /ru-ru/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
