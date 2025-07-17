## Docker Compose commands

```powershell
docker compose -f MSSQL2022Dev-ComposeV2.yml logs -f
docker compose -f MSSQL2022Dev-ComposeV2.yml exec mssql bash
docker compose -f MSSQL2022Dev-ComposeV2.yml exec mssql /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P '!Sa@dmin' -Q 'SELECT @@VERSION'
docker compose -f MSSQL2022Dev-ComposeV2.yml exec mssql /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P '!Sa@dmin' -Q 'SELECT name FROM sys.databases'
docker compose -f MSSQL2022Dev-ComposeV2.yml exec mssql /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P '!Sa@dmin' -Q 'CREATE DATABASE TestDB'  
```