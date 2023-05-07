# Debugging Kolla

The logs from all services in all containers may be read from `/var/log/kolla/SERVICE_NAME`

If the stdout logs are needed, please run:

```
docker logs <container-name>
```

Note that most of the containers don’t log to stdout so the above command will provide no information.

The log volume `kolla_logs` is linked to `/var/log/kolla` on the host. You can find all kolla logs in there.

```
readlink -f /var/log/kolla
/var/lib/docker/volumes/kolla_logs/_data
```
