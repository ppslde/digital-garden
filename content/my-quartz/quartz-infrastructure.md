---
title: quartz infrastructure
description: This is a digital garden where I share my thoughts and ideas.
---

# setup development & CI environment #infrastructure for my digital garden with #quartz.

I cloned the quartz repository to a local folder.
```shell
# Clone the quartz repository
git clone https://github.com/jackyzha0/quartz.git
```

In a second repository I created a folder called `content` and symlinked it to the quartz content folder.
```powershell
# Create a symbolic link to the content folder in the quartz repository
New-Item -ItemType SymbolicLink -Path "absolute\path\to\quartz\repo\root\content" -Target "absolute\path\to\my-quartz\repository\content"
```


 But instead of putting my stuff into the quartz structure I created a second repository and created a symlink to content folder and use my repository without the full quartz structure.