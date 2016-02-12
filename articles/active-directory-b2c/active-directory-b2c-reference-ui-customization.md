<properties
	pageTitle="Предварительная версия Azure Active Directory B2C: настройка пользовательского интерфейса | Microsoft Azure"
	description="В этом разделе описаны возможности настройки пользовательского интерфейса в Azure Active Directory B2C"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="mbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/28/2016"
	ms.author="swkrish"/>

# Предварительная версия Azure Active Directory B2C: настройка пользовательского интерфейса Azure AD B2C

Взаимодействие с пользователем имеет первостепенную важность в клиентском приложении. В этом заключается различие между хорошим приложением и превосходным, а также между просто активными клиентами и действительно заинтересованными. Azure Active Directory (AD) B2C позволяет настраивать страницы регистрации, входа (*см. примечание ниже*) и изменения профиля с невероятной точностью.

> [AZURE.NOTE]
Сейчас страницы входа в локальную учетную запись, подтверждающие сообщения электронной почты и страницы самостоятельного сброса пароля можно настроить только с помощью [фирменной символики компании](../active-directory/active-directory-add-company-branding.md). Описанные в этой статье механизмы недоступны для этих целей.

В этой статье содержатся такие сведения:

- общая информация о возможностях настройки пользовательского интерфейса страницы;
- информация о вспомогательном средстве, которое поможет протестировать возможности настройки пользовательского интерфейса страницы с помощью предоставленного образца содержимого;
- описание основных элементов пользовательского интерфейса для каждого типа страницы;
- рекомендации по использованию этих возможностей.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Возможность настройки пользовательского интерфейса страницы

Возможность настройки пользовательского интерфейса страницы позволяет изменять внешний вид страниц регистрации, входа в систему и изменения профиля клиента (посредством настройки [политик](active-directory-b2c-reference-policies.md)). При переходе между приложением и страницами, за которые отвечает служба Azure AD B2C, ваши пользователи не заметят никакой разницы в работе.

