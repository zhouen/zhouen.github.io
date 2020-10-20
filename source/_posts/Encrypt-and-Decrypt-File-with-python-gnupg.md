---
layout: post
title: Encrypt and Decrypt File with `python-gnupg`
sitemap: false
date: 2020-10-21 00:23:54
categories:
tags:
---

## Installation/Deployment
```sh
$ sudo apt-get install gnupg
$ sudo adduser testgpguser
$ sudo su testgpguser
$ cd
$ virtualenv --no-site-packages venv
$ source venv/bin/activate
$ pip install python-gnupg
```

## Importing and receiving keys
```python
import gnupg
from pprint import pprint

gpg = gnupg.GPG(gnupghome='/home/testgpguser/gpghome')
key_data = open('mykeyfile.asc').read()
import_result = gpg.import_keys(key_data)
pprint(import_result.results)
```

## List Keys
```python
import gnupg
from pprint import pprint

gpg = gnupg.GPG(gnupghome='/home/testgpguser/gpghome')
public_keys = gpg.list_keys()
private_keys = gpg.list_keys(True)
print 'public keys:'
pprint(public_keys)
print 'private keys:'
pprint(private_keys)
```

## Encrypt File

```python
import gnupg

gpg = gnupg.GPG(gnupghome='/home/testgpguser/gpghome')
open('my-unencrypted.txt', 'w').write('You need to Google Venn diagram.')
with open('my-unencrypted.txt', 'rb') as f:
    status = gpg.encrypt_file(
        f, recipients=['testgpguser@mydomain.com'],
        output='my-encrypted.txt.gpg'
    )
    print 'ok: ', status.ok
    print 'status: ', status.status
    print 'stderr: ', status.stderr
```

## Decrypt File
```python
import gnupg

gpg = gnupg.GPG(gnupghome='/home/testgpguser/gpghome')
with open('my-encrypted.txt.gpg', 'rb') as f:
    status = gpg.decrypt_file(f, passphrase='mypassphrase', output='my-decrypted.txt')
    print 'ok: ', status.ok
    print 'status: ', status.status
    print 'stderr: ', status.stderr
```

## Encrypt String
```python
import gnupg

gpg = gnupg.GPG(gnupghome='/home/testgpguser/gpghome')
unencrypted_string = 'Who are you? How did you get in my house?'
encrypted_data = gpg.encrypt(unencrypted_string, 'testgpguser@mydomain.com')
encrypted_string = str(encrypted_data)
print 'ok: ', encrypted_data.ok
print 'status: ', encrypted_data.status
print 'stderr: ', encrypted_data.stderr
print 'unencrypted_string: ', unencrypted_string
print 'encrypted_string: ', encrypted_string
```

## Decrypt String
```python
import gnupg

gpg = gnupg.GPG(gnupghome='/home/testgpguser/gpghome')
unencrypted_string = 'Who are you? How did you get in my house?'
encrypted_data = gpg.encrypt(unencrypted_string, 'testgpguser@mydomain.com')
encrypted_string = str(encrypted_data)
decrypted_data = gpg.decrypt(encrypted_string, passphrase='my passphrase')

print 'ok: ', decrypted_data.ok
print 'status: ', decrypted_data.status
print 'stderr: ', decrypted_data.stderr
print 'decrypted string: ', decrypted_data.data
```

## References
[python-gnupg - A Python wrapper for GnuPG — Python Wrapper for GnuPG 0.4.0 documentation](https://pythonhosted.org/python-gnupg/)
[Python gnupg (GPG) example - SaltyCrane Blog](https://www.saltycrane.com/blog/2011/10/python-gnupg-gpg-example/)
