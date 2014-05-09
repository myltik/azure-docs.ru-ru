<properties linkid="develop-net-how-to-twilio-phone-call" urlDisplayName="Служба Twilio Phone Call" pageTitle="Выполнение телефонных звонков из Twilio (.NET) — Azure" metaKeywords="Azure .NET Twilio, Azure Twilio, телефонные звонки Azure, Azure twilio, Azure SMS, Azure SMS, голосовые вызовы Azure, голосовые вызовы azure, текстовые сообщения Azure, текстовые сообщения Azure, ASP.NET twilio Azure" description="Узнайте, как выполнять телефонные звонки и отправлять текстовые сообщения с помощью API службы Twilio для Azure. Примеры кода для .NET." metaCanonical="" services="" documentationCenter=".NET" title="Осуществление телефонных звонков с использованием Twilio в веб-роли Azure" authors="" solutions="" manager="" editor="" />





<h1>Осуществление телефонных звонков с использованием Twilio в веб-роли Azure</h1>

В этом руководстве описывается, как выполнять телефонные звонки с веб-страницы, размещенной в Azure, с помощью службы Twilio. В полученном приложении пользователю предлагается ввести нужные данные для телефонного звонка, как показано на следующем снимке экрана.

![Форма звонка Azure с использованием службы Twilio и ASP.NET][twilio_dotnet_basic_form]



<h2><span class="short-header">Оглавление</span>Оглавление</h2>
- [Предварительные требования](#twilio-prereqs)
- [Создание веб-формы для выполнения звонка](#howtocreateform)
- [Создание кода для выполнения звонка](#howtocreatecode)
- [Следующие шаги](#nextsteps)
- [Дополнительные материалы](#seealso)


<h2><a name="twilio-prereqs"></a><span class="short-header">Предварительные требования</span>Предварительные требования</h2>

Чтобы использовать код, представленный в этом разделе, выполните следующие действия:

1. Получите учетную запись Twilio и маркер проверки подлинности. Чтобы начать работу в службе Twilio, пройдите регистрацию на странице [https://www.twilio.com/try-twilio][try_twilio]. Ценовая политика описывается на странице [http://www.twilio.com/pricing][twilio_pricing]. Дополнительные сведения о предоставляемых службой Twilio API-интерфейсах см. на странице [http://www.twilio.com/voice/api][twilio_api].
2. Проверьте ваш номер телефона с помощью службы Twilio. Дополнительные сведения о проверке номера телефона см. на странице [https://www.twilio.com/user/account/phone-numbers/verified#][verify_phone]. Вместо использования существующего номера вы можете приобрести телефонный номер Twilio.<br/>
В этом примере для отправки сообщений на проверенный номер телефона используется номер телефона в изолированной среде Twilio. Номер телефона в изолированной среде можно использовать только для отправки сообщений на проверенные номера телефона.
3. Добавьте библиотеку Twilio .NET в свою веб-роль. Дополнительные сведения см. далее в разделе "Добавление библиотек Twilio в проект веб-роли".

Для этого вам потребуются знания процесса, позволяющего создать базовую веб-роль в Azure.

<h2><a name="howtocreateform"></a><span class="short-header">Создание веб-формы для выполнения звонка</span>Создание веб-формы для выполнения звонка</h2>

<h3><a id="use_nuget"></a>Добавление библиотек Twilio в проект веб-роли:</h3>

1.  Откройте решение в Visual Studio.
2.  Щелкните правой кнопкой **Ссылки**.
3.  Щелкните **Управление пакетами NuGet**.
4.  Щелкните **В сети**.
5.  В поле поиска введите *twilio*.
6.  Щелкните **Установить** на пакете Twilio.

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

<h2><a id="howtocreatecode"></a><span class="short-header">Создание кода</span>Создание кода для выполнения звонка</h2>
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

![Ответ на звонок Azure с использованием службы Twilio и ASP.NET][twilio_dotnet_basic_form_output]

Дополнительные сведения о TwiML см. на странице [http://www.twilio.com/docs/api/twiml][twiml]. Дополнительные сведения о команде &lt;Say&gt; и других командах Twilio см. на странице [http://www.twilio.com/docs/api/twiml/say][twilio_say].

<h2><a id="nextsteps"></a><span class="short-header">Дальнейшие действия</span>Дальнейшие действия</h2>
В этом коде демонстрируются базовые функциональные возможности службы Twilio в веб-роли ASP.NET в Azure. Возможно, перед развертыванием в рабочей среде Azure потребуется добавить в него дополнительные обработчики ошибок и другие функции. Например:

* Вместо веб-формы для хранения номеров телефона и текста звонков вы можете использовать хранилище BLOB-объектов Azure или экземпляр базы данных SQL Azure. Дополнительные сведения об использовании BLOB-объектов в Azure см. в разделе [Использование службы хранения BLOB-объектов Azure в .NET][howto_blob_storage_dotnet]. Дополнительные сведения об использовании базы данных SQL см. в разделе [Использование базы данных SQL Azure в приложениях .NET][howto_sql_azure_dotnet].
* С помощью RoleEnvironment.getConfigurationSettings вы можете извлечь идентификатор учетной записи Twilio и маркер проверки подлинности из параметров конфигурации развертывания, не прописывая их в форме в виде фиксированных значений. Дополнительные сведения о классе RoleEnvironment см. в разделе [Пространство имен Microsoft.WindowsAzure.ServiceRuntime][azure_runtime_ref_dotnet].
* Рекомендации по безопасности Twilio приведены на странице [https://www.twilio.com/docs/security][twilio_docs_security].
* Дополнительные сведения о службе Twilio см. на странице [https://www.twilio.com/docs][twilio_docs].

<h2><a id="seealso"></a><span class="short-header">Дополнительные материалы</span>Дополнительные материалы</h2>
* [Использование Twilio для поддержки голосовых возможностей и SMS в веб-роли][howto_twilio_voice_sms_dotnet]


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