В отличие от других служб, где параметры ограничены или доступны только через API, Azure AD B2C использует современный (и более простой) метод настройки пользовательского интерфейса страницы. Он заключается в следующем: служба Azure AD B2C выполняет код в браузере пользователя и использует современный метод загрузки содержимого из URL-адреса, указанного в политике. Этот метод называется [общим доступом к ресурсам независимо от источника (CORS)](http://www.w3.org/TR/cors/). Для разных страниц можно указать разные URL-адреса. Код объединяет содержимое Azure AD B2C (элементы пользовательского интерфейса) с содержимым, загруженным из URL-адреса, и отображает страницу для пользователя. Вам нужно лишь создать корректное содержимое HTML5 и добавить в раздел `<body>` элемент `<div id="api"></div>`. Именно сюда будет добавляться содержимое Azure AD B2C. Затем нужно разместить это содержимое в конечной точке HTTPS (CORS-доступ должен быть включен). Элементы пользовательского интерфейса Azure AD B2C можно также полностью стилизовать.

## Пробное использование функции настройки пользовательского интерфейса

Если вы хотите опробовать функцию настройки взаимодействия с пользователем с помощью нашего примера содержимого HTML и CSS, размещенного в хранилище больших двоичных объектов Azure, то можете воспользоваться [простым вспомогательным средством](active-directory-b2c-reference-ui-customization-helper-tool.md) для отправки и настройки статичного содержимого.

## Основные элементы пользовательского интерфейса для каждого типа страницы

В разделах ниже приведены примеры кода HTML5 (для каждого типа страницы), которые служба Azure AD B2C добавляет в элемент <div id="api"></div> в вашем содержимом. Настроить эти элементы пользовательского интерфейса можно с помощью собственных таблиц стилей. Эти таблицы переопределят таблицы стилей по умолчанию, которые мы добавляем к этим типам страниц во фрагментах <head>.

> [AZURE.IMPORTANT]
	В предварительной версии элементы пользовательского интерфейса будут изменяться по мере получения и обработки ваших отзывов. Всегда проверяйте исходный код страниц по умолчанию на наличие последних обновлений. На самом деле первое изменение, которое рассматривается, — удаление наших таблиц стилей по умолчанию. То есть вам всегда придется добавлять собственные таблицы стилей для этих элементов пользовательского интерфейса в содержимом.

## Страница выбора поставщика удостоверений

На этой странице содержится список поставщиков удостоверений, которых можно выбирать во время регистрации или входа пользователя. Это поставщики удостоверений социальных сетей, например Facebook и Google+, или локальных учетных записей (адрес электронной почты или имя пользователя).

```HTML

<div id="api" data-name="IdpSelections">
	<div class="intro">
         <p>Sign up</p>
	</div>
	
	<div>
		<ul>
			<li>
				<button class="accountButton" id="FacebookExchange">Facebook</button>
			</li>
			<li>
				<button class="accountButton" id="GoogleExchange">Google+</button>
			</li>
			<li>
				<button class="accountButton" id="SignUpWithLogonEmailExchange">Email</button>
			</li>
		</ul>
	</div>
</div>

```

## Страница регистрации локальной учетной записи

Эта страница содержит форму регистрации, которую нужно заполнить пользователю при регистрации с помощью локальной учетной записи (адрес электронной почты или имя пользователя). Форма может содержать различные элементы управления для ввода текста, например текстовое поле, поле пароля, переключатель, раскрывающийся список с единственным выбором или флажки множественного выбора.

```HTML

<div id="api" data-name="SelfAsserted"> 
	<div class="intro"> 
		<p>Create your account by providing the following details</p>
	</div>
	
	<div id="attributeVerification"> 
		<div class="errorText" id="passwordEntryMismatch" style="display: none;">The password entry fields do not match. Please enter the same password in both fields and try again.</div>
		<div class="errorText" id="requiredFieldMissing" style="display: none;">A required field is missing. Please fill out all required fields and try again.</div>
		<div class="errorText" id="fieldIncorrect" style="display: none;">One or more fields are filled out incorrectly. Please check your entries and try again.</div>
		<div class="errorText" id="claimVerificationServerError" style="display: none;"></div>
		<div class="attr" id="attributeList"> 
			<ul>
				<li> 
					<div class="attrEntry validate">
						<div> 
							<div class="verificationInfoText" id="email_intro" style="display: inline;">Verification is necessary. Please click Send button.</div>
							<div class="verificationInfoText" id="email_info" style="display:none">Verification code has been sent to your inbox. Please copy it to the input box below.</div>
							<div class="verificationSuccessText" id="email_success" style="display:none">E-mail address verified. You can now continue.</div>
							<div class="verificationErrorText" id="email_fail_retry" style="display:none">Incorrect code, try again.</div>
							<div class="verificationErrorText" id="email_fail_no_retry" style="display:none">Exceeded number of retries you need to send new code.</div>
							<div class="verificationErrorText" id="email_fail_server" style="display:none">Server error, please try again</div>
							<div class="verificationErrorText" id="email_incorrect_format" style="display:none">Incorect format.</div>
						</div>

					<div class="helpText show">This information is required</div>
						<label>Email</label>
						<input id="email" class="textInput" type="text" placeholder="Email" required="" autofocus=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Email address that can be used to contact you.');" class="tiny">What is this?</a>

					<div class="buttons verify" claim_id="email"> 
						<div id="email_ver_wait" class="working" style="display: none;"></div>
							<label id="email_ver_input_label" for="email_ver_input" style="display: none;">Verification code</label> 
							<input id="email_ver_input" type="text" placeholder="Verification code" style="display:none">
							<button id="email_ver_but_send" class="sendButton" type="button" style="display: inline;">Send verification code</button>
							<button id="email_ver_but_verify" class="verifyButton" type="button" style="display:none">Verify code</button>
							<button id="email_ver_but_resend" class="sendButton" type="button" style="display:none">Send new code</button>
							<button id="email_ver_but_edit" class="editButton" type="button" style="display:none">Change e-mail</button>
							<button id="email_ver_but_default" class="defaultButton" type="button" style="display:none">Default</button> 
						</div>
					</div>
				</li>
				<li> 
					<div class="attrEntry">
						<div class="helpText">8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .This information is required</div>
						<label>Enter password</label>
						<input id="password" class="textInput" type="password" placeholder="Enter password" pattern="^((?=.*[a-z])(?=.*[A-Z])(?=.*\d)|(?=.*[a-z])(?=.*[A-Z])(?=.*[^A-Za-z0-9])|(?=.*[a-z])(?=.*\d)(?=.*[^A-Za-z0-9])|(?=.*[A-Z])(?=.*\d)(?=.*[^A-Za-z0-9]))([A-Za-z\d@#$%^&amp;*-_+=[]{}|\\:',?/`~";();!]|\.(?!@)){8,16}$" title="8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ "; ( ) ; ." required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Enter password');" class="tiny">What is this?</a>
					</div>
				</li>
				<li> 
					<div class="attrEntry">
						<div class="helpText"> This information is required</div>
						<label>Reenter password</label>
						<input id="reenterPassword" class="textInput" type="password" placeholder="Reenter password" pattern="^((?=.*[a-z])(?=.*[A-Z])(?=.*\d)|(?=.*[a-z])(?=.*[A-Z])(?=.*[^A-Za-z0-9])|(?=.*[a-z])(?=.*\d)(?=.*[^A-Za-z0-9])|(?=.*[A-Z])(?=.*\d)(?=.*[^A-Za-z0-9]))([A-Za-z\d@#$%^&amp;*-_+=[]{}|\\:',?/`~";();!]|\.(?!@)){8,16}$" title=" " required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Reenter password');" class="tiny">What is this?</a>
					</div>
				</li>
				<li> 
					<div class="attrEntry">
						<div class="helpText">This information is required</div>
						<label>Name</label>
						<input id="displayName" class="textInput" type="text" placeholder="Name" required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Your display name.');" class="tiny">What is this?</a>
					</div>
				</li>
				<li> 
					<div class="attrEntry">
						<div class="helpText"></div>
						<label>Gender</label>
						<input id="extension_Gender_F" name="extension_Gender" type="radio" value="F" autofocus="">
						<label for="extension_Gender_F">Female</label>
						<input id="extension_Gender_M" name="extension_Gender" type="radio" value="M">
						<label for="extension_Gender_M">Male</label>
						<a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('');" class="tiny">What is this?</a>
					</div>
				</li>
				<li> 
					<div class="attrEntry">
						<div class="helpText"></div>
						<label>Loyalty number</label>
						<input id="extension_MemNum" class="textInput" type="text" placeholder="Loyalty number"><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Membership number');" class="tiny">What is this?</a>
					</div>
				</li>
				<li> 
					<div class="attrEntry">
						<div class="helpText"></div>
						<label>State</label>
						<select class="dropdown_single" id="state">
							<option style="display:none" disabled="disabled" value="placeholder" selected="">State</option>
							<option value="WA">Washington</option>
							<option value="NY">New York</option>
							<option value="CA">California</option>
						</select>
						<a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Your residential state or province.');" class="tiny">What is this?</a>
					</div>
				</li>
				<li> 
					<div class="attrEntry">
						<div class="helpText">This information is required</div>
						<label>Zip code</label>
						<input id="postalCode" class="textInput" type="text" placeholder="Zip code" required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('The postal code of your address.');" class="tiny">What is this?</a>
					</div>
				</li>
			</ul> 
		</div>
		<div class="buttons"> <button id="continue" disabled="">Create</button> <button id="cancel">Cancel</button></div>
	</div>
	<div class="verifying-modal"> 
		<div class="preloader"> <img src="https://login.microsoftonline.com/static/img/win8loader.gif" alt="Please wait"></div>
		<div id="verifying_blurb"></div>
	</div>
