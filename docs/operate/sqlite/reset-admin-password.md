---
id: reset-admin-password
title: Reset Admin Password
sidebar_label: Reset Admin Password
---

In case you have forgotten the root admin password, you can reset it
by removing all users and organisation from the SQLite database.

The guide mainly covers the following:
- Exec into the `query-service` container
- Remove all users and organisation using SQLite shell
- Restart the `query-service` container

## Exec into `query-service` Container

### Docker Standalone

```bash
docker exec -it query-service sh
```

### Docker Swarm

```bash
docker exec -it $(docker ps -q -f name=signoz_query-service) sh
```

:::info
Replace `signoz` with your Docker stack name.
:::

### Kubernetes

```bash
kubectl -n platform exec -it pod/my-release-signoz-query-service-0 -- sh
```

:::info
Replace `my-release` with your Helm release name. And `platform` with your
SigNoz namespace.
:::

## Steps to Remove All Users and Organisation

### Step 1: Install SQLite and Connect to the Database File

```bash
apk update
apk add sqlite

sqlite3 /var/lib/signoz/signoz.db
```

### Step 2: Remove All Users and Organisation

In the sqlite shell, run the following commands:

```bash
select * from users;
select * from organizations;

delete from users;
delete from organizations;
```

### Step 3: Verify the Users and Organisation are Removed

```bash
select * from users;

select * from organizations;
```

### Step 4: Exit from SQLite Shell and Container Shell

To exit from the sqlite shell, press `CTRL + D` and then exit from the
`query-service` container.

### Step 5: Restart the `query-service` Container

To restart the `query-service` container, run the following command:

**For Docker Users**

```bash
docker restart query-service
```

**For Docker Swarm Users**

```bash
docker service update --force signoz_query-service
```

**For Kubernetes Users**

```bash
kubectl -n platform rollout restart statefulset/my-release-signoz-query-service
```

Now, you should be able to create a new admin account from SigNoz UI: `http://localhost:3301`.

![Reset Admin Password](/img/docs/sqlite-reset-admin-password.png)

---
