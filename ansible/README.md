# Ansible Operational Notes

## Execution Host Dependencies

- `ansible` / `ansible-core`
- `ansible.posix` collection
- `nerdctl` (used on the online preparation host for pulling and archiving images)

---

## Manual etcd Backup Verification

Trigger an on-demand snapshot job from the scheduled CronJob in `apisix-etcd`:

```bash
kubectl -n apisix-etcd create job \
  --from=cronjob/etcd-snapshot-scheduler \
  etcd-snapshot-test
```

---

## Troubleshooting Notes

### 1. Certificate time validation failure during node join

**Symptom:**
```text
err: tls: failed to verify certificate: x509: certificate has expired or is not yet valid:
current time 2026-10-23T13:31:52Z is before 2026-10-23T15:23:04Z
```

**Cause:**
Target VMs in an isolated environment without an accessible local NTP server drifted out of clock synchronization.

**Workaround:**
Synchronize the node clock against the initial manager before joining:
```bash
sudo date -u -s "$(ssh ubuntu@manager0 'date -u "+%Y-%m-%d %H:%M:%S"')"
```

### 2. CoreDNS forwarding failures in isolated LANs

**Symptom:**
CoreDNS fails to forward queries when the isolated network has no upstream gateway or DNS resolver, causing lookup timeouts via `127.0.0.53`.

**Resolution:**
The `tasks/30-k8s/10-initialize/coredns.yml` task patches the `coredns` ConfigMap in `kube-system` to remove external `forward` rules in disconnected environments.
