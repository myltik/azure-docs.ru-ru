---
title: Azure AD Connect и конфиденциальность пользователей | Документация Майкрософт
description: В этом документе рассматривается обеспечение соответствия требованиям GDPR в Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2018
ms.author: billmath
ms.openlocfilehash: a1fa7f58040b420bf52d89a57b1234416c2fb939
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32149395"
---
# <a name="user-privacy-and-azure-ad-connect"></a>Конфиденциальность пользователей и Azure AD Connect 

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

>[!NOTE] 
>В этой статье рассматривается конфиденциальность пользователей в Azure AD Connect.  Сведения о конфиденциальности пользователей в Azure Active Directory Connect Health приведены в [этой статье](../../active-directory/connect-health/active-directory-aadconnect-health-gdpr.md).

Соответствия требованиям конфиденциальности пользователей для систем Azure AD Connect можно достичь двумя способами.

1.  По запросу извлекать данные для пользователя и удалять эти данные из установленных решений.
2.  Не хранить данные больше 48 часов.

Команда Azure AD Connect рекомендует второй вариант, так как его намного проще реализовать и поддерживать.

На сервере синхронизации Azure AD Connect хранятся следующие данные о конфиденциальности пользователей:
1.  Данные о пользователе в **базе данных Azure AD Connect**.
2.  Данные в файлах **журнала событий Windows**, которые могут содержать сведения о пользователе.
3.  Данные в **файлах журнала установки Azure AD Connect**, которые могут содержать сведения о пользователе.

При удалении данных пользователя клиенты Azure AD Connect должны следовать следующим рекомендациям.
1.  Регулярно (по крайней мере каждые 48 часов) удаляйте содержимое папки, содержащей файлы журнала установки Azure AD Connect.
2.  Этот продукт может также создавать журналы событий.  Дополнительные сведения о журналах событий см. в [документации здесь](https://msdn.microsoft.com/library/windows/desktop/aa385780.aspx).

Личные данные автоматически удаляются из базы данных Azure AD Connect при удалении из исходной системы, где они были созданы. Требования GDPR не предусматривают какие-либо конкретные действия со стороны администраторов.  Тем не менее требуется, чтобы данные Azure AD Connect синхронизировались с источником данных не реже, чем раз в два дня.

## <a name="delete-the-azure-ad-connect-installation-log-file-folder-contents"></a>Удаление содержимого папки с файлами журнала установки Azure AD Connect
Регулярно проверяйте и удаляйте все содержимое папки **c:\programdata\aadconnect**, кроме файла **PersistedState.Xml**. Этот файл хранит сведения о состоянии из предыдущей установки Azure Connect и используется при установке обновления. В нем нет личных данных, и его не нужно удалять.

>[!IMPORTANT]
>Не удаляйте файл PersistedState.xml.  Этот файл сохраняет состояние предыдущей установки. В нем нет информации о пользователе.

Вы можете просмотреть и удалить эти файлы с помощью проводника Windows или использовать сценарий, подобный приведенному ниже, для выполнения необходимых действий.


```
$Files = ((Get-childitem -Path "$env:programdata\aadconnect" -Recurse).VersionInfo).FileName
Foreach ($file in $files) {
If ($File.ToUpper() -ne "$env:programdata\aadconnect\PERSISTEDSTATE.XML".toupper()) # Do not delete this file
    {Remove-Item -Path $File -Force}
    } 
```

### <a name="schedule-this-script-to-run-every-48-hours"></a>Планирование выполнения сценария каждые 48 часов
Чтобы запланировать выполнение сценария каждые 48 часов, сделайте следующее.

1.  Сохраните сценарий в файле с расширением **PS1**, а затем откройте панель управления и выберите команду **Systems and Security** (Системы и безопасность).
    ![Система](media\active-directory-aadconnect-gdpr\gdpr2.png)

2.  В разделе "Администрирование" щелкните **Расписание выполнения задач**.
    ![Задача.](media\active-directory-aadconnect-gdpr\gdpr3.png)
3.  В планировщике задач нажмите правой кнопкой мыши команду **Task Schedule Library** (Библиотека расписания задач) и щелкните **Create Basic task…** (Создать простую задачу...).
4.  Введите имя для новой задачи и нажмите кнопку **Далее**.
5.  Выберите триггер задачи **Ежедневно** и нажмите кнопку **Далее**.
6.  Установите значение периодичности на **2 дня** и нажмите кнопку **Далее**.
7.  Выберите действие **Запуск программы** и нажмите кнопку **Далее**.
8.  В поле программы или сценария введите **PowerShell** и в поле **Add arguments (optional)** (Добавить аргументы (необязательно)) введите полный путь к сценарию, созданному ранее, а затем нажмите кнопку **Далее**.
9.  На следующем экране показана сводка задачи, которую вы собираетесь создать. Для создания задачи проверьте значения и нажмите кнопку **Готово**.



## <a name="next-steps"></a>Дополнительная информация
* [Просмотр политики конфиденциальности корпорации Майкрософт в центре управления безопасностью](https://www.microsoft.com/trustcenter)
- [Azure AD Connect Health и конфиденциальность пользователей](../../active-directory/connect-health/active-directory-aadconnect-health-gdpr.md)
