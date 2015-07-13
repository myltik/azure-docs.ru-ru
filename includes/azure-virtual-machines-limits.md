<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Ресурс</th>
   <th align="left" valign="middle">Ограничение по умолчанию</th>
   <th align="left" valign="middle">Максимальное ограничение</th>
</tr>
<tr>
   <td valign="middle"><p><a href="http://azure.microsoft.com/documentation/services/virtual-machines/">Виртуальные машины</a> на облачную службу<sup>1</sup></p></td>
   <td valign="middle"><p>50</p></td>
   <td valign="middle"><p>50</p></td>
</tr>
<tr>
   <td valign="middle"><p>Входные конечные точки на облачную службу<sup>2</sup></p></td>
   <td valign="middle"><p>150</p></td>
   <td valign="middle"><p>150</p></td>
</tr>
</table>

<sup>1</sup>При создании виртуальной машины за пределами группы ресурсов Azure автоматически создается содержащая ее облачная служба. Можно далее добавить несколько виртуальных машин в ту же самую облачную службу.

<sup>2</sup>Конечные точки ввода используются для подключения к виртуальным машинам за пределами облачной службы, где размещается приложение. Виртуальным машинам в одной облачной службе автоматически разрешено подсоединение по всем портам UDP и TCP для внутренних коммуникаций.

<!---HONumber=62-->