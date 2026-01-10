# Sub-Hub

Sub-Hub 是一个基于 Cloudflare Workers 的代理节点订阅管理系统。它提供了一个直观的 Web 界面，让您可以轻松管理多个订阅和节点。

## 功能特点

- 🚀 支持多种代理协议
  - SS（Shadowsocks）
  - VMess
  - Trojan
  - VLESS（除 Surge 外）
  - SOCKS5
  - Snell（仅 Surge）

- 💼 订阅管理
  - 创建多个独立订阅
  - 自定义订阅路径
  - 支持批量导入节点
  - 节点拖拽排序

- 🔄 多种订阅格式
  - 原始格式（适用于大多数客户端）
  - Base64 编码格式（/v2ray 路径）
  - Surge 配置格式（/surge 路径）

- 🔒 安全特性
  - 管理面板登录认证
  - 会话管理
  - 安全的 Cookie 设置

- 🎨 现代化界面
  - 响应式设计
  - 直观的操作界面
  - 支持移动设备

## 部署教程


### 1. 创建项目

1. 创建名为“sub-hub”新的 Workers 项目


2. 创建名为“sub-hub” 的D1 数据库


3. 将D1数据库与Cloudflare Workers绑定

   变量名称 = "DB"
   数据库名称 = "sub-hub"


### 2. 初始化数据库，在名为“sub-hub” 的D1 数据库“控制台中执行如下代码”

-- 数据库初始化
   ```bash
CREATE TABLE IF NOT EXISTS subscriptions (
  id INTEGER PRIMARY KEY AUTOINCREMENT, 
  name TEXT NOT NULL, 
  path TEXT NOT NULL UNIQUE, 
  sub_order INTEGER DEFAULT 0, 
  updated_at INTEGER, 
  converter_backend TEXT DEFAULT 'sub.xeton.dev'
);

CREATE TABLE IF NOT EXISTS nodes (
  id INTEGER PRIMARY KEY AUTOINCREMENT, 
  subscription_id INTEGER NOT NULL, 
  name TEXT NOT NULL, 
  original_link TEXT NOT NULL, 
  node_order INTEGER NOT NULL DEFAULT 0, 
  enabled INTEGER DEFAULT 1, 
  FOREIGN KEY (subscription_id) REFERENCES subscriptions(id) ON DELETE CASCADE
);

CREATE TABLE IF NOT EXISTS sessions (
  session_id TEXT PRIMARY KEY, 
  username TEXT NOT NULL, 
  expires_at INTEGER NOT NULL
);

CREATE INDEX IF NOT EXISTS idx_subscriptions_path ON subscriptions(path);
CREATE INDEX IF NOT EXISTS idx_nodes_subscription_order ON nodes(subscription_id, node_order);
CREATE INDEX IF NOT EXISTS idx_sessions_expires ON sessions(expires_at);

   ```



### 3. 配置环境变量

在 Cloudflare Dashboard 中设置以下环境变量：

- `ADMIN_PATH`: 管理面板路径（默认：admin）
- `ADMIN_USERNAME`: 管理员用户名（默认：admin）
- `ADMIN_PASSWORD`: 管理员密码（默认：pass）



### 4. 部署代码

1. 将“worker.js”文件内容复制到Cloudflare Workers保存


### 5. 访问系统

1. 访问管理面板：
   ```
   https://你的域名/ADMIN_PATH
   ```

2. 订阅地址格式：
   - 原始格式：`https://你的域名/订阅路径`
   - Base64 格式：`https://你的域名/订阅路径/v2ray`
   - Surge 格式：`https://你的域名/订阅路径/surge`


## 使用说明

### 创建订阅

1. 登录管理面板
2. 点击"添加订阅"按钮
3. 输入订阅名称和路径（路径只能包含小写字母、数字和连字符）
4. 点击"创建"按钮

### 管理节点

1. 在订阅列表中找到目标订阅
2. 点击"添加节点"按钮添加新节点
3. 支持以下格式：
   - 单个节点链接
   - 多个节点链接（每行一个）
   - Base64 编码的节点列表

### 节点排序

1. 点击"节点列表"按钮查看节点
2. 拖拽节点行可以调整顺序
3. 顺序会自动保存

### 批量操作

1. 点击"批量删除"按钮进入批量模式
2. 勾选要删除的节点
3. 点击"确认删除"执行删除操作

## 注意事项

1. 首次部署后请立即修改默认的管理员密码
2. 定期备份数据库内容
3. 妥善保管管理面板地址和登录信息
4. 建议使用强密码提高安全性

## 技术栈

- Cloudflare Workers
- Cloudflare D1 (SQLite)
- HTML5 + CSS3
- JavaScript (ES6+)
- Bootstrap 5
- Font Awesome
- SortableJS

