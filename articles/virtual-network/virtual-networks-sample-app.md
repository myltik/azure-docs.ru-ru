---
title: Пример приложения Azure для использования с сетями периметра | Документация Майкрософт
description: Развертывание этого простого веб-приложения после создания сети периметра позволит тестировать потоки трафика.
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: ''
ms.assetid: 60340ab7-b82b-40e0-bd87-83e41fe4519c
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: jonor
ms.openlocfilehash: 8506238e41c5d9dac8d76d729d4919b30a0528b9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23124352"
---
# <a name="sample-application-for-use-with-dmzs"></a>Пример приложения для использования с сетями периметра
[Вернуться на страницу с советами и рекомендациями по построению периметра безопасности][HOME]

Эти сценарии PowerShell можно запускать локально на серверах IIS01 и AppVM01. С их помощью можно установить и настроить простое веб-приложение, которое отображает HTML-страницу с сервера переднего плана IIS01 и содержимое с тылового сервера AppVM01.

Это приложение предоставляет простую тестовую среду для многих примеров сети периметра. Такая среда позволяет определить, как на потоки трафика влияют изменения в конечных точках, группах безопасности сети, определенных пользователем маршрутах и правилах брандмауэра.

## <a name="firewall-rule-to-allow-icmp"></a>Правило брандмауэра, разрешающее ICMP-трафик
Выполнение этой простой инструкции PowerShell на любой виртуальной машине Windows разрешает ICMP-трафик (проверка связи). Это обновление брандмауэра упрощает тестирование и устранение неполадок, так как протокол проверки связи в итоге может проходить через брандмауэр Windows (в большинстве дистрибутивов Linux ICMP-трафик разрешен по умолчанию).

```PowerShell
# Turn On ICMPv4
New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" `
    -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow
```

Если вы будете использовать приведенные ниже сценарии, это правило брандмауэра следует добавить в качестве первой инструкции.

## <a name="iis01---web-application-installation-script"></a>Сервер IIS01: сценарий установки веб-приложения
Вот что делает этот сценарий:

1. Включает протокол IMCPv4 (проверка связи) в брандмауэре Windows локального сервера для упрощения тестирования.
2. Устанавливает службы IIS и платформу .NET Framework 4.5.
3. Создает веб-страницу ASP.NET и файл Web.config.
4. Изменяет пул приложений по умолчанию, чтобы упростить доступ к файлам.
5. Задает анонимного пользователя для учетной записи администратора и пароля.

Этот сценарий PowerShell нужно запускать локально, удаленно подключившись к серверу IIS01 через протокол RDP.

```PowerShell
# IIS Server Post Build Config Script
# Get Admin Account and Password
    Write-Host "Please enter the admin account information used to create this VM:" -ForegroundColor Cyan
    $theAdmin = Read-Host -Prompt "The Admin Account Name (no domain or machine name)"
    $thePassword = Read-Host -Prompt "The Admin Password"

# Turn On ICMPv4
    Write-Host "Creating ICMP Rule in Windows Firewall" -ForegroundColor Cyan
    New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow

# Install IIS
    Write-Host "Installing IIS and .Net 4.5, this can take some time, like 15+ minutes..." -ForegroundColor Cyan
    add-windowsfeature Web-Server, Web-WebServer, Web-Common-Http, Web-Default-Doc, Web-Dir-Browsing, Web-Http-Errors, Web-Static-Content, Web-Health, Web-Http-Logging, Web-Performance, Web-Stat-Compression, Web-Security, Web-Filtering, Web-App-Dev, Web-ISAPI-Ext, Web-ISAPI-Filter, Web-Net-Ext, Web-Net-Ext45, Web-Asp-Net45, Web-Mgmt-Tools, Web-Mgmt-Console

# Create Web App Pages
    Write-Host "Creating Web page and Web.Config file" -ForegroundColor Cyan
    $MainPage = '<%@ Page Language="vb" AutoEventWireup="false" %>
    <%@ Import Namespace="System.IO" %>
    <script language="vb" runat="server">
        Protected Sub Page_Load(ByVal sender As Object, ByVal e As System.EventArgs) Handles Me.Load
            Dim FILENAME As String = "\\10.0.2.5\WebShare\Rand.txt"
            Dim objStreamReader As StreamReader
            objStreamReader = File.OpenText(FILENAME)
            Dim contents As String = objStreamReader.ReadToEnd()
            lblOutput.Text = contents
            objStreamReader.Close()
            lblTime.Text = Now()
        End Sub
    </script>

    <!DOCTYPE html>
    <html xmlns="http://www.w3.org/1999/xhtml">
    <head runat="server">
        <title>DMZ Example App</title>
    </head>
    <body style="font-family: Optima,Segoe,Segoe UI,Candara,Calibri,Arial,sans-serif;">
      <form id="frmMain" runat="server">
        <div>
          <h1>Looks like you made it!</h1>
          This is a page from the inside (a web server on a private network),<br />
          and it is making its way to the outside! (If you are viewing this from the internet)<br />
          <br />
          The following sections show:
          <ul style="margin-top: 0px;">
            <li> Local Server Time - Shows if this page is or isnt cached anywhere</li>
            <li> File Output - Shows that the web server is reaching AppVM01 on the backend subnet and successfully returning content</li>
            <li> Image from the Internet - Doesnt really show anything, but it made me happy to see this when the app worked</li>
          </ul>
          <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
            <b>Local Web Server Time</b>: <asp:Label runat="server" ID="lblTime" /></div>
          <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
            <b>File Output from AppVM01</b>: <asp:Label runat="server" ID="lblOutput" /></div>
          <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
            <b>Image File Linked from the Internet</b>:<br />
            <br />
            <img src="http://sd.keepcalm-o-matic.co.uk/i/keep-calm-you-made-it-7.png" alt="You made it!" width="150" length="175"/></div>
        </div>
      </form>
    </body>
    </html>'

    $WebConfig ='<?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <system.web>
        <compilation debug="true" strict="false" explicit="true" targetFramework="4.5" />
        <httpRuntime targetFramework="4.5" />
        <identity impersonate="true" />
        <customErrors mode="Off"/>
      </system.web>
      <system.webServer>
        <defaultDocument>
          <files>
            <add value="Home.aspx" />
          </files>
        </defaultDocument>
      </system.webServer>
    </configuration>'

    $MainPage | Out-File -FilePath "C:\inetpub\wwwroot\Home.aspx" -Encoding ascii
    $WebConfig | Out-File -FilePath "C:\inetpub\wwwroot\Web.config" -Encoding ascii

