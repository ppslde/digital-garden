---
title: some useful developer hacks
description: A collection of developer hacks I use
---

## #Windows 11 menu
Disable #Win11 Menu ("Show more options"-Menu)

```powershell
reg add "HKCU\Software\Classes\CLSID\{86ca1aa0-34aa-4e8b-a509-50c905bae2a2}\InprocServer32" /f /ve
```

Enable #Win11 Menu ("Show more options"-Menu)
```powershell
reg delete "HKCU\Software\Classes\CLSID\{86ca1aa0-34aa-4e8b-a509-50c905bae2a2}" /f
```

## Global #Git ignore

Create a `.gitignore` file in user's home directory and tell git to use this one as well.

```bash
git config --global core.excludesFile "<path-to-user-path>\.gitignore"
```