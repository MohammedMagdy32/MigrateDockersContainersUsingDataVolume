
# Migrate Docker Containers Using exist  Data Volume 
This document shows you how to Back up and restore instances of the WhatsApp Business API client using  exist data volumes.

## Pre-requisites
- Install Docker from the following [link](https://docs.docker.com/compose/install/?fbclid=IwAR3Qui2fC8-Q3O4qgOEbCERv0l-HSViw80k3pN3xWvLZcakyeasvMMnctzE)
- Set up a Multiple instances of the WhatsApp Business API client on a single machine machine from the following [link](https://github.com/MohammedMagdy32/whatsappApiDeployment)

## Deployment
1. after docker containers creation  you should  take snapshot from  exist data volume
```sh
$ docker run --rm --volumes-from dbstore -v $(pwd):/backup ubuntu tar cvf /backup/backup.tar /dbdata
```
   - Launch a new container and mount the volume from the `dbstore` container
   - Mount a local host directory as `/backup`
   - Pass a command that tars the contents of the `dbdata` volume to a `backup.tar` file inside our `/backup` directory.
2. Restore container from backup
With the backup just created, you can restore it to the same container, or another that you made elsewhere.For example, create a new container named `dbstore2`:
```sh
$ docker run --rm --volumes-from dbstore -v $(pwd):/backup ubuntu tar cvf /backup/backup.tar /dbdata
```
3. Then un-tar the backup file in the new container`s data volume: 
```sh
$ docker run --rm --volumes-from dbstore2 -v $(pwd):/backup ubuntu bash -c "cd /dbdata && tar xvf /backup/backup.tar --strip 1"
```
 
4. Repeat steps above  for each instance located in machine

## Validation
To make sure the app up and running, run the below command
```sh
curl --location --request POST 'https://machineHostIp:PortNumber/v1/users/login' \
--header 'Content-Type: application/json' \
--header 'Authorization: Basic YNMTWRtaDWW46c2VNNXOsjcMNmV580888' \
--data-raw '{
    "new_password": "your password"
}'
```