# Set App Pool to Clasic Pipeline to remote file access will work easier
    Write-Host "Updaing IIS Settings" -ForegroundColor Cyan
    c:\windows\system32\inetsrv\appcmd.exe set app "Default Web Site/" /applicationPool:".NET v4.5 Classic"
    c:\windows\system32\inetsrv\appcmd.exe set config "Default Web Site/" /section:system.webServer/security/authentication/anonymousAuthentication /userName:$theAdmin /password:$thePassword /commit:apphost

# Make sure the IIS settings take
    Write-Host "Restarting the W3SVC" -ForegroundColor Cyan
    Restart-Service -Name W3SVC

    Write-Host
    Write-Host "Web App Creation Successfull!" -ForegroundColor Green
    Write-Host
```

## <a name="appvm01---file-server-installation-script"></a>Сервер AppVM01: сценарий установки файлового сервера
Этот сценарий настраивает тыловой сервер для простого приложения. Вот что делает этот сценарий:

1. Включает в брандмауэре протокол IMCPv4 (проверка связи) для упрощения тестирования.
2. Создает каталог для веб-сайта.
3. Создает текстовый файл, к которому будет удаленно обращаться веб-страница.
4. Устанавливает разрешения для каталога и файла и разрешает к ним анонимный доступ.
5. Отключает усиленную безопасность Internet Explorer для упрощения работы в Интернете на этом сервере. 

> [!IMPORTANT]
> **Рекомендация.** Никогда не отключайте усиленную безопасность Internet Explorer на рабочем сервере. Кроме того, мы не рекомендуем подключаться к Интернету на рабочем сервере. Мы также не рекомендуем предоставлять анонимный доступ к файловым ресурсам. В нашем случае это сделано для простоты.
> 
> 

Этот сценарий PowerShell следует запускать локально, удаленно подключившись к серверу AppVM01 через протокол RDP. Для успешного выполнения сценария оболочку PowerShell необходимо открыть с правами администратора.

```PowerShell
# AppVM01 Server Post Build Config Script
# PowerShell must be run as Administrator for Net Share commands to work

# Turn On ICMPv4
    New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow

# Create Directory
    New-Item "C:\WebShare" -ItemType Directory

# Write out Rand.txt
    $FileContent = "Hello, I'm the contents of a remote file on AppVM01."
    $FileContent | Out-File -FilePath "C:\WebShare\Rand.txt" -Encoding ascii

# Set Permissions on share
    $Acl = Get-Acl "C:\WebShare"
    $AccessRule = New-Object system.security.accesscontrol.filesystemaccessrule("Everyone","ReadAndExecute, Synchronize","ContainerInherit, ObjectInherit","InheritOnly","Allow")
    $Acl.SetAccessRule($AccessRule)
    Set-Acl "C:\WebShare" $Acl

# Create network share
    Net Share WebShare=C:\WebShare "/grant:Everyone,READ"

# Turn Off IE Enhanced Security Configuration for Admins
    Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A7-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0

    Write-Host
    Write-Host "File Server Set up Successfull!" -ForegroundColor Green
    Write-Host
```

## <a name="dns01---dns-server-installation-script"></a>DNS01: сценарий установки DNS-сервера
В этом примере приложения нет сценария установки DNS-сервера. Если для тестирования правил брандмауэра, группы безопасности сети или определенного пользователем маршрута требуется трафик DNS, сервер DNS01 нужно настроить вручную. Для XML-файла конфигурации сети и шаблона Resource Manager в обоих примерах сервер DNS01 указан в качестве основного DNS-сервера, а общедоступный DNS-сервер, размещенный Level 3, — в качестве резервного DNS-сервера. DNS-сервер от Level 3 будет фактическим DNS-сервером, используемым для нелокального трафика. Если сервер DNS01 не установлен, локальной сети DNS не будет.

## <a name="next-steps"></a>Дополнительная информация
* Запуск сценария IIS01 на сервере IIS
* Запуск сценария файлового сервера на сервере AppVM01
* Переход к общедоступному IP-адресу на сервере IIS01 для проверки создания сценария

<!--Link References-->
[HOME]: ../best-practices-network-security.md
