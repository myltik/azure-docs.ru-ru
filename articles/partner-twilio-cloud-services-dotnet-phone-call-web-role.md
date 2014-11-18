<properties urlDisplayName="Twilio Phone Call" pageTitle="Как выполнять телефонные звонки из Twilio (.NET) &mdash; Azure" metaKeywords="Azure .NET Twilio, Azure Twilio, Azure phone calls, Azure twilio, Azure SMS, Azure SMS, Azure voice calls, azure voice calls, Azure text messages, Azure text messages, ASP.NET twilio Azure" description="Узнайте, как осуществлять телефонные вызовы и отправку SMS-сообщений с помощью службы Twilio API в Azure. Примеры программного кода написаны в .NET." metaCanonical="" services="" documentationCenter=".NET" title="Осуществление телефонных звонков с использованием Twilio в веб-роли Azure" authors="MicrosoftHelp@twilio.com" solutions="" manager="twilio" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com; larryf" />

# Осуществление телефонных звонков с использованием Twilio в веб-роли Azure

В этом руководстве описывается, как выполнять телефонные звонки с веб-страницы, размещенной в Azure, с помощью службы Twilio. В полученном приложении пользователю предлагается ввести нужные данные для телефонного звонка, как показано на следующем снимке экрана.

![Форма звонка Azure с использованием службы Twilio и ASP.NET][Форма звонка Azure с использованием службы Twilio и ASP.NET]

## Оглавление

-   [Предварительные требования][Предварительные требования]
-   [Практическое руководство. Создание веб-формы для выполнения звонка][Практическое руководство. Создание веб-формы для выполнения звонка]
-   [Практическое руководство. Создание кода для выполнения звонка][Практическое руководство. Создание кода для выполнения звонка]
-   [Дальнейшие действия][Дальнейшие действия]
-   [Дополнительные материалы][Дополнительные материалы]

## <a name="twilio-prereqs"></a>Предварительные требования

Чтобы использовать код, представленный в этом разделе, выполните следующие действия:

