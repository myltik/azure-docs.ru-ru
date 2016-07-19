<properties
   pageTitle="Microsoft Power BI Embedded — внедрение отчета Power BI с помощью IFrame"
   description="Microsoft Power BI Embedded — базовый код для интеграции отчета в приложение, сведения о выполнении проверки подлинности с помощью маркера приложения Power BI Embedded, а также информация о получении отчетов."
   services="power-bi-embedded"
   documentationCenter=""
   authors="minewiskan"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="07/05/2016"
   ms.author="owend"/>

# Внедрение отчета Power BI с помощью IFrame
В этой статье приведен базовый код для использования REST API **Power BI Embedded**, маркеры приложения, IFrame и код JavaScript, с помощью которых можно интегрировать или внедрить отчет в приложение.

В статье [Начало работы с Microsoft Power BI Embedded](power-bi-embedded-get-started.md) содержатся сведения о том, как настроить **коллекцию рабочих областей** для хранения одной или нескольких **рабочих областей** для содержимого отчета. В статье [Приступая к работе с примером Microsoft Power BI Embedded](power-bi-embedded-get-started-sample.md) описывается процедура импорта отчета в **рабочую область**.

Здесь же приводятся действия по внедрению отчета в приложение. Для изучения этой статьи необходимо скачать пример [интеграции отчета с IFrame](https://github.com/Azure-Samples/power-bi-embedded-iframe) на GitHub. В этом примере содержится простое веб-приложение ASP.NET, с помощью которого можно просмотреть базовый код C# и JavaScript, необходимый для интеграции отчета. Более сложный пример, в котором для интеграции отчета используется шаблон архитектуры Model-View-Controller (MVC), см. в [примере веб-приложения панели мониторинга](http://go.microsoft.com/fwlink/?LinkId=761493) на GitHub.

Вот как можно интегрировать отчет.

- Шаг 1. [Получение отчета в рабочей области](#GetReport). На этом этапе вы используете поток маркеров приложений, чтобы получить маркер доступа для вызова операции REST [Получить отчеты](https://msdn.microsoft.com/library/mt711510.aspx). Получив отчет из списка **Получение отчетов**, внедрите отчет в приложение с помощью элемента **IFrame**.
- Шаг 2. [Внедрение отчета в приложение](#EmbedReport). На этом шаге используется маркер для отчета, код JavaScript и IFrame, чтобы интегрировать или внедрить отчет в веб-приложение.

Если нужно запустить пример, чтобы узнать, как интегрировать отчет, скачайте пример [интеграции отчета с использованием IFrame](https://github.com/Azure-Samples/power-bi-embedded-iframe) на GitHub и настройте три параметра Web.Config:

- **AccessKey**. **AccessKey** используется для создания веб-маркера JSON Web Token (JWT), предназначенного для получения и внедрения отчетов. Чтобы узнать, как получить **AccessKey**, см. статью [Начало работы с Microsoft Power BI Embedded](power-bi-embedded-get-started.md).
- **WorkspaceName**. Чтобы узнать, как получить **WorkspaceName**, см. статью [Начало работы с Microsoft Power BI Embedded](power-bi-embedded-get-started.md).
- **WorkspaceId**. Чтобы узнать, как получить **WorkspaceId**, см. статью [Начало работы с Microsoft Power BI Embedded](power-bi-embedded-get-started.md).

В следующих разделах содержится код, необходимый для интеграции отчета.

<a name="GetReport"/>
## Получение отчета в рабочей области

Чтобы интегрировать отчет в приложение, понадобятся такие параметры отчета, как **ID** и **embedUrl**. Чтобы получить параметры отчета **ID** и **embedUrl**, вызовите операцию REST [Получить отчеты](https://msdn.microsoft.com/library/mt711510.aspx) и выберите отчет в списке JSON. В разделе [Внедрение отчета в приложение](#EmbedReport) используются параметры отчета **ID** и **embedUrl** для внедрения отчета в приложение.

### Ответ JSON на операцию получения отчетов
```
{
  "@odata.context":"https://api.powerbi.com/beta/collections/{WorkspaceName}/workspaces/{WorkspaceId}/$metadata#reports","value":[
    {
      "id":"804d3664-…-e71882055dba","name":"Import report sample","webUrl":"https://embedded.powerbi.com/reports/804d3664-...-e71882055dba","embedUrl":"https://embedded.powerbi.com/appTokenReportEmbed?reportId=804d3664-...-e71882055dba"
    },{
      "id":"1d7cff58-…-380610e263a9","name":"Sample Report 2","webUrl":"https://embedded.powerbi.com/reports/1d7cff58-...-380610e263a9","embedUrl":"https://embedded.powerbi.com/appTokenReportEmbed?reportId=1d7cff58-...-380610e263a9"
    }
  ]
}

```

Чтобы вызвать операцию REST [Получить отчеты](https://msdn.microsoft.com/library/mt711510.aspx), используйте маркер приложения. Дополнительные сведения о потоке маркеров приложений см. в статье [Сведения о потоке маркеров приложений в Power BI Embedded](power-bi-embedded-app-token-flow.md). В следующем коде описывается получение списка отчетов JSON. Чтобы внедрить отчет, см. раздел [Внедрение отчета в приложение](#EmbedReport).

```
protected void getReportsButton_Click(object sender, EventArgs e)
{
    //Get an app token to generate a JSON Web Token (JWT). An app token flow is a claims-based design pattern.
    //To learn how you can code an app token flow to generate a JWT, see the PowerBIToken class.
    var appToken = PowerBIToken.CreateDevToken(workspaceName, workspaceId);

    //After you get a PowerBIToken which has Claims including your WorkspaceName and WorkspaceID,
    //you generate JSON Web Token (JWT) . The Generate() method uses classes from System.IdentityModel.Tokens: SigningCredentials,
    //JwtSecurityToken, and JwtSecurityTokenHandler.
    string jwt = appToken.Generate(accessKey);

    //Set app token textbox to JWT string to show that the JWT was generated
    appTokenTextbox.Text = jwt;

    //Construct reports uri resource string
    var uri = String.Format("https://api.powerbi.com/beta/collections/{0}/workspaces/{1}/reports", workspaceName, workspaceId);

    //Configure reports request
    System.Net.WebRequest request = System.Net.WebRequest.Create(uri) as System.Net.HttpWebRequest;
    request.Method = "GET";
    request.ContentLength = 0;

    //Set the WebRequest header to AppToken, and jwt
    //Note the use of AppToken instead of Bearer
    request.Headers.Add("Authorization", String.Format("AppToken {0}", jwt));

    //Get reports response from request.GetResponse()
    using (var response = request.GetResponse() as System.Net.HttpWebResponse)
    {
        //Get reader from response stream
        using (var reader = new System.IO.StreamReader(response.GetResponseStream()))
        {
            //Deserialize JSON string into PBIReports
            PBIReports PBIReports = JsonConvert.DeserializeObject<PBIReports>(reader.ReadToEnd());

            //Clear Textbox
            tb_reportsResult.Text = string.Empty;

            //Get each report
            foreach (PBIReport rpt in PBIReports.value)
            {
                tb_reportsResult.Text += String.Format("{0}\t{1}\t{2}\n", rpt.id, rpt.name, rpt.embedUrl);
            }
        }
    }
}
```

<a name="EmbedReport"/>
## Внедрение отчета в приложение

Прежде чем отчет можно будет внедрить в приложение, для отчета необходимо получить маркер внедрения. Этот маркер похож на маркер приложения, используемый для вызова операций REST **Power BI Embedded**, но он создается для ресурса отчета, а не для ресурса REST. Ниже приведен код, который позволяет получить маркер приложения для отчета. Чтобы использовать маркер отчета для приложения, см. раздел [Внедрение отчета в приложение](#EmbedReportJS).

<a name="EmbedReportToken"/>
### Получение маркера приложения для отчета

```
protected void getReportAppTokenButton_Click(object sender, EventArgs e)
{
    //Get an embed token for a report.
    var token = PowerBIToken.CreateReportEmbedToken(workspaceName, workspaceId, getReportAppTokenTextBox.Text);

    //After you get a PowerBIToken which has Claims including your WorkspaceName and WorkspaceID,
    //you generate JSON Web Token (JWT) . The Generate() method uses classes from System.IdentityModel.Tokens: SigningCredentials,
    //JwtSecurityToken, and JwtSecurityTokenHandler.
    string jwt = token.Generate(accessKey);

    //Set textbox to JWT string. The JavaScript embed code uses the embed jwt for a report.
    reportAppTokenTextbox.Text = jwt;
}
```

<a name="EmbedReportJS"/>
### Интеграция отчета в приложение

Чтобы внедрить отчет **Power BI** в приложение, используйте IFrame и код JavaScript. Ниже приведен пример IFrame и код JavaScript для внедрения отчета. Все примеры кода по внедрению отчета см. в примере [интеграции отчета с помощью IFrame](https://github.com/Azure-Samples/power-bi-embedded-iframe) на GitHub.

![IFrame](media\power-bi-embedded-integrate-report\Iframe.png)


```
window.onload = function () {
    // Client side click to embed a selected report.
    var el = document.getElementById("bEmbedReportAction");
    if (el.addEventListener) {
        el.addEventListener("click", updateEmbedReport, false);
    } else {
        el.attachEvent('onclick', updateEmbedReport);
    }

};

// Update embed report
function updateEmbedReport() {
    // Check if the embed url was selected
    var embedUrl = document.getElementById('tb_EmbedURL').value;

    // To load a report do the following:
    // 1: Set the url
    // 2: Add a onload handler to submit the auth token
    var iframe = document.getElementById('iFrameEmbedReport');
    iframe.src = embedUrl;
    iframe.onload = postActionLoadReport;
}

// Post the auth token to the iFrame.
function postActionLoadReport() {

    // Get the app token.
    accessToken = document.getElementById('MainContent_reportAppTokenTextbox').value;

    // Construct the push message structure
    var m = { action: "loadReport", accessToken: accessToken };
    message = JSON.stringify(m);

    // Push the message.
    iframe = document.getElementById('iFrameEmbedReport');
    iframe.contentWindow.postMessage(message, "*");
}
```
Внедрив в приложение отчет, вы сможете фильтровать в нем данные. В следующем разделе показано, как можно отфильтровать данные в отчете с помощью синтаксиса URL-адреса.

## Фильтрация данных в отчете

Можно отфильтровать внедренный отчет, используя синтаксис URL-адреса. Для этого добавьте параметр строки запроса в исходный URL-адрес iFrame с указанием фильтра. Вы можете **отфильтровать данные по значению** и **скрыть панель фильтра**.


**Фильтрация по значению**

Чтобы отфильтровать данные по значению, используйте синтаксис запроса **$filter** с оператором **eq**.

```
https://app.powerbi.com/reportEmbed
?reportId=d2a0ea38-0694-...-ee9655d54a4a&
$filter={tableName/fieldName}%20eq%20'{fieldValue}'
```

Например, можно отфильтровать данные по свойству Store Chain, для которого задано значение Lindseys. Фильтр в составе URL-адреса будет выглядеть так:

```
$filter=Store/Chain%20eq%20'Lindseys'
```

> [AZURE.NOTE] {tableName/fieldName} не может содержать пробелы или специальные знаки. {FieldValue} принимает одно дискретное значение.

**Скрытие панели фильтра**

Чтобы скрыть **панель фильтра**, добавьте в запрос отчета строку **filterPaneEnabled**.

```
&filterPaneEnabled=false
```

## Дополнительные ресурсы

В этой статье мы рассмотрели код для интеграции отчета **Power BI** в приложение. Обязательно ознакомьтесь со следующими дополнительными примерами на GitHub:

- [пример интеграции отчета с помощью IFrame;](https://github.com/Azure-Samples/power-bi-embedded-iframe)
- [Пример панели мониторинга веб-приложения](http://go.microsoft.com/fwlink/?LinkId=761493)

## См. также
- [System.IdentityModel.Tokens.SigningCredentials](https://msdn.microsoft.com/library/system.identitymodel.tokens.signingcredentials.aspx)
- [System.IdentityModel.Tokens.JwtSecurityToken](https://msdn.microsoft.com/library/system.identitymodel.tokens.jwtsecuritytoken.aspx)
- [System.IdentityModel.Tokens.JwtSecurityTokenHandler](https://msdn.microsoft.com/library/system.identitymodel.tokens.signingcredentials.aspx)

<!---HONumber=AcomDC_0713_2016-->