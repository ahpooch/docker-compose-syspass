# docker-compose-syspass
Guidance and files to deploy sysPass password manager using Docker Compose.

## Working with docker-compose.yml 
After fulfilling all requirements you could start **sysPass** just like that:
```
docker-compose up -d
```

## Brief description
This docker-compose file spawns three containers to provide fully functional **sysPass** service.
- syspass_nginx
- syspass_app
- syspass_db



## Volumes
This docker-compose file creates some volumes for containers in the folder where it resides at the time of run `docker-compose up` command:
- ./ssl (this folder should contain .crt and .key).
- ./syspass-config
- ./syspass-backup
- ./syspass-db

## Networks
This docker-compose file creates required networks, but you need to create main pre-existent network `syspass_pre-existing`.
You're free to make it your way but it would be sufficient to run 
```shell
docker network create syspass_pre-existing
```

## SSL Requirements
For SSL to work correctly you need to provide certificate in a format required for [jwilder/nginx-proxy](https://hub.docker.com/r/jwilder/nginx-proxy) container. 

Certificate should contain Common Name field set as `<SysPassServerDNSName>`. If you provide only DNS (SAN) during certificate request/issue it wouldn't be sufficient for [jwilder/nginx-proxy](https://hub.docker.com/r/jwilder/nginx-proxy) to work with it.

Public and private SSL certificate files should be named like this:
- `<SysPassServerDNSName>.crt` 
- `<SysPassServerDNSName>.key`

See it's [GitHub project](https://github.com/nginx-proxy/nginx-proxy) for details).

It's plenty of sources where you can find how to convert certificates in desired format but I'll leave here what was my actions giving that I had .pfx certificate as a starting point.
```Powershell
# Change location to a certificate folder
cd <folder with syspass.example.com.pfx file>
# Enter password for export of a certificate and than twice for creating encrypted .key file.
openssl pkcs12 -in .\syspass.example.com.pfx -nocerts -out .\syspass.example.com-encrypted.key
# Export .key file but without password this time.
openssl rsa -in .\syspass.example.com-encrypted.key -out .\syspass.example.com.key
# Export .crt file.
openssl pkcs12 -in .\syspass.example.com.pfx -clcerts -nokeys -out .\syspass.example.com.crt
```