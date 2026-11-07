1.在后台先手动创建一个 D1 数据库和一个 KV 命名空间，Pages 不会像 Workers 那样自动创建。

2.到 Workers & Pages 选 Create → Pages → Connect to Git，选这个仓库，构建设置如下：
  框架预设 (Framework preset)：None
  构建命令 (Build command)：npm install
  构建输出目录 (Build output directory)：public
  
3.部署后，在项目 Settings → Bindings 里添加 D1，变量名 DB，添加 KV，变量名 KV。Production 和 Preview 建议都加。
   在 Variables and Secrets 里设置 ADMIN_PASSWORD（必填），ADMIN_USERNAME 和 JWT_SECRET 按需设置。
   
  重新部署（Retry deployment），然后访问 /api/health，确认 kvBound 和 adminPasswordSet 都是 true，再去 /admin 登录。


## 备份与恢复（后台右上角「备份」按钮）

- **本地备份**：一键下载 JSON（可选打包图片）；也可从本地 JSON 文件恢复。
- **WebDAV 备份**：填写地址 / 目录 / 用户名 / 密码（坚果云等用「应用密码」），可测试连接、立即备份；密码用 AES-GCM 加密后存库。
- **站内存档**：备份保存到已绑定的 KV（`__backup__/` 前缀，不会通过 /uploads 对外公开）。
- **自动备份**：支持「每天定时 / 每周定时 / 每隔 N 小时」，时间按北京时间；可选保存到 WebDAV 和/或站内存档；可设置保留份数，超出自动删除最旧的。
  - Pages 没有内置定时器：到点后的第一次访问会触发备份；失败会 30 分钟后重试（最多 3 次）。
  - 想要准点：用外部定时服务每 5–10 分钟访问面板里显示的 `/api/backup/cron?token=…`（未到点会自动跳过）。
- **恢复**：会覆盖栏目 / 卡片 / 广告 / 友链 / 壁纸设置（不动登录账号），恢复前自动把当前数据存一份 `pre-restore` 到站内存档以便回滚。
- 不需要新增任何绑定：沿用已有的 D1（`DB`）和 KV（`KV`）。