</div>

```

## Страница регистрации учетной записи в социальной сети

Эта страница содержит форму регистрации, которую нужно заполнить пользователю при регистрации с использованием существующей учетной записи от таких поставщиков удостоверений социальных сетей, как Facebook или Google+. Эта страница аналогична странице регистрации локальных учетных записей (см. предыдущий раздел), за исключением полей для ввода пароля.

## Страница многофакторной проверки подлинности

Эта страница позволяет пользователю подтвердить свой номер телефона (с помощью SMS-сообщения или голосового звонка) во время регистрации или входа.

```HTML

<div id="api" data-name="Phonefactor">
	<div id="phonefactor_initial">
		<div class="intro">
			<p>Enter a number below that we can send a code via SMS or phone to authenticate you.</p>
		</div>
		<div class="errorText" id="errorMessage" style="display:none"></div>
		<div class="phoneEntry" id="phoneEntry">
			<div class="phoneNumber">
				<select id="countryCode" style="display:inline-block">
					<option value="+93">Afghanistan (+93)</option>
					<!-- Not all country codes listed -->
					<option value="+44">United Kingdom (+44)</option>
					<option value="+1" selected="">United States (+1)</option>
					<!-- Not all country codes listed -->
				</select>
			</div>
			<div class="phoneNumber">
				<input type="text" id="localNumber" style="display:inline-block" placeholder="Phone number">
			</div>
		</div>
		<div id="codeVerification" style="display:none">
			<div>
				<p>Enter your verification code below, or <button id="retryCode" class="textButton">send a new code</button></p>
				<input type="text" id="verificationCode" placeholder="Verification code">
			</div>
		</div>
		<div class="buttons">
			<button id="verifyCode" class="sendInitialCodeButton">Send Code</button>
			<button id="verifyPhone" style="display:inline-block">Call Me</button>
			<button id="cancel" style="display:inline-block">Cancel</button>
		</div>
	</div>
	<div class="dialing-modal">
		<div class="preloader"> <img src="https://login.microsoftonline.com/static/img/win8loader.gif" alt="Please wait"></div>
		<div id="dialing_blurb"></div><div id="dialing_number"></div>
	</div>
