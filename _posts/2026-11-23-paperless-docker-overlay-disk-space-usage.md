## Paperless-ngx server using lots of disk space (Docker overlay)

A brief note for when I eventually forget this.

Recently ran into an issue where my paperless-ngx server was running out of disk space. This appeared to be caused by Docker's overlay. 

```
root@paperless:/home/# df -h
Filesystem      Size  Used Avail Use% Mounted on
tmpfs           382M  1.7M  381M   1% /run
/dev/sda1        38G   28G  7.7G  79% /
tmpfs           1.9G     0  1.9G   0% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
/dev/sda15      253M  6.2M  246M   3% /boot/efi
tmpfs           382M   12K  382M   1% /run/user/1000
overlay          38G   28G  7.7G  79% /var/lib/docker/overlay2/cc332f0b7f0c1953e19291adce9b356bfeb877597846115c8fe4211072c1d2b0/merged
overlay          38G   28G  7.7G  79% /var/lib/docker/overlay2/50ee0365ee34634944eb0869f22c0f1e1efd311bd7e2313d46666a9877d556a0/merged
overlay          38G   28G  7.7G  79% /var/lib/docker/overlay2/99ce1acbbc24d4cd46c87a536453a72e5d44fae7abba97801edb775f99b117c2/merged
overlay          38G   28G  7.7G  79% /var/lib/docker/overlay2/9344acb8649dbdea4a20b11641d15b59f5bc2da382fb181d29aeec7571d531ed/merged
overlay          38G   28G  7.7G  79% /var/lib/docker/overlay2/49bcd96cdf5fa1b70beef7826addff01dce2cca3dcf6d1bda60fd08c246bf697/merged
```

Suggestions online seemed to range from reinstalling Docker to re-mapping paperless-ngx's tmp directory.

The fix was far more simple thankfully. Prune the old Docker images! This reclaimed 14GB of disk space!!

```
root@paperless:/home# docker image prune
WARNING! This will remove all dangling images.
Are you sure you want to continue? [y/N] y

Deleted Images:
untagged: apache/tika@sha256:c0154cb95587cde64be74f35ada1a2bd7892219f3f0ac3c9dc6cab34046b3573
deleted: sha256:26fad49b93a54011fb0a5399e8bb4e4c1ff4ece1d890d91773b0469fef418567
[...]

Total reclaimed space: 14.07GB
```

Hopefully this helps someone (or me in future).
