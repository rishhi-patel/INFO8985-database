# OpenTelemetry Collector Configuration Explanation

This configuration adds two important receivers to the OpenTelemetry Collector:

```yaml
sqlquery:
  driver: postgres
  datasource: "host=db port=5432 user=devtedsuser password=devtedspass sslmode=disable database=lcbo"
  queries:
    - sql: "select count(*) as count FROM inventories WHERE product_id = 18 AND quantity = 0"
      metrics:
        - metric_name: "lcbo_inv_count"
          value_column: "count"

postgresql:
  endpoint: db:5432
  transport: tcp
  username: devtedsuser
  password: devtedspass
  collection_interval: 10s
  tls:
    insecure: true
```

### Explanation

* **sqlquery receiver:**
  Connects to the PostgreSQL database and runs the specified SQL query periodically. Here, it counts how many inventory records have product ID 18 with zero quantity. The result (`count`) is collected as a custom metric named `lcbo_inv_count`. This allows tracking specific business metrics directly from the database.

* **postgresql receiver:**
  Collects standard PostgreSQL performance and health metrics (e.g., cache hits, connections) every 10 seconds from the same database. TLS is disabled (`insecure: true`) to simplify connectivity in this setup.

Together, these receivers provide both custom business insight metrics and general PostgreSQL operational metrics for monitoring.


![image](https://github.com/user-attachments/assets/950d550a-d72a-4d0e-b937-de4fa4928de2)


lcbo database with signoz demo

```bash
ansible-playbook up.yml
```

This does docker compose up on an "empty" signoz in a codespace. Use this as a starting point for instrumenting your app. It also creates a postgresql/pgadmin container with an old version of the LCBO database in it.

```bash
ansible-playbook down.yml
```

This does docker compose down on the `docker-compose.yml` (the same docker-compose file from up.yml)
