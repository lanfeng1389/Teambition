# Kopia 备份系统配置说明文档

以下是对当前 Kopia 系统备份策略和存储状态的详细解释，旨在帮助首次使用 Kopia 的用户快速了解系统的备份情况。

---

## 一、全局备份策略 (`**kopia policy get --global**`)

### 1. 数据保留策略 (Retention)

| **参数** | **值** | **含义** |
| --- | --- | --- |
| Annual snapshots | 3 | 每年最多保留 3 个年度快照。 |
| Monthly snapshots | 24 | 每月最多保留 24 个月度快照。 |
| Weekly snapshots | 4 | 每周最多保留 4 个周度快照。 |
| Daily snapshots | 7 | 每天最多保留 7 个日度快照。 |
| Hourly snapshots | 48 | 每小时最多保留 48 个时度快照。 |
| Latest snapshots | 10 | 最新快照最多保留 10 个版本。 |
| Ignore identical snapshots | false | 是否忽略完全相同的快照（`false` 表示不忽略）。 |

**总结**：系统会根据时间粒度（年、月、周、日、小时）保留一定数量的快照，并确保最新的 10 个快照始终被保留。

---

### 2. 文件策略 (Files Policy)

| **参数** | **值** | **含义** |
| --- | --- | --- |
| Ignore cache directories | true | 是否忽略缓存目录（`true` 表示忽略）。 |
| Ignore rules | `/teambition/db/kafka-cluster/conf<br>/teambition/db/kafka-cluster/data<br>/teambition/db/kafka-zookeeper/data` | 忽略指定路径下的文件或目录。 |
| Read ignore rules from files | `.kopiaignore` | 从 `.kopiaignore` 文件中读取忽略规则。 |
| Scan one filesystem only | false | 是否仅扫描单个文件系统（`false` 表示可以跨文件系统扫描）。 |

**总结**：系统会忽略指定的缓存目录和路径，并通过 `.kopiaignore` 文件定义额外的忽略规则。

---

### 3. 错误处理策略 (Error Handling Policy)

| **参数** | **值** | **含义** |
| --- | --- | --- |
| Ignore file read errors | false | 是否忽略文件读取错误（`false` 表示不忽略）。 |
| Ignore directory read errors | false | 是否忽略目录读取错误（`false` 表示不忽略）。 |
| Ignore unknown types | true | 是否忽略未知类型的文件或目录（`true` 表示忽略）。 |

**总结**：系统会严格处理文件和目录的读取错误，但会忽略未知类型的文件或目录。

---

### 4. 调度策略 (Scheduling Policy)

| **参数** | **值** | **含义** |
| --- | --- | --- |
| Scheduled snapshots | None | 当前未设置自动调度快照任务。 |
| Manual snapshot | false | 是否允许手动触发快照（`false` 表示不允许）。 |

**总结**：当前系统未启用自动调度快照功能，也无法手动触发快照。

---

### 5. 上传设置 (Uploads)

| **参数** | **值** | **含义** |
| --- | --- | --- |
| Max parallel snapshots (server/UI) | 1 | 并发快照的最大数量为 1。 |
| Max parallel file reads | \- | 文件并发读取数量无限制。 |
| Parallel upload above size | 2.1 GB | 文件大小超过 2.1 GB 时启用并行上传。 |

**总结**：系统限制了并发快照的数量为 1，但对于文件读取和上传设置了灵活的规则。

---

### 6. 压缩设置 (Compression)

| **参数** | **值** | **含义** |
| --- | --- | --- |
| Compressor | deflate-best-compression | 使用 `deflate-best-compression` 压缩算法。 |
| Compress files regardless of extensions | \- | 无论文件扩展名如何，均对文件进行压缩。 |
| Compress files of all sizes | \- | 对所有大小的文件均进行压缩。 |

**总结**：系统会对所有文件进行压缩，以减少存储空间占用。

---

### 7. 日志记录 (Logging Details)

| **参数** | **值** | **含义** |
| --- | --- | --- |
| Directory snapshotted | 5 | 目录被快照时的日志级别为 5（信息级）。 |
| Directory ignored | 5 | 忽略目录时的日志级别为 5（信息级）。 |
| Entry snapshotted | 0 | 条目被快照时的日志级别为 0（无记录）。 |
| Entry ignored | 5 | 忽略条目时的日志级别为 5（信息级）。 |
| Entry cache hit | 0 | 缓存命中时的日志级别为 0（无记录）。 |
| Entry cache miss | 0 | 缓存未命中时的日志级别为 0（无记录）。 |

**总结**：系统会在关键操作（如目录快照或忽略）时记录日志，但不会记录每个条目的详细信息。

---

## 二、存储库状态 (`**kopia repository status**`)

### 1. 存储库基本信息

| **参数** | **值** | **含义** |
| --- | --- | --- |
| Config file | /root/.config/kopia/repository.config | 配置文件路径。 |
| Description | Repository in Filesystem: /teambition/backup/master1 | 存储库位于文件系统路径 `/teambition/backup/master1`。 |
| Hostname | master1 | 主机名为 `master1`。 |
| Username | root | 用户名为 `root`。 |
| Read-only | false | 存储库是否只读（`false` 表示可写）。 |

---

### 2. 存储容量与配置

| **参数** | **值** | **含义** |
| --- | --- | --- |
| Storage type | filesystem | 存储类型为文件系统。 |
| Storage capacity | 422.6 GB | 存储总容量为 422.6 GB。 |
| Storage available | 171.3 GB | 可用存储空间为 171.3 GB。 |
| Storage config | `{...}` | 存储路径及权限配置详情。 |

---

### 3. 加密与压缩

| **参数** | **值** | **含义** |
| --- | --- | --- |
| Hash | BLAKE2B-256-128 | 使用 BLAKE2B 哈希算法。 |
| Encryption | AES256-GCM-HMAC-SHA256 | 使用 AES256-GCM-HMAC-SHA256 加密算法。 |
| Content compression | true | 是否启用内容压缩（`true` 表示启用）。 |

---

### 4. Epoch 管理

| **参数** | **值** | **含义** |
| --- | --- | --- |
| Epoch Manager | enabled | Epoch 管理功能已启用。 |
| Current Epoch | 13 | 当前 Epoch 编号为 13。 |
| Epoch refresh frequency | 20m0s | Epoch 刷新频率为 20 分钟。 |
| Epoch advance on | 20 blobs or 10.5 MB, minimum 24h0m0s | 当达到 20 个 blob 或 10.5 MB 数据时，Epoch 自动推进，最小间隔为 24 小时。 |
| Epoch cleanup margin | 4h0m0s | Epoch 清理的时间间隔为 4 小时。 |
| Epoch checkpoint every | 7 epochs | 每 7 个 Epoch 创建一个检查点。 |