1.  Получение учетной записи Twilio и токена подтверждения подлинности. Чтобы начать работу в службе Twilio, пройдите регистрацию на странице [https://www.twilio.com/try-twilio][https://www.twilio.com/try-twilio]. Ценовая политика описывается на странице <http://www.twilio.com/pricing>. Дополнительные сведения о предоставляемых службой Twilio API-интерфейсах см. на странице <http://www.twilio.com/voice/api>.
2.  Проверьте свой номер телефона с помощью службы Twilio. Дополнительные сведения о проверке номера телефона см. на странице <https://www.twilio.com/user/account/phone-numbers/verified#>. Вместо уже существующего номера вы можете приобрести телефонный номер Twilio.
    В этом примере для отправки сообщений на проверенный номер телефона используется номер телефона в изолированной среде Twilio. Номер телефона в изолированной среде можно использовать только для отправки сообщений на проверенные номера телефона.
3.  Добавьте библиотеку Twilio .NET в свою веб-роль. Дополнительные сведения см. далее в разделе "Добавление библиотек Twilio в проект веб-роли".

Для этого вам потребуются знания процесса, позволяющего создать базовую веб-роль в Azure.

## <a name="howtocreateform"></a>Практическое руководство. Создание веб-формы для выполнения звонка

### <span id="use_nuget"></span></a>Добавление библиотек Twilio в проект веб-роли:

1.  Откройте решение в Visual Studio.
2.  Щелкните правой кнопкой мыши **References** (Ссылки).
3.  Выберите **Управление пакетами NuGet...**.
4.  Щелкните **Online** (В сети).
5.  В поле поиска online введите *twilio*.
6.  Щелкните **Install** (Установить) на пакете Twilio.

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

## <span id="howtocreatecode"></span></a>Практическое руководство. Создание кода для выполнения звонка

Следующий код вызывается после заполнения формы пользователем, создает сообщение звонка, а затем сам звонок. В этом примере код выполняется в обработчике события onclick для кнопки формы. Вместо заполнителей **accountSID** и **authToken** в приведенном ниже коде следует указать вашу учетную запись Twilio и маркер проверки подлинности.

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

                    // Diplay the enpoint, API version, and the URL for the message.
                    this.varDisplay.Items.Add("Using Tilio endpoint "
                        + TwilioBaseURL);
                    this.varDisplay.Items.Add("Twilioclient API Version is "
                        + APIversuion);
                    this.varDisplay.Items.Add("The URL is " + Url);

                    // Instantiate the call options that are passed
                    // to the outbound call.
                    CallOptions options = new CallOptions();

                    // Set the call From, To, and URL values into a hash map.
                    // This sample uses the sandbox number provided by Twilio
                    // to make the call.
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

![Ответ на звонок Azure с использованием службы Twilio и ASP.NET][Ответ на звонок Azure с использованием службы Twilio и ASP.NET]

Дополнительные сведения о TwiML см. на странице <http://www.twilio.com/docs/api/twiml>. Дополнительные сведения о команде \<Say\> и других командах Twilio см. на странице <http://www.twilio.com/docs/api/twiml/say>.

## <span id="nextsteps"></span></a>Дальнейшие действия

В этом коде демонстрируются базовые функциональные возможности службы Twilio в веб-роли ASP.NET в Azure. Возможно, перед развертыванием в рабочей среде Azure потребуется добавить в него дополнительные обработчики ошибок и другие функции. Например:

-   Вместо веб-формы для хранения номеров телефона и текста звонков вы можете использовать хранилище BLOB-объектов Azure или экземпляр базы данных SQL Azure. Дополнительные сведения об использовании BLOB-объектов в Azure см. в разделе [Использование службы хранения BLOB-объектов Azure в .NET][Использование службы хранения BLOB-объектов Azure в .NET]. Дополнительные сведения об использовании базы данных SQL см. в разделе [Использование базы данных SQL Azure в приложениях .NET][Использование базы данных SQL Azure в приложениях .NET].
-   С помощью RoleEnvironment.getConfigurationSettings вы можете извлечь идентификатор учетной записи Twilio и маркер проверки подлинности из параметров конфигурации развертывания, не прописывая их в форме в виде фиксированных значений. Дополнительные сведения о классе RoleEnvironment см. в разделе [Пространство имен Microsoft.WindowsAzure.ServiceRuntime][Пространство имен Microsoft.WindowsAzure.ServiceRuntime].
-   Руководства по безопасности работы с Twilio — [https://www.twilio.com/docs/security][https://www.twilio.com/docs/security].
-   Дополнительные сведения о службе Twilio см. на странице [https://www.twilio.com/docs][https://www.twilio.com/docs].

## <a name="seealso"></a>Дополнительные материалы

-   [Использование Twilio для поддержки голосовых вызовов и SMS в Azure][Использование Twilio для поддержки голосовых вызовов и SMS в Azure]

  [Форма звонка Azure с использованием службы Twilio и ASP.NET]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form.png
  [Предварительные требования]: #twilio-prereqs
  [Практическое руководство. Создание веб-формы для выполнения звонка]: #howtocreateform
  [Практическое руководство. Создание кода для выполнения звонка]: #howtocreatecode
  [Дальнейшие действия]: #nextsteps
  [Дополнительные материалы]: #seealso
  [https://www.twilio.com/try-twilio]: http://www.twilio.com/try-twilio
  [Ответ на звонок Azure с использованием службы Twilio и ASP.NET]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form_output.png
  [Использование службы хранения BLOB-объектов Azure в .NET]: https://www.windowsazure.com/ru-ru/develop/net/how-to-guides/blob-storage/
  [Использование базы данных SQL Azure в приложениях .NET]: https://www.windowsazure.com/ru-ru/develop/net/how-to-guides/sql-database/
  [Пространство имен Microsoft.WindowsAzure.ServiceRuntime]: http://msdn.microsoft.com/ru-ru/library/windowsazure/microsoft.windowsazure.serviceruntime.aspx
  [https://www.twilio.com/docs/security]: http://www.twilio.com/docs/security
  [https://www.twilio.com/docs]: http://www.twilio.com/docs
  [Использование Twilio для поддержки голосовых вызовов и SMS в Azure]: ../twilio-dotnet-how-to-use-for-voice-sms/
