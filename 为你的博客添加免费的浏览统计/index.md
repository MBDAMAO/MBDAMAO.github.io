# 为你的博客添加免费的浏览统计


<!--more-->

## 初始化远程数据库

```shell
npx wrangler d1 execute cfviews_db --remote --file=./schema.sql
```

您将会看到如下输出

```shell
cfviews on  main [✘!] via  v20.12.2 took 15s
❯ npx wrangler d1 execute cfviews_db --remote --file=./schema.sql

 ⛅️ wrangler 3.86.1 (update available 3.90.0)
-------------------------------------------------------

√ ⚠️ This process may take some time, during which your D1 database will be unavailable to serve queries.
  Ok to proceed? ... yes
🌀 Executing on remote database cfviews_db (bef5ccbc-5985-4eb6-8fa9-885f7d74b177):
🌀 To execute on your local development database, remove the --remote flag from your wrangler command.
Note: if the execution fails to complete, your DB will return to its original state and you can safely retry.
├ 🌀 Uploading bef5ccbc-5985-4eb6-8fa9-885f7d74b177.c263018675e99df5.sql
│ 🌀 Uploading complete.
│
🌀 Starting import...
🌀 Processed 1 queries.
🚣 Executed 1 queries in 0.00 seconds (1 rows read, 3 rows written)
   Database is currently at bookmark 00000003-00000005-00004e54-12824d4ae3a8377626107f7f1d320578.
┌────────────────────────┬───────────┬──────────────┬────────────────────┐
│ Total queries executed │ Rows read │ Rows written │ Database size (MB) │
├────────────────────────┼───────────┼──────────────┼────────────────────┤
│ 1                      │ 1         │ 3            │ 0.02               │
└────────────────────────┴───────────┴──────────────┴────────────────────┘
```

## 部署到远程

```shell
cfviews on  main [✘!] via  v20.12.2
❯ npm run deploy

> deploy
> wrangler deploy --minify src/index.ts


 ⛅️ wrangler 3.86.1 (update available 3.90.0)
-------------------------------------------------------

Total Upload: 22.75 KiB / gzip: 9.00 KiB
Your worker has access to the following bindings:
- KV Namespaces:
  - KV: 3243086648f045868df62bf34066fab6
- D1 Databases:
  - DB: cfviews_db (bef5ccbc-5985-4eb6-8fa9-885f7d74b177)
- Vars:
  - DOMAIN: "https://damaoz.site"
  - PAGESIZE: 10
Uploaded cfviews (2.44 sec)
Deployed cfviews triggers (4.05 sec)
  https://cfviews.mbdamao.workers.dev
Current Version ID: 348a32b4-4e78-48f6-a67f-cb13580ee054
```

