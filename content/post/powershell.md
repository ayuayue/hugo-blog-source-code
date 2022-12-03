---
cssclass:
title: powershell
tags: [IT/Tools, IT/Windows]
image-auto-upload: true
date: 2022-10-03 20:47:46
lastmod: 2022-10-25 08:07:53
---
# powershell
### 升级

```text
Update-Module PowerShellGet
Invoke-Expression "& { $(irm https://aka.ms/install-powershell.ps1) } -UseMSI"

```

### oh-my-posh

[https://ohmyposh.dev/docs/installation/windows](https://ohmyposh.dev/docs/installation/windows)

```text
scoop install https://github.com/JanDeDobbeleer/oh-my-posh/releases/latest/download/oh-my-posh.json
```

### readline

[https://docs.microsoft.com/zh-cn/powershell/module/psreadline/about/about_psreadline?view=powershell-7.2](https://docs.microsoft.com/zh-cn/powershell/module/psreadline/about/about_psreadline?view=powershell-7.2)

```text
Install-Module -Name PSReadLine -AllowClobber -Force

```

### show module

```text
get-installedModule
```

### theme

```text
 Get-PoshThemes
```

### install module

```text
Set-ExecutionPolicy Bypass
Set-PSRepository -Name 'PSGallery' -InstallationPolicy Trusted

Install-Script -Name RefreshEnv -Force -Scope CurrentUser

Install-Module oh-my-posh -Scope CurrentUser
Install-Module posh-git -Scope CurrentUser
Install-Module ZLocation -Scope CurrentUser; 
Install-Module PSReadLine -AllowPrerelease -Force
Install-Module -Name PSFzf -Scope CurrentUser
Install-Module -Name PoshColor -Scope CurrentUser
Install-Module -Name Terminal-Icons -Repository PSGallery -Scope CurrentUser

```

### profile

```text
Import-Module -Name Terminal-Icons
Import-Module -Name PSWindowsUpdate
Import-Module -Name PoshColor
Import-Module -Name PSReadLine
Import-Module posh-git
Import-Module -Name ZLocation 

oh-my-posh init pwsh --config "$env:POSH_THEMES_PATH/powerlevel10k_lean.omp.json" | Invoke-Expression

#PSReadLine快捷键设置
# 设置预测文本来源为历史记录
Set-PSReadLineOption -PredictionSource History
# alt在windows中有特殊用途，这里使用ctrl键代替
Set-PSReadLineKeyHandler -Chord "Ctrl+RightArrow" -Function ForwardWord

# 每次回溯输入历史，光标定位于输入内容末尾
Set-PSReadLineOption -HistorySearchCursorMovesToEnd
Set-PSReadLineKeyHandler -Key UpArrow -Function HistorySearchBackward
Set-PSReadLineKeyHandler -Key DownArrow -Function HistorySearchForward

# 设置 Tab 为菜单补全和 Intellisense
Set-PSReadLineKeyHandler -Key "Tab" -Function MenuComplete
Set-PSReadLineKeyHandler -Key "Ctrl+u" -Function DeleteLine
Set-PSReadLineKeyHandler -Key "Ctrl+d" -Function ViExit


Register-ArgumentCompleter -Native -CommandName winget -ScriptBlock {
    param($wordToComplete, $commandAst, $cursorPosition)
        [Console]::InputEncoding = [Console]::OutputEncoding = $OutputEncoding = [System.Text.Utf8Encoding]::new()
        $Local:word = $wordToComplete.Replace('"', '""')
        $Local:ast = $commandAst.ToString().Replace('"', '""')
        winget complete --word="$Local:word" --commandline "$Local:ast" --position $cursorPosition | ForEach-Object {
            [System.Management.Automation.CompletionResult]::new($_, $_, 'ParameterValue', $_)
        }
}




```


---

## 只提示

```bash

Install-Module PSReadLine -RequiredVersion 2.1.0 -Scope CurrentUser
Import-Module PSReadLine
Set-PSReadLineOption -PredictionSource History
```
code $profile

```
# 每次回溯输入历史，光标定位于输入内容末尾

Set-PSReadLineOption -HistorySearchCursorMovesToEnd

Set-PSReadLineKeyHandler -Key UpArrow -Function HistorySearchBackward

Set-PSReadLineKeyHandler -Key DownArrow -Function HistorySearchForward

  

# 设置 Tab 为菜单补全和 Intellisense

Set-PSReadLineKeyHandler -Key "Tab" -Function MenuComplete

Set-PSReadLineKeyHandler -Key "Ctrl+u" -Function DeleteLine

Set-PSReadLineKeyHandler -Key "Ctrl+d" -Function ViExit


Import-Module PSReadLine

Set-PSReadLineOption -PredictionSource History
```