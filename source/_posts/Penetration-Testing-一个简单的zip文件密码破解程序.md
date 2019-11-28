---
title: 'Penetration Testing : 一个简单的zip文件密码破解程序'
date: 2019-11-28 21:19:09
tags: [Penetration Testing,Kali Linux,Python]
---


## intro
```python
# author: huangyuhui
# envir: python 3.7.3
# date: november 20,2019
# tested on the os: windows10
# desc: brute force attack the specified zip file
# repo address: https://github.com/yubuntu0109/penetration-testing-learning
```


## code
```python
import zipfile
import optparse
from threading import Thread


def extractfile(zfile, password):
    try:
        print('[-] try to decompress the zip file by the password: %s' % password)
        zfile.extractall(pwd=bytes(password, "utf8"))
        print('[+] the zip file has decompressed successfully by the password: %s' % password)
        exit(0)
    except:
        pass


def main():
    global zname, dname
    parser = optparse.OptionParser("usage: %prog -f <file> -d <dict>")
    parser.add_option('-f', dest='zname', type='string', help='specified zip file')
    parser.add_option('-d', dest='dname', type='string', help='specified pwd dictionary file')
    (options, args) = parser.parse_args()
    if (options.zname is None) | (options.dname is None):
        print(parser.usage)
        exit(0)

    else:
        zname = options.zname
        dname = options.dname
    zfile = zipfile.ZipFile(zname)
    pwdfile = open(dname)
    for line in pwdfile.readlines():
        password = line.strip('\n')
        t = Thread(target=extractfile, args=(zfile, password))
        t.start()


if __name__ == '__main__':
    main()
```


## recommended tools
* Common User Passwords Profiler (CUPP) : https://github.com/Mebus/cupp
* CeWL is a Custom Word List Generator : https://github.com/digininja/CeWL
