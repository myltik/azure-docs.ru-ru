<properties urlDisplayName="Twilio Phone Call" pageTitle="Как выполнять телефонные звонки из Twilio (.NET): Azure" metaKeywords="Azure .NET Twilio, Azure Twilio, Azure phone calls, Azure twilio, Azure SMS, Azure SMS, Azure voice calls, azure voice calls, Azure text messages, Azure text messages, ASP.NET twilio Azure" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Code samples written in .NET." metaCanonical="" services="" documentationCenter=".NET" title="How to make a phone call using Twilio in a web role on Azure" authors="MicrosoftHelp@twilio.com" solutions="" manager="twilio" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="11/25/2014" ms.author="MicrosoftHelp@twilio.com; larryf" />




<h1>Осуществление телефонных звонков с использованием Twilio в веб-роли Azure</h1>

В этом руководстве описывается, как выполнять телефонные звонки с веб-страницы, размещенной в Azure, с помощью службы Twilio. В полученном приложении пользователю предлагается ввести нужные данные для телефонного звонка, как показано на следующем снимке экрана.

![Azure call form using Twilio and ASP.NET][twilio_dotnet_basic_form]



<h2>Оглавление</h2>
- [Необходимые компоненты](#twilio-prereqs)
- [Практическое руководство. Создание веб-формы для выполнения звонка](#howtocreateform)
- [Практическое руководство. Создание кода для выполнения звонка](#howtocreatecode)
- [Дальнейшие действия](#nextsteps)
- [См. также](#seealso)


<h2><a name="twilio-prereqs"></a>Предварительные требования</h2>

Чтобы использовать код, представленный в этом разделе, выполните следующие действия:

1. Получение учетной записи Twilio и токена подтверждения подлинности. Чтобы начать работу в службе Twilio, пройдите регистрацию на странице [https://www.twilio.com/try-twilio][try_twilio]. Ценовая политика описывается на странице [http://www.twilio.com/pricing][twilio_pricing]. Дополнительную информацию о предоставляемых службой Twilio API-интерфейсах см. на странице [http://www.twilio.com/voice/api][twilio_api].
2. Добавьте библиотеку Twilio .NET в свою веб-роль. Дополнительные сведения см. далее в разделе "Добавление библиотек Twilio в проект веб-роли".

Для этого вам потребуются знания процесса, позволяющего создать базовую веб-роль в Azure.

<h2><a name="howtocreateform"></a>Практическое руководство: Создание веб-формы для выполнения звонка</h2>

<h3><a id="use_nuget"></a>Добавление библиотек Twilio в проект веб-роли:</h3>

1.  Откройте решение в Visual Studio.
2.  Щелкните правой кнопкой **Ссылки**.
3.  Выберите **Управление пакетами NuGet**.
4.  Щелкните **В сети**.
5.  В поле поиска "в сети" введите "twilio".
6.  Щелкните **Установить**, предварительно выбрав пакет Twilio.

В следующем коде показано, как создать веб-форму, позволяющую извлечь данные пользователя для выполнения звонка. В этом примере создается веб-роль ASP.NET с именем **TwilioCloud**.

    <%@ Page Title="Home Page" Language="C#" MasterPageFile="~/Site.master"
        AutoEventWireup="true" CodeBehind="Default.aspx.cs"
        Inherits="WebRole1._Default" %>

    <asp:Content ID="HeaderContent" runat="server" ContentPlaceHolderID="HeadContent">
    </asp:Content>
    <asp:Content ID="BodyContent" runat="server" ContentPlaceHolderID="MainContent">
        <div>
            <asp:BulletedList ID="varDisplay" runat="server" BulletStyle="NotSet">
            </asp:BulletedList>
        </div>
        <div>
            <p>Fill in all fields and click <b>Make this call</b>.</p>
            <div>
                To:<br /><asp:TextBox ID="toNumber" runat="server" /><br /><br />
                Message:<br /><asp:TextBox ID="message" runat="server" /><br /><br />
                <asp:Button ID="callpage" runat="server" Text="Make this call"
                    onclick="callpage_Click" />
            </div>
        </div>
    </asp:Content>

<h2><a id="howtocreatecode"></a>Практическое руководство: Создание кода для выполнения звонка</h2>
Следующий код вызывается после заполнения формы пользователем, создает сообщение звонка, а затем сам звонок. В этом примере код выполняется в обработчике события onclick для кнопки формы. Вместо заполнителей **accountSID** и **authToken** в приведенном ниже коде следует указать вашу учетную запись Twilio и маркер аутентификации.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.Web.UI;
    using System.Web.UI.WebControls;
    using Twilio;

    namespace WebRole1
    {
        public partial class _Default : System.Web.UI.Page
        {
            protected void Page_Load(object sender, EventArgs e)
            {

            }

            protected void callpage_Click(object sender, EventArgs e)
            {
                // Call porcessing happens here.

                // Use your account SID and authentication token instead of
                // the placeholders shown here.
                string accountSID = "ACNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";
                string authToken =  "NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";

                // Instantiate an instance of the Twilio client.
                TwilioRestClient client;
                client = new TwilioRestClient(accountSID, authToken);

                // Retrieve the account, used later to retrieve the
                Twilio.Account account = client.GetAccount();
                string APIversuion = client.ApiVersion;
                string TwilioBaseURL = client.BaseUrl;

                this.varDisplay.Items.Clear();
                if (this.toNumber.Text == "" || this.message.Text == "")
                {
                    this.varDisplay.Items.Add(
                            "You must enter a phone number and a message.");
                }
                else
                {
                    // Retrieve the values entered by the user.
                    string to = this.toNumber.Text;
                    string myMessage = this.message.Text;

                    // Create a URL using the Twilio message and the user-entered
                    // text. You must replace spaces in the user's text with '%20'
                    // to make the text suitable for a URL.
                    String Url = "http://twimlets.com/message?Message%5B0%5D="
                            + myMessage.Replace(" ", "%20");

                    // Display the endpoint, API version, and the URL for the message.
                    this.varDisplay.Items.Add("Using Twilio endpoint "
                        + TwilioBaseURL);
                    this.varDisplay.Items.Add("Twilioclient API Version is "
                        + APIversuion);
                    this.varDisplay.Items.Add("The URL is " + Url);

                    // Instantiate the call options that are passed
                    // to the outbound call.
                    CallOptions options = new CallOptions();

                    // Set the call From, To, and URL values.                    
                    options.From = "+14155992671";
                    options.To = to;
                    options.Url = Url;

                    // Place the call.
                    var call = client.InitiateOutboundCall(options);
                    this.varDisplay.Items.Add("Call status: " + call.Status);
                }
            }
        }
    }

Выполняется звонок. После этого отображаются конечная точка Twilio, версия API-интерфейса и сведения о состоянии звонка. На следующем снимке экрана показаны результаты выполнения примера.

![Azure call response using Twilio and ASP.NET][twilio_dotnet_basic_form_output]

Дополнительную информацию о TwiML см. на странице [http://www.twilio.com/docs/api/twiml][twiml]. Дополнительную информацию о команде &lt;Say&gt; и других командах Twilio см. на странице [http://www.twilio.com/docs/api/twiml/say][twilio_say].

<h2><a id="nextsteps"></a>Дальнейшие действия</h2>
В этом коде демонстрируются базовые функциональные возможности службы Twilio в веб-роли ASP.NET в Azure. Возможно, перед развертыванием в рабочей среде Azure потребуется добавить в него дополнительные обработчики ошибок и другие функции. Например:

* Вместо веб-формы для хранения номеров телефона и текста звонков вы можете использовать хранилище BLOB-объектов Azure или экземпляр базы данных SQL Azure. Дополнительную информацию об использовании больших двоичных объектов в Azure см. в статье [Использование хранилища BLOB-объектов из .NET][howto_blob_storage_dotnet]. Дополнительную информацию об использовании базы данных SQL см. в статье [Как использовать базу данных SQL Azure в приложениях .NET][howto_sql_azure_dotnet].
* С помощью RoleEnvironment.getConfigurationSettings вы можете извлечь идентификатор учетной записи Twilio и маркер проверки подлинности из параметров конфигурации развертывания, не прописывая их в форме в виде фиксированных значений. Дополнительную информацию о классе RoleEnvironment см. в разделе [Microsoft.WindowsAzure.ServiceRuntime - пространство имен][azure_runtime_ref_dotnet].
* Рекомендации по безопасности Twilio приведены на странице [https://www.twilio.com/docs/security][twilio_docs_security].
* Дополнительную информацию о службе Twilio см. на странице [https://www.twilio.com/docs][twilio_docs].

##<a name="seealso"></a>Дополнительные материалы
* [Использование Twilio для поддержки голосовых вызовов и SMS в Azure](../twilio-dotnet-how-to-use-for-voice-sms/)

[twilio_pricing]: http://www.twilio.com/pricing
[try_twilio]: http://www.twilio.com/try-twilio
[twilio_api]: http://www.twilio.com/voice/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#

[twilio_dotnet_basic_form]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form.png
[twilio_dotnet_basic_form_output]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form_output.png

[twiml]: http://www.twilio.com/docs/api/twiml



[howto_twilio_voice_sms_dotnet]: /ru-ru/develop/net/how-to-guides/twilio/

[howto_blob_storage_dotnet]: https://www.windowsazure.com/ru-ru/develop/net/how-to-guides/blob-storage/

[howto_sql_azure_dotnet]: https://www.windowsazure.com/ru-ru/develop/net/how-to-guides/sql-database/


[twilio_docs_security]: http://www.twilio.com/docs/security
[twilio_docs]: http://www.twilio.com/docs
[twilio_say]: http://www.twilio.com/docs/api/twiml/say


[azure_runtime_ref_dotnet]: http://msdn.microsoft.com/ru-ru/library/windowsazure/microsoft.windowsazure.serviceruntime.aspx

<!--HONumber=35.1-->
