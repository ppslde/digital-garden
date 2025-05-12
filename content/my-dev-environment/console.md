---
title: my command line
description: Description how I configure my pwsh.
---

Customizing #Powershell makes working with it much more comfortable. This is why I use JanDeDobbeleer's #OhMyPosh to configure a powerfull prompt.

## Install #Oh-My-Posh

Documention: [ohmyposh.dev](https://ohmyposh.dev/docs)

```powershell
winget install JanDeDobbeleer.OhMyPosh -s winget
```

## Customize the Oh-My-Posh prompt

Customize the prompt `<path-to>\oh-my-posh\themes\_my_jandedobbeleer.omp.json`

```json
{
  "$schema": "https://raw.githubusercontent.com/JanDeDobbeleer/oh-my-posh/main/themes/schema.json",
  "blocks": [
    {
      "alignment": "left",
      "segments": [
        {
          "type": "battery",
          "style": "powerline",
          "powerline_symbol": "\ue0b0",
          "foreground": "#193549",
          "background": "#ffeb3b",
          "background_templates": [
            "{{if eq \"Charging\" .State.String}}#40c4ff{{end}}",
            "{{if eq \"Discharging\" .State.String}}#ff5722{{end}}",
            "{{if eq \"Full\" .State.String}}#4caf50{{end}}"
          ],
          "template": " {{ if not .Error }}{{ .Icon }}{{ .Percentage }}{{ end }} ",
          "properties": {
            "charged_icon": "\ue22f ",
            "charging_icon": "\ue234 ",
            "discharging_icon": "\ue231 "
          }
        },
        {
          "background": "#f05151",
          "foreground": "#111111",
          "style": "powerline",
          "powerline_symbol": "\ue0b0",
          "template": " \ueb53 ",
          "type": "root"
        },
        {
          "background": "#2d59a5",
          "foreground": "#ffffff",
          "properties": {
            "folder_separator_icon": " \ue0b1 ",
            "home_icon": "~",
            "style": "folder"
          },
          "style": "powerline",
          "powerline_symbol": "\ue0b0",
          "template": " \uea83 {{ .Path }} ",
          "type": "path"
        },
        {
          "background": "#fffb38",
          "background_templates": [
            "{{ if or (.Working.Changed) (.Staging.Changed) }}#FF9248{{ end }}",
            "{{ if and (gt .Ahead 0) (gt .Behind 0) }}#ff4500{{ end }}",
            "{{ if gt .Ahead 0 }}#B388FF{{ end }}",
            "{{ if gt .Behind 0 }}#B388FF{{ end }}"
          ],
          "foreground": "#0c1922",
          "properties": {
            "branch_template": "{{ trunc 25 .Branch }}",
            "fetch_stash_count": true,
            "fetch_status": true,
            "fetch_upstream_icon": true
          },
          "style": "powerline",
          "powerline_symbol": "\ue0b0",
          "template": " {{ .UpstreamIcon }} {{ .HEAD }} {{if .BranchStatus }} {{ .BranchStatus }}{{ end }}{{ if .Working.Changed }} \uf044 {{ .Working.String }}{{ end }}{{ if and (.Working.Changed) (.Staging.Changed) }} |{{ end }}{{ if .Staging.Changed }} \uf046 {{ .Staging.String }}{{ end }}{{ if gt .StashCount 0 }} \ueb4b {{ .StashCount }}{{ end }} ",
          "trailing_diamond": "\ue0b4",
          "type": "git"
        },
        {
          "background": "#6CA35E",
          "foreground": "#ffffff",
          "properties": {
            "fetch_version": true
          },
          "style": "powerline",
          "powerline_symbol": "\ue0b0",
          "template": " \ue718 {{ if .PackageManagerIcon }}{{ .PackageManagerIcon }} {{ end }}{{ .Full }} ",
          "type": "node"
        },
        {
          
          "style": "powerline",
          "powerline_symbol": "\ue0b0",
          "foreground": "#000000",
          "background": "#00ffff",
          "template": " \ue77f {{ .Full }} ",
          "type": "dotnet"
        },
        {
          "background": "#83769c",
          "foreground": "#ffffff",
          "properties": {
            "always_enabled": true
          },
          "style": "powerline",
          "powerline_symbol": "\ue0b0",
          "template": " \ueba2 {{ .FormattedMs }}\u2800",
          "type": "executiontime"
        },
        {
          "background": "#00897b",
          "background_templates": [
            "{{ if gt .Code 0 }}#e91e63{{ end }}"
          ],
          "foreground": "#ffffff",
          "properties": {
            "always_enabled": true
          },
          "style": "powerline",
          "powerline_symbol": "\ue0b0",
          "template": " \ue23a ",
          "type": "status"
        }
      ],
      "type": "prompt"
    }
  ],
  "console_title_template": "{{ .Shell }} in {{ .Folder }}",
  "final_space": true,
  "version": 3
}
```


## Loading Powershell profile

Create a porfile script in `C:\Users\<your-user>\Documents\PowerShell\Microsoft.PowerShell_profile.ps1` and add the following script:

```powershell
## Map PSDrives to other registry hives
if (!(Test-Path HKCR:)) {
    $null = New-PSDrive -Name HKCR -PSProvider Registry -Root HKEY_CLASSES_ROOT
    $null = New-PSDrive -Name HKU -PSProvider Registry -Root HKEY_USERS
}

## Customize the prompt
function prompt {
    $identity = [Security.Principal.WindowsIdentity]::GetCurrent()
    $principal = [Security.Principal.WindowsPrincipal] $identity
    $adminRole = [Security.Principal.WindowsBuiltInRole]::Administrator

    $prefix = if (Test-Path Variable:/PSDebugContext) { '[DBG]: ' } else { '' }
    if ($principal.IsInRole($adminRole)) {
        $prefix = "[ADMIN]:$prefix"
    }
    $body = 'PS ' + $PWD.path
    $suffix = $(if ($NestedPromptLevel -ge 1) { '>>' }) + '> '
    "${prefix}${body}${suffix}"
}


## Set PSReadLine options and keybindings
$PSROptions = @{
    ContinuationPrompt = '  '
    Colors             = @{
        Operator         = $PSStyle.Foreground.Magenta
        Parameter        = $PSStyle.Foreground.Magenta
        Selection        = $PSStyle.Background.BrightBlack
        InLinePrediction = $PSStyle.Foreground.BrightYellow + $PSStyle.Background.BrightBlack
    }
}
Set-PSReadLineOption @PSROptions
Set-PSReadLineKeyHandler -Chord 'Ctrl+f' -Function ForwardWord
Set-PSReadLineKeyHandler -Chord 'Enter' -Function ValidateAndAcceptLine

## Add argument completer for the dotnet CLI tool
$scriptblock = {
    param($wordToComplete, $commandAst, $cursorPosition)
    dotnet complete --position $cursorPosition $commandAst.ToString() |
        ForEach-Object {
            [System.Management.Automation.CompletionResult]::new($_, $_, 'ParameterValue', $_)
        }
}
Register-ArgumentCompleter -Native -CommandName dotnet -ScriptBlock $scriptblock

# Init the Oh-My-Posh configuration
oh-my-posh init pwsh --config "$env:POSH_THEMES_PATH\_my_jandedobbeleer.omp.json" | Invoke-Expression

```
