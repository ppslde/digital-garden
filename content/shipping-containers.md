---
title: container captain
description: Useful hints and commands for container handling
---

## Start a #sqlserver in #podman or #docker
`podman run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=yourStrong(!)Password" -p 1433:1433 -v <hostdir>:/var/opt/mssql/data -d mcr.microsoft.com/mssql/server:2022-latest`

`docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=yourStrong(!)Password" -p 1433:1433 -v <hostdir>:/var/opt/mssql/data -d mcr.microsoft.com/mssql/server:2022-latest`
