# Troubleshoot

## Pods stuck terminating

Symptom: when deleting a pod (or uninstalling a Helm release, which deletes pods) the pods are never removed and are instead stuck in a Terminating state. 
Looking at the events on the pods you see "permission denied" errors related to terminating the docker containers.

Fix:

The following commands seem to unblock the current pods and allow you to delete pods in the future.
```
sudo systemctl disable apparmor.service --now
sudo service apparmor teardown
sudo systemctl restart apparmor
```
