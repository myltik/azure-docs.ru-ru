<properties
	pageTitle="Azure AD Connect: начало работы с использованием стандартных параметров | Microsoft Azure"
	description="Узнайте, как скачать, установить и запустить мастер установки Azure AD Connect."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="04/25/2016"
	ms.author="billmath;andkjell"/>

# Приступая к работе с Azure AD Connect с использованием стандартных параметров
Эта статья поможет вам начать работу с Azure Active Directory Connect. В этом документе рассматривается экспресс-установка Azure AD Connect. Стандартные параметры применяются в топологии с одним лесом с синхронизацией паролей. Выполнив всего несколько простых действий, можно расширить локальный каталог в облако.

## Дополнительная документация
Если вы еще не ознакомились с документацией по [интеграции локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md), в следующей таблице приведены ссылки на соответствующие разделы. Указания двух первых разделов, выделенных полужирным шрифтом, необходимо выполнить перед началом установки.

| Раздел | |
| --------- | --------- |
| **Загрузка Azure AD Connect** | [Загрузка Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771) |
| **Оборудование и необходимые компоненты** | [Azure AD Connect: оборудование и необходимые компоненты](active-directory-aadconnect-prerequisites.md) |
| Установка с помощью настроенных параметров | [Выборочная установка Azure AD Connect](active-directory-aadconnect-get-started-custom.md) |
| Обновление из DirSync | [Обновление из средства синхронизации Azure AD (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md) |
| Действия после установки | [Проверка установки и назначение лицензий](active-directory-aadconnect-whats-next.md) |
| Учетные записи, используемые для установки | [Дополнительная информация об учетных записях и разрешениях Azure AD Connect](active-directory-aadconnect-accounts-permissions.md) |


## Экспресс-установка Azure AD Connect
**Стандартные параметры** — это наиболее распространенный и предлагаемый по умолчанию сценарий развертывания. При использовании стандартных параметров Azure AD Connect развертывает службу синхронизации в топологии с одним лесом. [Синхронизация паролей](active-directory-aadconnectsync-implement-password-synchronization.md) включена, поэтому пользователи могут входить в облако с помощью локального пароля. [Автоматическое обновление](active-directory-aadconnect-feature-automatic-upgrade.md) включено, что облегчает обслуживание. При использовании стандартных параметров по завершении установки запускается автоматическая синхронизация (хотя этот действие можно отключить).

### Установка Azure AD Connect с использованием стандартных параметров

1. Войдите с правами локального администратора на сервер, на котором требуется установить Azure AD Connect. Это должен быть сервер, который нужно сделать сервером синхронизации.
2. Перейдите к файлу **AzureADConnect.msi** и дважды щелкните его.
3. На экране приветствия установите флажок, подтверждающий ваше согласие с условиями лицензионного соглашения, и нажмите кнопку **Продолжить**. ![Приветствие мастера установки Azure AD Connect.](./media/active-directory-aadconnect-get-started-express/welcome.png)
4. На экране «Стандартные параметры» щелкните **Использовать стандартные параметры**. ![Приветствие мастера установки Azure AD Connect.](./media/active-directory-aadconnect-get-started-express/express.png)
5. На экране "Подключение к Azure AD" введите имя пользователя и пароль глобального администратора Azure AD. Нажмите кнопку **Далее**. ![Подключение к Azure AD](./media/active-directory-aadconnect-get-started-express/connectaad.png) Если вы получаете сообщение об ошибке и испытываете проблемы с подключением, см. статью [Устранение неполадок подключения в Azure AD Connect](active-directory-aadconnect-troubleshoot-connectivity.md).
6. На экране "Подключение к AD DS" введите имя пользователя и пароль учетной записи администратора предприятия. Вы можете ввести часть домена в формате NetBios или полное доменное имя, т. е. FABRIKAM\\administrator или fabrikam.com\\administrator. Нажмите кнопку **Далее**. ![Подключение к AD DS](./media/active-directory-aadconnect-get-started-express/connectad.png)
7. На экране "Все готово к настройке" нажмите кнопку **Установить**.
	- При необходимости на странице "Готово к настройке" можно снять флажок **Запустить синхронизацию сразу после завершения настройки**. Этот флажок следует снять, если необходимо выполнить дополнительную настройку, например [фильтрацию](active-directory-aadconnectsync-configure-filtering.md). Если отменить выбор этого параметра, мастер настроит синхронизацию, но оставит планировщик отключенным. Он не запустится, пока вы не включите его вручную, повторно запустив мастер установки.
	- Также при необходимости можно настроить службы синхронизации для режима **Гибридное развертывание Exchange**, установив соответствующий флажок. Включите этот параметр, если планируете использовать почтовые ящики Exchange одновременно в облаке и в локальной среде. ![Все готово к настройке Azure AD Connect](./media/active-directory-aadconnect-get-started-express/readytoconfigure.png)
8. После завершения установки щелкните **Выход**.
9. После завершения установки выполните выход и снова войдите, прежде чем начинать использовать диспетчер службы синхронизации или редактор правил синхронизации.

Рекомендуем посмотреть видео по экспресс-установке:

>[AZURE.VIDEO azure-active-directory-connect-express-settings]

## Дальнейшие действия
После установки Azure AD Connect можно [проверить установку и назначить лицензии](active-directory-aadconnect-whats-next.md).

Узнайте больше об [интеграции локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0427_2016-->