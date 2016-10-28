<properties 
	pageTitle="Интеграция локальных удостоверений с Azure Active Directory."
	description="В этой статье объясняется, что такое служба Azure AD Connect и зачем ее использовать."
	services="multi-factor-authentication"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor="curtand"/>

<tags
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/04/2016"
	ms.author="kgremban"/>

# Построение Multi-Factor Authentication в пользовательских приложениях (SDK)

> [AZURE.IMPORTANT]  Если вы хотите скачать пакет SDK, необходимо создать поставщик Azure Multi-Factor Authentication, даже если у вас уже есть лицензии Azure MFA, AAD Premium или EMS. Если вы создаете поставщик Azure Multi Factor Authentication для этой цели и у вас уже есть лицензии, поставщик нужно создавать с моделью лицензий **для каждого пользователя**, а затем привязать его к каталогу, который содержит лицензии Azure MFA, Azure AD Premium или EMS. В таком случае с вас не будет взиматься плата, пока количество уникальных пользователей пакета SDK не превысит количество лицензий, которыми вы владеете.

Пакет средств разработки программного обеспечения (SDK) для Azure Multi-Factor Authentication позволяет встроить процедуру проверки телефонного вызова и текстового сообщения прямо в процессы входа или обработки транзакции приложений в клиенте Azure AD.

Пакет SDK для Multi-Factor Authentication доступен для C#, Visual Basic (.NET), Java, Perl, PHP и Ruby. Пакет SDK предоставляет тонкую оболочку для многофакторной проверки подлинности. Он включает в себя все необходимое для написания кода, в том числе закомментированные файлы исходного кода, файлы примеров и подробный файл ReadMe. Каждый пакет SDK также содержит сертификат и закрытый ключ для шифрования транзакций, уникальный для поставщика Multi-Factor Authentication. До тех пор пока у вас есть поставщик, пакет SDK можно загружать на всех необходимых языках и во всех требуемых форматах.

API-интерфейсы в пакете SDK для Multi-Factor Authentication имеют довольно простую структуру. Вы отправляете один вызов функции в API-интерфейс с многофакторными параметрами, например режимом проверки, и данными пользователя, такими как телефонный номер для отправки вызова или ПИН-код для проверки. API-интерфейсы преобразуют вызов функции в запросы веб-служб, отправляемые в облачную службу Azure Multi-Factor Authentication. Все вызовы должны содержать ссылку на закрытый сертификат, который имеется в каждом пакете SDK.

Так как API-интерфейсы не имеют доступа к пользователям, зарегистрированным в Azure Active Directory, необходимо предоставить сведения о пользователе, например номера телефонов и ПИН-коды, в файле или базе данных. Кроме того, API-интерфейсы не предоставляют функции управления регистрацией или пользователями, поэтому эти процессы необходимо встроить в приложение.






## Загрузка пакета SDK для Azure Multi-Factor Authentication

Для скачивания пакета SDK для Azure Multi-Factor Authentication требуется [поставщик Azure Multi-Factor Authentication](multi-factor-authentication-get-started-auth-provider.md). Для этого нужна полная версия подписки Azure, даже если у вас уже есть лицензии Azure MFA, AAD Premium или Enterprise Mobility Suite. Чтобы скачать пакет SDK, необходимо перейти на портал управления Multi-Factor Authentication непосредственно с помощью поставщика Multi-Factor Authentication или щелкнув ссылку **Перейти на портал** на странице параметров службы MFA.


### Загрузка пакета SDK для Azure Multi-Factor Authentication с портала Azure


1. Войдите на портал Azure с учетной записью администратора.
2. Выберите слева элемент Active Directory.
3. На странице Active Directory вверху выберите вкладку **Поставщики многофакторной проверки подлинности**.
4. В нижней части страницы щелкните **Управление**.
5. Откроется новая страница. Слева внизу нажмите SDK.
<center>![Download](./media/multi-factor-authentication-sdk/download.png)</center>
6. Выберите необходимый язык и щелкните одну из связанных ссылок для загрузки.
7. Сохраните загружаемый файл.



### Загрузка пакета SDK для Azure Multi-Factor Authentication через параметры службы


1. Войдите на портал Azure с учетной записью администратора.
2. Выберите слева элемент Active Directory.
3. Дважды щелкните свой экземпляр Azure AD.
4. В верхней части экрана щелкните ссылку **Настроить**.
5. В разделе многофакторной проверки подлинности выберите **Управление параметрами службы**. ![Загрузить](./media/multi-factor-authentication-sdk/download2.png)
6. На странице параметров службы в нижней части экрана щелкните ссылку **Перейти на портал**. ![Загрузить](./media/multi-factor-authentication-sdk/download3a.png)
7. Откроется новая страница. Слева внизу нажмите SDK.
8. Выберите необходимый язык и щелкните одну из связанных ссылок для загрузки.
9. Сохраните загружаемый файл.

## Содержимое пакета SDK для Azure Multi-Factor Authentication
Внутри пакета SDK находятся следующие элементы.

- **README**. В этом файле объясняется, как использовать API-интерфейсы для Multi-Factor Authentication в новом или существующем приложении.
- **Исходный файл (файлы)** для Multi-Factor Authentication
- **Сертификат клиента**, используемый для взаимодействия со службой Multi-Factor Authentication
- **Закрытый ключ** для сертификата
- **Результаты вызова.** Список кодов результатов вызова. Чтобы открыть этот файл, используйте приложение с возможностями форматирования текста, например WordPad. Для проверки и устранения неполадок при реализации процесса Multi-Factor Authentication в приложении используйте коды результатов вызова. Они отличаются от кодов состояния проверки подлинности.
- **Примеры.** Пример кода для базовой рабочей реализации процесса Multi-Factor Authentication.


