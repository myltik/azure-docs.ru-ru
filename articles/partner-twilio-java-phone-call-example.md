<properties linkid="develop-java-how-to-twilio-phone-call" urlDisplayName="Выполнение телефонных звонков из Twilio в Java" pageTitle="Выполнение телефонных звонков из Twilio (Java) — Azure" metaKeywords="звонок Azure Twilio, веб-сайт для звонков Twilio, Azure Twilio Java" description="Узнайте, как выполнить телефонный звонок с веб-страницы с помощью службы Twilio для Java-приложения в Azure." metaCanonical="" services="" documentationCenter="Java" title="Осуществление телефонных звонков с использованием Twilio в Java-приложении Azure" authors="waltpo" videoId="" scriptId="" solutions="" manager="bjsmith" editor="mollybos" />







# Осуществление телефонных звонков с использованием Twilio в Java-приложении Azure 

В следующем примере показано, как выполнить звонок с веб-страницы, размещенной в Azure, с помощью службы Twilio. В полученном приложении пользователю предлагается ввести нужные данные для телефонного звонка, как показано на следующем снимке экрана.

![Форма вызова Azure с помощью Twilio и Java][twilio_java]

Чтобы использовать код, представленный в этом разделе, выполните следующие действия:

1. Получите учетную запись Twilio и маркер проверки подлинности. Перед началом работы с Twilio вы можете ознакомиться с ценовой политикой на странице [http://www.twilio.com/pricing][twilio_pricing]. Чтобы зарегистрироваться в службе, перейдите на страницу [https://www.twilio.com/try-twilio][try_twilio]. Дополнительные сведения об API-интерфейсе, предоставляемом Twilio, см. на странице [http://www.twilio.com/api][twilio_api].
2. Проверьте свой номер телефона, который будет использоваться в Twilio как идентификатор объекта, выполняющего исходящий вызов. Дополнительные сведения о проверке номера телефона см. в разделе [https://www.twilio.com/user/account/phone-numbers/verified#][verify_phone]. Вместо использования существующего номера вы можете приобрести телефонный номер Twilio.
В этом примере в поле **From** файла callform.jsp (см. описание далее) используется проверенный номер телефона.
3. Получите JAR-файл Twilio. На странице [https://github.com/twilio/twilio-java][twilio_java_github] вы можете загрузить исходные файлы GitHub и создать с их помощью собственные JAR-файл, а также загрузить стандартный JAR-файл, в состав которого могут включаться зависимые компоненты.
В этом разделе приводится код, написанный с использованием стандартного JAR-файла TwilioJava-3.3.8-with-dependencies.
4. Добавьте JAR-файл в путь построения Java.
5. Если вы используете Eclipse для создания Java-приложения, включите JAR-файл Twilio в файл развертывания приложения (WAR) с помощью компонента сборки развертывания Eclipse. Если среда Eclipse не используется, включите JAR-файл Twilio в ту же роль Azure, которой принадлежит ваше Java-приложение, после чего добавьте в приложение путь к классу.
6. Убедитесь, что в хранилище ключей cacerts содержится сертификат Equifax Secure Certificate Authority с отпечатком MD5 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (серийный номер 35:DE:F4:CF, отпечаток SHA1 D2:32:09:AD:23:D3:14:23:21:74:E4:0D:7F:9D:62:13:97:86:63:3A). Это сертификат центра сертификации (ЦС) для службы [https://api.twilio.com][twilio_api_service], который был вызван при использовании интерфейсов API Twilio. Дополнительные сведения о добавлении этого сертификата центра сертификации в хранилище JDK cacert см. в разделе [Добавление сертификата в хранилище сертификатов ЦС Java][add_ca_cert].

Кроме того, если вы не используете среду Eclipse, мы настоятельно рекомендуем вам ознакомиться с разделом [Создание приложения "Hello World" с использованием подключаемого модуля Azure для Eclipse с помощью Java (Microsoft Open Technologies)][azure_java_eclipse_hello_world], а также другими способами размещения Java-приложений в Azure.

## Создание веб-формы для выполнения звонка

В следующем коде показано, как создать веб-форму, позволяющую извлечь данные пользователя для выполнения звонка. В этом примере создается новый динамический веб-проект **TwilioCloud**, в который в качестве JSP-файла добавляется файл **callform.jsp**.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Automated call form</title>
    </head>
    <body>
     <p>Fill in all fields and click <b>Make this call</b>.</p>
     <br/>
      <form action="makecall.jsp" method="post">
       <table>
         <tr>
           <td>To:</td>
           <td><input type="text" size=50 name="callTo" value="" />
           </td>
         </tr>
         <tr>
           <td>From:</td>
           <td><input type="text" size=50 name="callFrom" value="" />
           </td>
         </tr>
         <tr>
           <td>Call message:</td>
           <td><input type="text" size=400 name="callText" value="Hello. This is the call text. Good bye." />
           </td>
         </tr>
         <tr>
           <td colspan=2><input type="submit" value="Make this call" />
           </td>
         </tr>
       </table>
     </form>
     <br/>
    </body>
    </html>

## Создание кода для выполнения звонка
Следующий код вызывается после заполнения формы пользователем, создает сообщение звонка, а затем сам звонок. В этом примере JSP-файлу присваивается имя **makecall.jsp**, после чего он добавляется в проект **TwilioCloud**. Вместо заполнителей **accountSID** и **authToken** в приведенном ниже коде следует указать вашу учетную запись Twilio и маркер проверки подлинности.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    import="java.util.*"
    import="com.twilio.*"
    import="com.twilio.sdk.*"
    import="com.twilio.sdk.resource.factory.*"
    import="com.twilio.sdk.resource.instance.*"
    pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Call processing happens here</title>
    </head>
    <body>
        <b>This is my make call page.</b><p/>
     <%
    try 
    {
         // Use your account SID and authentication token instead
         // of the placeholders shown here.
         String accountSID = "your_twilio_account";
         String authToken = "your_twilio_authentication_token";
     
         // Instantiate an instance of the Twilio client.     
         TwilioRestClient client;
         client = new TwilioRestClient(accountSID, authToken);

         // Retrieve the account, used later to retrieve the CallFactory.
         Account account = client.getAccount();

         // Display the client endpoint. 
         out.println("<p>Using Twilio endpoint " + client.getEndpoint() + ".</p>");
     
         // Display the API version.
         String APIVERSION = TwilioRestClient.DEFAULT_VERSION;
         out.println("<p>Twilio client API version is " + APIVERSION + ".</p>");
    
         // Retrieve the values entered by the user.
         String callTo = request.getParameter("callTo");  
         // The Outgoing Caller ID, used for the From parameter,
         // must have previously been verified with Twilio.
         String callFrom = request.getParameter("callFrom");
         String userText = request.getParameter("callText");
     
         // Replace spaces in the user's text with '%20', 
         // to make the text suitable for a URL.
         userText = userText.replace(" ", "%20");
     
         // Create a URL using the Twilio message and the user-entered text.
         String Url="http://twimlets.com/message";
         Url = Url + "?Message%5B0%5D=" + userText;
     
         // Display the message URL.
         out.println("<p>");
         out.println("The URL is " + Url);
         out.println("</p>");
    
         // Place the call From, To and URL values into a hash map. 
         HashMap<String, String> params = new HashMap<String, String>();
         params.put("From", callFrom);
         params.put("To", callTo);
         params.put("Url", Url);
     
         CallFactory callFactory = account.getCallFactory();
         Call call = callFactory.create(params);
         out.println("<p>Call status: " + call.getStatus()  + "</p>"); 
    } 
    catch (TwilioRestException e) 
    {
        out.println("<p>TwilioRestException encountered: " + e.getMessage() + "</p>");
        out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
    }
    catch (Exception e) 
    {
        out.println("<p>Exception encountered: " + e.getMessage() + "");
        out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
    }
    %>
    </body>
    </html>

Помимо звонка makecall.jsp отображает конечную точку Twilio, версию API-интерфейса и сведения о состоянии звонка. Пример показан на следующем снимке экрана:

![Ответ на звонок Azure с помощью Twilio и Java][twilio_java_response]

## Запуск приложения
Ниже приводятся пошаговые инструкции по запуску приложения. Подробное описание этих шагов см. в разделе [Создание приложения "Hello World" с использованием подключаемого модуля Azure для Eclipse с помощью Java (Microsoft Open Technologies)][azure_java_eclipse_hello_world].

1. Экспортируйте WAR-файл TwilioCloud в папку **approot** Azure. 
2. Измените файл **startup.cmd**, чтобы распаковать WAR-файл TwilioCloud.
3. Выполните компиляцию приложения для эмулятора среды выполнения приложений.
4. Запустите развертывание в эмуляторе среды выполнения приложений.
5. Откройте браузер и выполните файл **http://localhost:8080/TwilioCloud/callform.jsp**.
6. Введите в форму значения, щелкните **Выполнить звонок** и просмотрите результаты в файле makecall.jsp.

Завершив подготовку к развертыванию в Azure, выполните повторную компиляцию для развертывания в облаке, далее выполните развертывание в Azure и запустите http://*your_hosted_name*.cloudapp.net/TwilioCloud/callform.jsp в браузере (замените атрибут *your_hosted_name* именем вашего размещенного приложения).

## Дальнейшие действия
В этом коде демонстрируются базовые функциональные возможности службы Twilio в Java в Azure. Возможно, перед развертыванием в рабочей среде Azure потребуется добавить в него дополнительные обработчики ошибок и другие функции. Например:

* Вместо веб-формы для хранения номеров телефона и текста звонков вы можете использовать хранилище BLOB-объектов Azure или базу данных SQL Azure. Дополнительные сведения об использовании BLOB-объектов Azure в Java см. в разделе [Использование службы хранения BLOB-объектов Azure в Java][howto_blob_storage_java]. Дополнительные сведения об использовании базы данных SQL в Java см. в разделе [Использование базы данных SQL в Java][howto_sql_azure_java].
* С помощью **RoleEnvironment.getConfigurationSettings** вы можете извлечь идентификатор учетной записи Twilio и маркер проверки подлинности из параметров конфигурации развертывания, не прописывая их в файле makecall.jsp в виде фиксированных значений. Дополнительные сведения о классе **RoleEnvironment** см. в разделе [Использование библиотеки среды выполнения служб Azure в JSP][azure_runtime_jsp], а также в документации по пакету среды выполнения служб Azure по адресу [http://dl.windowsazure.com/javadoc][azure_javadoc].
* Код makecall.jsp назначает предоставляемый Twilio URL-адрес [http://twimlets.com/message][twimlet_message_url] переменной **Url**. Этот URL-адрес представляет собой ответ в формате языка разметки Twilio (TwiML), содержащий инструкции по обработке звонка в Twilio. Например, возвращаемый ответ TwiML может содержать команду **&lt;Say&gt;**, которая задает голосовое воспроизведение текста вызываемому абоненту. Вместо предоставляемого Twilio URL-адреса вы можете построить собственную службу, отвечающую на запросы Twilio. Дополнительные сведения см. в разделе [Использование Twilio для поддержки голосовых возможностей и SMS в Java][howto_twilio_voice_sms_java]. Дополнительные сведения о TwiML см. на странице [http://www.twilio.com/docs/api/twiml][twiml]. Дополнительные сведения о команде **&lt;Say&gt;** и других командах Twilio см. на странице [http://www.twilio.com/docs/api/twiml/say][twilio_say].
* Ознакомьтесь с руководством по безопасности Twilio на странице [https://www.twilio.com/docs/security][twilio_docs_security].

Дополнительные сведения о Twilio см. на странице [https://www.twilio.com/docs][twilio_docs].

## См. также
* [Использование Twilio для поддержки голосовых возможностей и SMS в Java][howto_twilio_voice_sms_java]
* [Добавление сертификата в хранилище сертификатов ЦС Java][add_ca_cert]

[twilio_pricing]: http://www.twilio.com/pricing
[try_twilio]: http://www.twilio.com/try-twilio
[twilio_api]: http://www.twilio.com/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_java_github]: http://github.com/twilio/twilio-java
[twimlet_message_url]: http://twimlets.com/message
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api_service]: http://api.twilio.com
[add_ca_cert]: ../java-add-certificate-ca-store
[azure_java_eclipse_hello_world]: http://msdn.microsoft.com/ru-ru/library/windowsazure/hh690944.aspx
[howto_twilio_voice_sms_java]: ../partner-twilio-java-how-to-use-voice-sms
[howto_blob_storage_java]: http://www.windowsazure.com/ru-ru/develop/java/how-to-guides/blob-storage/
[howto_sql_azure_java]: http://msdn.microsoft.com/ru-ru/library/windowsazure/hh749029.aspx
[azure_runtime_jsp]: http://msdn.microsoft.com/ru-ru/library/windowsazure/hh690948.aspx
[azure_javadoc]: http://dl.windowsazure.com/javadoc
[twilio_docs_security]: http://www.twilio.com/docs/security
[twilio_docs]: http://www.twilio.com/docs
[twilio_say]: http://www.twilio.com/docs/api/twiml/say
[twilio_java]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaCallForm.jpg
[twilio_java_response]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaMakeCall.jpg