</div>

```

## Страница «Ошибка»


```HTML

<div id="api" class="error-page-content" data-name="GlobalException">
	<h2>Sorry, but we're having trouble signing you in.</h2>
    <div class="error-page-help">We track these errors automatically, but if the problem persists feel free to contact us. In the meantime, please try again.</div>
	<div class="error-page-messagedetails">Your administrator hasn't provided any contact details.</div>
	<div class="error-page-messagedetails">
		<div class="error-page-correlationid">Correlation ID:1c4f0397-c6e4-4afe-bf74-42f488f2f15f</div>
		<div>Timestamp:2015-09-14 23:22:35Z</div>
		<div class="error-page-detail">AADB2C90065: A B2C client-side error 'Access is denied.' has occurred requesting the remote resource.</div>
	</div>
</div>

```

## Рекомендации по созданию собственного содержимого

Если вы планируете использовать возможности настройки пользовательского интерфейса страницы, изучите следующие рекомендации.

- Не пытайтесь заменить или изменить стандартный шаблон Azure AD B2C. Лучше создать содержимое HTML5 с нуля, используя стандартный шаблон в качестве образца.
- В целях безопасности мы запрещаем добавлять в содержимое какие-либо сценарии JavaScript. Большинство необходимых вам возможностей уже доступны. Если вам не хватает каких-либо функций, сообщите нам об этом на [форуме](http://feedback.azure.com/forums/169401-azure-active-directory).
- Поддерживаемые версии браузеров:
	- Internet Explorer 11;
	- Internet Explorer 10;
	- Internet Explorer 9 (ограничено);
	- Internet Explorer 8 (ограничено);
	- Google Chrome 43.0;
	- Google Chrome 42.0;
	- Mozilla Firefox 38.0;
	- Mozilla Firefox 37.0.

<!---HONumber=AcomDC_0204_2016-->