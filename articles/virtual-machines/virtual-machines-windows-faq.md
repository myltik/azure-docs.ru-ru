<properties
	pageTitle="Часто задаваемые вопросы по виртуальным машинам Windows | Microsoft Azure"
	description="В этой статье содержатся ответы на некоторые распространенные вопросы о виртуальных машинах Windows, созданных с помощью модели Resource Manager."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/16/2016"
	ms.author="cynthn"/>

# Часто задаваемые вопросы по виртуальным машинам Windows 


В этой статье содержатся ответы на некоторые распространенные вопросы о виртуальных машинах Windows, созданных в Azure с помощью модели развертывания Resource Manager. Версия этой статьи для Linux — [Часто задаваемые вопросы по виртуальным машинам Linux](virtual-machines-linux-faq.md).

## Что можно запускать на виртуальной машине Azure?

Все подписчики могут запускать на виртуальной машине Azure серверное программное обеспечение. Сведения о политике поддержки серверного ПО Майкрософт в Azure см. в разделе [Поддержка серверного программного обеспечения Майкрософт для виртуальных машин Azure](https://support.microsoft.com/kb/2721672).

Подписчикам MSDN Azure, а также подписчикам MSDN для разработки и тестирования с оплатой по мере использования доступны определенные версии Windows 7 и Windows 8.1 для использования в целях разработки или тестирования. Дополнительную информацию, включая указания и ограничения, см. в статье [Образы клиента Windows для подписчиков MSDN](http://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/).


## Какой объем памяти можно использовать с виртуальной машиной?

Каждый диск данных может иметь объем до 1 ТБ. Количество дисков данных, которое можно использовать, зависит от размера виртуальной машины. Дополнительную информацию см. в статье [Размеры виртуальных машин](virtual-machines-windows-sizes.md).

Учетная запись хранения Azure предоставляет хранилище для диска операционной системы и любых дисков данных. Каждый из этих дисков представляет собой VHD-файл, хранящийся как страничный BLOB-объект. Информацию о ценах см. в разделе [Информация о ценах на хранилища](https://azure.microsoft.com/pricing/details/storage/).


## Как получить доступ к своей виртуальной машине?

Установите удаленное подключение с использованием протокола удаленного рабочего стола (RDP) к виртуальной машине Windows. Указания см. в статье [Как подключиться к виртуальной машине Azure под управлением Windows и войти на нее](virtual-machines-windows-connect-logon.md). Если сервер не настроен как узел сеансов служб удаленных рабочих столов, то поддерживается не более двух параллельных подключений.


Если возникли проблемы с удаленным подключением, ознакомьтесь со статьей [Устранение неполадок с подключением к удаленному рабочему столу на виртуальной машине Azure под управлением Windows](virtual-machines-windows-troubleshoot-rdp-connection.md).

Если вы знакомы с Hyper-V, то, возможно, ищете инструмент, аналогичный VMConnect. В Azure такой инструмент не предусмотрен, так как не поддерживается консольный доступ к виртуальной машине.

## Можно ли использовать временный диск (по умолчанию это диск D) для хранения данных?

Не используйте для хранения данных временный диск. Он обеспечивает лишь временное хранение, поэтому вы рискуете потерять данные, которые невозможно восстановить. Потеря данных может происходить, когда виртуальная машина перемещается на другой узел. Виртуальная машина может перемещаться, когда возникает необходимость в изменении ее размера, при обновлении узла, при сбое оборудования, а также по другим причинам.

При наличии приложения, для которого необходимо использовать букву диска D, можно переназначить буквы дисков, чтобы для временного диска использовался диск, отличный от D. Указания см. в статье [Изменение буквы диска для временного диска Windows](virtual-machines-windows-classic-change-drive-letter.md).

## Как изменить букву временного диска?

Можно изменить букву диска, переместив файл подкачки и переназначив буквы дисков, но эти действия нужно выполнять в строго определенном порядке. Указания см. в статье [Изменение буквы диска для временного диска Windows](virtual-machines-windows-classic-change-drive-letter.md).

## Можно ли добавить существующую виртуальную машину в группу доступности?

Нет. Чтобы виртуальная машина была частью группы доступности, ее необходимо создать в группе. Добавлять виртуальные машины в группу доступности после создания пока невозможно.

## Можно ли передать виртуальную машину в Azure?

Да. Указания см. в статье об [отправке образа виртуальной машины Windows в Azure](virtual-machines-windows-upload-image.md).

## Можно ли изменить размер диска ОС?

Да. Указания см. в статье [Как расширить диск ОС виртуальной машины в группе ресурсов Azure](virtual-machines-windows-expand-os-disk.md).

## Можно ли копировать или клонировать существующую виртуальную машину Azure?

Да. Указания см. в статье [Создание копии виртуальной машины Windows в модели развертывания диспетчера ресурсов](virtual-machines-windows-specialized-image.md).

## Почему в Azure Resource Manager не отображаются регионы центральной и восточной Канады?

При создании виртуальных машин в существующих подписках Azure эти два новых региона не регистрируются автоматически. Регистрация осуществляется автоматически при развертывании виртуальной машины на портале Azure в любом другом регионе с помощью Azure Resource Manager. После развертывания виртуальной машины в любом другом регионе Azure новые регионы станут доступными для последующих виртуальных машин.

## Поддерживает ли Azure виртуальные машины Linux?

Да. Чтобы быстро создать пробную виртуальную машину Linux, см. статью [Создание виртуальной машины Linux в Azure с помощью портала](virtual-machines-linux-quick-create-portal.md).

## Создав виртуальную машину, могу я добавить к ней сетевой адаптер?

Нет. Добавить сетевую карту можно только во время создания.

## Есть ли какие-либо требования к имени компьютера?

Да. Длина имени компьютера не должна превышать 15 знаков. Дополнительные сведения об именовании ресурсов см. в статье [Рекомендации по именованию для инфраструктуры](virtual-machines-windows-infrastructure-naming-guidelines.md).

## Какие требования к имени пользователя при создании виртуальной машины?

Длина имени пользователя не должна превышать 20 знаков. Также оно не должно заканчиваться точкой (".").

Не допускаются следующие имена пользователей:

<table>
	<tr>
		<td style="text-align:center">administrator </td><td style="text-align:center"> admin </td><td style="text-align:center"> user </td><td style="text-align:center"> user1</td>
	</tr>
	<tr>
		<td style="text-align:center">test </td><td style="text-align:center"> user2 </td><td style="text-align:center"> test1 </td><td style="text-align:center"> user3</td>
	</tr>
	<tr>
		<td style="text-align:center">admin1 </td><td style="text-align:center"> 1 </td><td style="text-align:center"> 123 </td><td style="text-align:center"> a</td>
	</tr>
	<tr>
		<td style="text-align:center">actuser  </td><td style="text-align:center"> adm </td><td style="text-align:center"> admin2 </td><td style="text-align:center"> aspnet</td>
	</tr>
	<tr>
		<td style="text-align:center">backup </td><td style="text-align:center"> console </td><td style="text-align:center"> david </td><td style="text-align:center"> guest</td>
	</tr>
	<tr>
		<td style="text-align:center">john </td><td style="text-align:center"> владелец </td><td style="text-align:center"> root </td><td style="text-align:center"> server</td>
	</tr>
	<tr>
		<td style="text-align:center">sql </td><td style="text-align:center"> support </td><td style="text-align:center"> support_388945a0 </td><td style="text-align:center"> sys</td>
	</tr>
	<tr>
		<td style="text-align:center">test2 </td><td style="text-align:center"> test3 </td><td style="text-align:center"> user4 </td><td style="text-align:center"> user5</td>
	</tr>
</table>

## Какие требования к паролю при создании виртуальной машины?

Длина пароля должна быть от 8 до 123 знаков. Также должны удовлетворяться 3 из следующих 4 требований сложности:

- используются строчные знаки;
- используются прописные знаки;
- используется цифра;
- используется специальный знак (регулярное выражение [\\W\_]).

Не допускаются следующие пароли:

<table>
	<tr>
		<td style="text-align:center">abc@123</td><td style="text-align:center">P@$$w0rd</td><td style="text-align:center">P@ssw0rd</td><td style="text-align:center">P@ssword123</td><td style="text-align:center">Pa$$word</td>
	</tr>
	<tr>
		<td style="text-align:center">pass@word1</td><td style="text-align:center">Password!</td><td style="text-align:center">Password1</td><td style="text-align:center">Password22</td><td style="text-align:center">iloveyou!</td>
	</tr>
</table>

<!---HONumber=AcomDC_0928_2016-->