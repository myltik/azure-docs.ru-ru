<properties linkid="" urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Вопросы, связанные с поддержкой нескольких клиентов из одной мобильной службы" authors="krisragh" solutions="" manager="" editor="" />

# Вопросы, связанные с поддержкой нескольких клиентов из одной мобильной службы
 
Одним из основных преимуществ использования мобильных служб Azure для поддержки разработки мобильных приложений является возможность использовать одну серверную службу, которая поддерживает приложение на нескольких клиентских платформах. Мобильные службы предоставляют собственные клиентские библиотеки для всех основных платформ устройств. Дополнительные сведения см. в разделе [Учебники и ресурсы].

Хотя мобильные службы упрощают перенос собственного приложения на несколько клиентских платформ, используя одну серверную службу, есть несколько моментов, которые нужно спланировать. Этот раздел содержит указания по организации работы push-уведомлений во всех клиентских платформах. Он также описывает способ решения этой проблемы с помощью ориентированного на клиента единого входа с использованием учетной записи Майкрософт в приложениях Магазина Windows и Windows Phone. Наконец, в этом разделе обсуждаются некоторые рекомендации для повторного использования кода в проектах Visual Studio.

## Push-уведомления
В этом разделе рассматриваются два подхода к отправке push-уведомлений от мобильной службы клиентским приложениям на различных платформах.

### Использование концентраторов уведомлений

Концентраторы уведомлений Azure — это служба Azure, масштабируемое решение для отправки push-уведомлений от вашей мобильной службы (или любого сервера) клиентским приложениям на всех основных платформах устройств. Дополнительные сведения см. в разделе [Концентраторы уведомлений Azure]. 

Концентраторы уведомлений обеспечивают согласованную и унифицированную инфраструктуру для создания и управления регистрациями устройств и для отправки push-уведомлений для устройств, работающих на всех основных платформах устройств. Дополнительные сведения см. в разделе [Приступая к работе с концентраторами уведомлений]. Концентраторы уведомлений поддерживают шаблон регистраций в зависимости от платформы, что позволяет использовать один вызов API для отправки уведомления в приложение, работающее на любой зарегистрированной платформе. Дополнительные сведения см. в разделе [Отправка пользователям уведомлений между различными платформами].

Концентраторы уведомлений — это рекомендуемое решение для отправки уведомлений от мобильной службы на множество клиентских платформ.

### Использование общей таблицы регистрации и идентификатора платформы 

