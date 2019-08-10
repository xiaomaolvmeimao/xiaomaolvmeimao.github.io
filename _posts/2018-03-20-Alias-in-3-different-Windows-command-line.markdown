---
layout:     post
title:      Alias in 3 different Command line & Bash on Windows
author:     Jexus
tags: 		Git Windows 
subtitle:   在 Windows 環境下的三種 Bash|CMD 設置 Alias 的方法
category:  cheat sheet
---


## Ubuntu Bash on Windows:
最快，直接編輯，跟linux一樣
```bash
sudo vim ~/.bashrc
```

## Git Bash:
找到`C:\Program Files\Git\etc\profile.d\aliases.sh`  

admin mode 下編輯即可  
(也可以用vs code編輯，權限不足他會幫你用系統管理員身分重試)  
## Windows CMD:
最麻煩，只要一次性的話：  

```bash
doskey np=notepad++.exe $*
```
可以用，但無法永久  
於是寫一個.bat，再把此.bat用.reg註冊到regedit
登錄檔裡，之後每次打開CMD都會自動執行  

.bat檔範例
```bat
@echo off

DOSKEY np=notepad++.exe $*
DOSKEY ls=dir /B
```
.reg檔範例
```reg
Windows Registry Editor Version 5.00

[HKEY_CURRENT_USER\Software\Microsoft\Command Processor]
"AutoRun"="%path\\to\\your\\*.bat"
```
把reg檔副檔名改成.reg，然後執行一次即可。

>https://stackoverflow.com/questions/20530996/aliases-in-windows-command-prompt