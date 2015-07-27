<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Ресурс</th>
   <th align="left" valign="middle">Ограничение по умолчанию</th>
   <th align="left" valign="middle">Максимальное ограничение</th>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/azure/jj156007.aspx">Виртуальных сетей</a> на подписку<sup>1</sup></p></td>
   <td valign="middle"><p>10</p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p>Всего машин <sup>2</sup> на виртуальную сеть</p></td>
   <td valign="middle"><p>2048</p></td>
   <td valign="middle"><p>2048</p></td>
</tr>
<tr>
   <td valign="middle"><p>Параллельных соединений TCP для виртуальной машины или экземпляра роли</p></td>
   <td valign="middle"><p>500K</p></td>
   <td valign="middle"><p>500K</p></td>
</tr>
<tr>
   <td valign="middle"><p>Списки управления доступом (ACL) на конечную точку<sup>3</sup></p></td>
   <td valign="middle"><p>50</p></td>
   <td valign="middle"><p>50</p></td>
</tr>
<tr>
   <td valign="middle"><p>Сайтов локальной сети на виртуальную сеть</p></td>
   <td valign="middle"><p>10</p></td>
   <td valign="middle"><p>10</p></td>
</tr>
</table>

<sup>1</sup>Каждая виртуальная сеть поддерживает один [шлюз виртуальной сети](http://msdn.microsoft.com/library/azure/jj156210.aspx).

<sup>2</sup>Общее число машин включает виртуальные машины, а также экземпляры рабочих и веб-ролей.

<sup>3</sup>ACL поддерживаются на конечных точках входа для виртуальных машин. Для ролей рабочего или веб-процесса поддерживаются для входных конечных точек и входных конечных точек экземпляра.

<!---HONumber=July15_HO3-->