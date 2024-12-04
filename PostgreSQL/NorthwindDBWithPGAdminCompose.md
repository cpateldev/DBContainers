## How to find postgresql db host name for this container

### Run docker inspect command

> On PowerShell Windows
``` PowerShell
docker inspect postgresdb -f "{{json .NetworkSettings.Networks }}"
````
> On Linux Bash
``` Bash
sudo docker inspect postgresdb -f "{{json .NetworkSettings.Networks }}"
```
Here is the output of this command

```json
{
  "postgresqldbwithpgadmin_postgresdb_default": {
    "IPAMConfig": null,
    "Links": null,
    "Aliases": [
      "postgresdb",
      "PostGreSQL"
    ],
    "MacAddress": "02:42:ac:12:00:03",
    "DriverOpts": null,
    "NetworkID": "1acb6992c3638d53741ae7c87af94dbaed87759c863ed63f87aea0f4c3f25450",
    "EndpointID": "d64e09e6f44df7a9589c9fdb48923852f01a4bf7216de8c7e557170a80e8be95",
    "Gateway": "172.18.0.1",
    "IPAddress": "172.18.0.3",
    "IPPrefixLen": 16,
    "IPv6Gateway": "",
    "GlobalIPv6Address": "",
    "GlobalIPv6PrefixLen": 0,
    "DNSNames": [
      "postgresdb",
      "PostGreSQL",
      "39527802d900"
    ]
  }
}
```
### 4 addresses to use to connect to postgresdb container
1. "IPAddress": "172.18.0.3"
2. "DNSNames": ["postgresdb","PostGreSQL","39527802d900"] <-- any of these values

## To see full container inspect without any command
> Go to Docker Desktop > Open PostGresDB container > Inspect panel > Then look for Network section to find IP address or Alias

![Insepct Container](dbContinerInspectNetwork.png)