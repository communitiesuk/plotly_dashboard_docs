## Useful Cloud Foundry commands

Get environment variables:
```bash
cf env APP_NAME
```

---

Get all service keys for a service

```bash
cf service-keys SERVICE_NAME
```

---

Get service key credentials
```bash
cf service-key SERVICE_NAME SERVICE_KEY
```

---

Get most recent logs. Useful when a deployment fails.
```bash
cf logs APP_NAME --recent
```