Если решено не использовать концентраторы уведомлений, можно по-прежнему поддерживать push-уведомления для нескольких клиентов из вашей мобильной службы, определив общий механизм регистрации устройства. Используйте единую таблицу для хранения данных конкретного устройства, используемых службами уведомлений поддерживаемой платформы. Учебники **Приступая к работе с push-уведомлениями** ([Магазин Windows C#][Get started with push Windows dotnet]/[Магазин Windows JavaScript][Get started with push Windows js]/[Windows Phone][Get started with push Windows Phone]/[iOS][Get started with push iOS]/[Android][Get started with push Android]) используют таблицу **Регистрации** и хранят в столбце с именем _handle_ URI канала (Windows), маркер устройства (iOS) или обработку (Android). 

<div class="dev-callout"><b>Примечание.</b>
	<p>При использовании мастера добавления push-уведомления Visual Studio 2013 для добавления push-уведомлений в приложение магазина Windows мастер автоматически создает таблицу под названием <strong>Канал</strong> для хранения URI каналов. В учебнике <strong>Приступая к работе с push-уведомлениями в мобильных службах с помощью Visual Studio 2012</strong> (<a href="/ru-ru/develop/mobile/tutorials/get-started-with-push-dotnet-vs2012">Магазин Windows C#</a> и <a href="/ru-ru/develop/mobile/tutorials/get-started-with-push-js-vs2012">Магазин Windows JavaScript</a>) показано, как включить push-уведомления с помощью таблицы <strong>Регистрации</strong>.</p>
</div>

Для поддержки нескольких клиентов в этой единой таблице регистрации включите в таблицу столбец _platform_, в поля которого платформы клиента будут вставлять строки во время регистрации. Например, ниже определение класса **Регистрации**, из приложения Магазин Windows C# или Windows Phone, сопоставляет клиентское поле _ChannelUri_ со столбцом _handle_ в таблице "Регистрации": 
		
		public class Registrations
		{
			[JsonProperty(PropertyName = "platform")]			
			public string Platform { 
				get
				{
					return "windowsstore";
					// return "windowsphone";
				}
				set {}
			}
			
		    public string Id { get; set; }
		
			[JsonProperty(PropertyName = "handle")]
			public string ChannelUri { get; set; }
		}

Обратите внимание, что на данном устройстве Windows поле _Platform_ всегда возвращает `windowsstore` (или `windowsphone`). При включенной динамической схеме (по умолчанию) мобильные службы добавляют столбец платформы в таблице "Регистрации", если он еще не существует. Дополнительные сведения см. в разделе [Динамическая схема]. 

В вашем серверном скрипте, который отправляет уведомления, используйте поле платформы для определения, какая именно из функций платформы будет вызывать [push-объект].  Следующий скрипт является модификацией серверного скрипта из учебников **Приступая к работе с push-уведомлениями** ([Магазин Windows C#][Приступая к работе с push-уведомлениями Windows dotnet]/[Магазин Windows JavaScript][Приступая к работе с push-уведомлениями Windows js]/[Windows Phone][Приступая к работе с push-уведомлениями Windows Phone]/[iOS][Приступая к работе с push-уведомлениями iOS]/[Android][Приступая к работе с push-уведомлениями Android]) для включения нескольких клиентских платформ:

		function insert(item, user, request) {
		    request.execute({
		        success: function() {
		            request.respond();
		            sendNotifications();
		        }
		    });
		
		    function sendNotifications() {
		        var registrationsTable = tables.getTable('Registrations');
		        registrationsTable.read({
		            success: function(registrations) {
		                registrations.forEach(function(registration) {
		                    if (registration.platform === 'winstore') {
		                        push.wns.sendToastText04(registration.handle, {
		                            text1: item.text
		                        }, {
		                            success: pushCompleted
		                        });
		                    } else if (registration.platform === 'winphone') {
		                        push.mpns.sendFlipTile(registration.handle, {
		                            title: item.text
		                        }, {
		                            success: pushCompleted
		                        });
		                    } else if (registration.platform === 'ios') {
		                        push.apns.send(registration.handle, {
		                            alert: "Toast: " + item.text,
		                            payload: {
		                                inAppMessage: item.text
		                            }
		                        });
		                    } else if (registration.platform === 'android') {
		                        push.gcm.send(registration.handle, item.text, {
		                            success: pushCompleted
		                        });
		                    } else {
		                        console.error("Unknown push notification platform");
		                    }
		                });
		            }
		        });
		    }
		
		    function pushCompleted(pushResponse) {
		        console.log("Sent push:", pushResponse);
		    }
		}



## Регистрация приложения Windows

Чтобы использовать проверку подлинности клиента при едином входе с помощью учетной записи Майкрософт в приложениях Магазина Windows и Windows Phone, необходимо сначала зарегистрировать приложение Магазина Windows на панели Магазина Windows. Это связано с тем, что после создания регистрации Live Connect для Windows Phone невозможно создать регистрацию для Магазина Windows. Дополнительные сведения о том, как это сделать, см. раздел **Проверка подлинности приложения Магазина Windows с помощью единого входа Live Connect** ([Магазин Windows][SSO Windows Store]/[Windows Phone][SSO Windows Phone]).

## Рекомендации по повторному использованию кода в проектах Visual Studio

Библиотеки переносимого класса позволяют записывать и создавать управляемые сборки, которые работают в более чем одной платформе, таких как Магазин Windows и Windows Phone. Можно создавать классы, содержащие код, который вы хотите использовать во многих проектах, и затем ссылаться на эти классы из проектов разного типа. 

Можно использовать библиотеку переносимого класса платформы .NET Framework для реализации шаблона "модель-представление-представление модели" (MVVM) и совместного использования сборки на нескольких платформах. Можно реализовать классы "модель" и "представление модели" в проекте переносимого класса в Visual Studio 2012 и затем создать представления, которые настроены для различных платформ. Код модели, общий для платформ, может (например) извлекать данные из источника, такого как мобильная служба Azure, безотносительно к платформе. Библиотека MSDN предоставляет <a href="http://msdn.microsoft.com/ru-ru/library/gg597391(v=vs.110)">описание и пример</a>, обсуждение <a href="http://msdn.microsoft.com/ru-ru/library/gg597392(v=vs.110)">отличий API</a>, пример <a href="http://msdn.microsoft.com/ru-ru/library/hh563947(v=vs.110)">использования библиотек переносимого класса для реализации шаблона MVVM</a>, дополнительные <a href="http://msdn.microsoft.com/ru-ru/library/windowsphone/develop/jj714086(v=vs.105).aspx">инструкции</a> и сведения об <a href="http://msdn.microsoft.com/ru-ru/library/hh871422(v=vs.110)">управлении ресурсами</a> в проектах библиотеки переносимого класса.

<!-- URLs -->

[Концентраторы уведомлений Azure]: /ru-ru/develop/net/how-to-guides/service-bus-notification-hubs/
[SSO Windows Store]: /ru-ru/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/
[SSO Windows Phone]: /ru-ru/develop/mobile/tutorials/single-sign-on-wp8/
[Учебники и ресурсы]: /ru-ru/develop/mobile/resources/
[Приступая к работе с концентраторами уведомлений]: /ru-ru/manage/services/notification-hubs/getting-started-windows-dotnet/
[Отправка пользователям уведомлений между различными платформами]: /ru-ru/manage/services/notification-hubs/notify-users-xplat-mobile-services/
[Приступая к работе с push-уведомлениями Windows dotnet]: /ru-ru/develop/mobile/tutorials/get-started-with-push-dotnet-vs2012/
[Приступая к работе с push-уведомлениями Windows js]: /ru-ru/develop/mobile/tutorials/get-started-with-push-js-vs2012/
[Приступая к работе с push-уведомлениями Windows Phone]: /ru-ru/develop/mobile/tutorials/get-started-with-push-wp8/
[Приступая к работе с push-уведомлениями iOS]: /ru-ru/develop/mobile/tutorials/get-started-with-push-ios/
[Приступая к работе с push-уведомлениями Android]: /ru-ru/develop/mobile/tutorials/get-started-with-push-android/
[Динамическая схема]: http://msdn.microsoft.com/ru-ru/library/windowsazure/jj193175.aspx
[Push-объект]: http://msdn.microsoft.com/ru-ru/library/windowsazure/jj554217.aspx

