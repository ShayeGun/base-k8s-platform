ansible, ansible-core, nerdctl
ansible collection ansible.posix

---
Backup:

kubectl -n apisix-etcd create job \
  --from=cronjob/etcd-snapshot-scheduler \
  etcd-snapshot-test

---

Errors:

1.
while joining worker to cluster got:

err: tls: failed to verify certificate: x509: certificate has expired or is not yet valid:
current time 2026-10-23T13:31:52Z is before 2026-10-23T15:23:04Z

reason: because vms weren't connect to internet and didnt have a local NTP so their time drift and had to manually correct it

cmd: sudo date -u -s "$(ssh ubuntu@manager1 'date -u "+%Y-%m-%d %H:%M:%S"')"

2. coredns couldnt forward dns to other server

err: ???

reason: because created local lan without a gateway or dns so by default coredns tries to forward qieries to host 127.0.0.53 and it also didnt know the lan

cmd: remove the roward part from codedns config map (available in playbook)