>[AZURE.WARNING]Сертификат клиента — это уникальный закрытый сертификат, созданный специально для вас. Не показывайте никому и не теряйте этот файл. Это ваш ключ для обеспечения безопасности вашего взаимодействия со службой Multi-Factor Authentication.

## Пример кода: проверка телефона в стандартном режиме

В этом примере кода показано, как использовать API-интерфейсы в пакете SDK для Azure Multi-Factor Authentication для добавления в приложение проверки голосового вызова в стандартном режиме. Стандартным режимом является ситуация, при которой для ответа на телефонный вызов пользователь нажимает кнопку #.

В этом примере используется SDK для C# .NET 2.0 Multi-Factor Authentication в базовом приложении ASP.NET с серверной логикой, написанной на C#, но данный процесс очень похож на простые реализации на других языках. Так как пакет SDK содержит не исполняемые, а исходные файлы, можно построить файлы и ссылаться на них или включить их непосредственно в свое приложение.

>[AZURE.NOTE]При реализации службы Multi-Factor Authentication используйте такие дополнительные факторы, как вторичную и третичную проверку в дополнение к основному способу проверки подлинности. Эти методы не предназначены для использования в качестве основных способов проверки подлинности.

### Обзор примера кода
В этом примере кода для очень простого демонстрационного веб-приложения используется телефонный вызов, для ответа на который следует нажимать клавишу #, чтобы выполнить проверку подлинности пользователя. В рамках Multi-Factor Authentication фактор телефонного вызова называется стандартным режимом.

В клиентском коде нет никаких элементов, относящихся к Multi-Factor Authentication. Поскольку дополнительные факторы проверки подлинности не зависят от основной проверки подлинности, их можно добавлять без изменения существующего интерфейса входа. API-интерфейсы в пакете SDK для Multi-Factor Authentication позволяют настраивать взаимодействие с пользователем, но, может быть, менять ничего не будет нужно.

Серверный код добавляет проверку подлинности в стандартном режиме на шаге 2. Он создает объект PfAuthParams с параметрами, необходимыми для проверки в стандартном режиме: имя пользователя, телефонный номер, режим и путь к сертификату клиента (CertFilePath), который требуется в каждом вызове. Демонстрацию всех параметров в PfAuthParams см. в файле примера в пакете SDK.

Затем код передает объект PfAuthParams в функцию pf\_authenticate(). Возвращаемое значение указывает на успешное или неудачное выполнение проверки подлинности. Выходные параметры (callStatus и код ошибки) содержат дополнительные сведения о результатах вызова. Коды результатов вызова описаны в файле результатов вызова в пакете SDK.

Такая минимальная реализация может занимать всего несколько строк. Однако в готовый код следует включить более сложную обработку ошибок, дополнительный код базы данных и улучшенный пользовательский интерфейс.

### Код веб-клиента

Ниже приведен код веб-клиента для демонстрационной страницы.


	<%@ Page Language="C#" AutoEventWireup="true" CodeFile="Default.aspx.cs" Inherits="_Default" %>

	<!DOCTYPE html>

	<html xmlns="http://www.w3.org/1999/xhtml">
	<head runat="server">
	<title>Multi-Factor Authentication Demo</title>
	</head>
	<body>
	<h1>Azure Multi-Factor Authentication Demo</h1>
	<form id="form1" runat="server">

	<div style="width:auto; float:left">
	Username:&nbsp;<br />
	Password:&nbsp;<br />
	</div>

	<div">
	<asp:TextBox id="username" runat="server" width="100px"/><br />
	<asp:Textbox id="password" runat="server" width="100px" TextMode="password" /><br />
	</div>

	<asp:Button id="btnSubmit" runat="server" Text="Log in" onClick="btnSubmit_Click"/>

	<p><asp:Label ID="lblResult" runat="server"></asp:Label></p>

	</form>
	</body>
	</html>


### Серверный код

В следующем серверном коде служба Multi-Factor Authentication настраивается и запускается на шаге 2. Стандартный режим (MODE\_STANDARD) — это телефонный вызов, для ответа на который пользователь нажимает клавишу #.

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Web;
	using System.Web.UI;
	using System.Web.UI.WebControls;

	public partial class _Default : System.Web.UI.Page
	{
	    protected void Page_Load(object sender, EventArgs e)
	    {
	    }

	    protected void btnSubmit_Click(object sender, EventArgs e)
	    {
	        // Step 1: Validate the username and password
	        if (username.Text != "Contoso" || password.Text != "password")
	        {
	            lblResult.ForeColor = System.Drawing.Color.Red;
	            lblResult.Text = "Username or password incorrect.";
	        }
	        else
	        {
	            // Step 2: Perform multi-factor authentication

	            // Add call details from the user database.
	            PfAuthParams pfAuthParams = new PfAuthParams();
	            pfAuthParams.Username = username.Text;
	            pfAuthParams.Phone = "9134884271";
	            pfAuthParams.Mode = pf_auth.MODE_STANDARD;

	            // Specify a client certificate
	            // NOTE: This file contains the private key for the client
	            // certificate. It must be stored with appropriate file
	            // permissions.
	            pfAuthParams.CertFilePath = "c:\\cert_key.p12";

	            // Perform phone-based authentication
	            int callStatus;
	            int errorId;

	            if(pf_auth.pf_authenticate(pfAuthParams, out callStatus, out errorId))
	            {
	                lblResult.ForeColor = System.Drawing.Color.Green;
	                lblResult.Text = "Multi-Factor Authentication succeeded.";
	            }
	            else
	            {
	                lblResult.ForeColor = System.Drawing.Color.Red;
	                lblResult.Text = " Multi-Factor Authentication failed.";
	            }
	        }

	    }
	}

<!---HONumber=AcomDC_0921_2016-->