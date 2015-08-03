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
   <td valign="middle"><p>Количество входных конечных точек на одну облачную службу<sup>2</sup></p></td>
   <td valign="middle"><p>150</p></td>
   <td valign="middle"><p>150</p></td>
</tr>
</table>

<sup>1</sup>Виртуальные машины, созданные на портале управления службами (а не в диспетчере ресурсов) автоматически сохраняются в облачной службе. Для балансировки нагрузки и обеспечения доступности можно добавить дополнительные виртуальные машины в эту облачную службу. См. статью [Подключение виртуальных машин с помощью виртуальной сети или облачной службы](../virtual-machines/cloud-services-connect-virtual-machine.md).

<sup>2</sup>Входные конечные точки позволяют обмениваться данными с виртуальной машиной из-за пределов облачной службы, в которой находится эта виртуальная машина. Виртуальные машины, размещенные в одной и той же облачной службе или виртуальной сети, могут автоматически взаимодействовать друг с другом. См. статью [Настройка конечных точек для виртуальной машины](../virtual-machines/virtual-machines-set-up-endpoints.md).

<!---HONumber=July15_HO4-->