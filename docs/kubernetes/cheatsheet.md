### Purge failed pods
```
kubectl delete pods -A --field-selector=status.phase=Failed
```