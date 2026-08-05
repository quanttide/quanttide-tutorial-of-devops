# 对象存储：从"另一种网盘"到云原生基础设施

> 学习对象：阿里云 OSS | 学习方式：实战驱动 | 作者：黎想（CTO 办公室）

---

## 〇、学习体验

在接手这个任务之前，我对对象存储的认知就是"另一种网盘"——存文件、取文件，跟百度网盘差不多。

两周后回头看，这个认知被彻底颠覆了。对象存储不是网盘。它是云体系内几乎所有产品的底层基座。它可以作为数据库的存储后端，可以托管静态网站，可以做数据湖的底层，可以跟函数计算、CDN、DNS 无缝打通。

最重要的是：**它不难学。** 每一个单独的操作都很简单——建桶、上传、绑域名、配证书。真正的难点在于把这些零件拼成一套完整的体系。而这正是这份教程试图帮你做到的。

这份教程是"费曼学习法"的产物：我先跟着果总实战做了两周，然后复盘总结，写出来教给你。如果你是第一次接触对象存储，跟着这份教程走一遍，你会在几天内拥有我两周才建立起来的认知。

---

## 一、什么是对象存储

### 1.1 一句话定义

对象存储（Object Storage）是一种扁平化的数据存储架构。不同于文件系统的目录树，它把所有数据都当成**对象**（Object），每个对象有一个唯一的键（Key），以及一组元数据（Metadata）。

```
文件系统：/home/user/documents/report.pdf
对象存储：oss://bucket/report.pdf  （没有目录，只有 key）
```

### 1.2 为什么它如此重要

对象存储是云原生基础设施的"底座"。它可以：

- **作为数据库的后端**：存 Markdown、JSON、CSV，应用程序通过 API 直接读写
- **托管静态网站**：HTML/CSS/JS 文件放上去，配 CDN 就是站点
- **作为数据湖的存储层**：Hudi、Iceberg、Delta Lake 的底层都是对象存储
- **存算分离**：存储和计算独立扩缩，Spark/Flink/Presto 共享同一份数据

现代大数据体系——数据湖、数据仓库、流批一体——几乎都建立在对象存储之上。

### 1.3 核心理念

> **无论存什么，它都当成一个对象。**

这意味着：
- 没有真正的"目录"——你看到的目录结构只是 key 前缀的视觉模拟
- 每个对象自带元数据，可以打标签、设生命周期、控制访问权限
- 天然适合程序化操作——遍历、打标签、批量迁移、自动化治理
- **对象存储 + AI**：让程序理解桶里内容的语义，生成智能索引

---

## 二、量潮怎么用：三类存储桶

量潮的技术体系以对象存储为中心。我们有三类桶，对应三种完全不同的用途：

### 2.1 Private 桶（网盘）

```
qtdata-private     → 业务数据（数据集、客户资料）
qtclass-private    → 课程录屏
qtadmin-private    → 内部管理（会议记录、归档）
```

**用途**：存文件。课程录屏、会议记录、客户交付物、数据集。通过 API 灵活操作，未来可部署到云端或保持本地，数据管理规则不变。

### 2.2 Provider 桶（后台数据库）

```
qtadmin-provider   → 管理后台的数据底座
```

**用途**：作为平台 API 的数据后端。应用程序直接读写桶中的结构化数据（JSON/YAML/Markdown），不经过传统数据库。

### 2.3 Site/Studio 桶（静态网站）

```
qtweb-site         → 官网（React，quanttide.com）
qtfounder-site     → 创始人站（React/Vite，founder.quanttide.com）
qtdata-studio      → 数据云工作台（Flutter Web，data.cloud.quanttide.com）
```

**用途**：托管静态网站。CI/CD 自动构建部署——GitHub Release 触发 → GitHub Actions 构建 → ossutil 上传 → CDN 刷新。

### 2.4 命名规则

| 后缀 | 类型 | 示例 |
|------|------|------|
| `-private` | 业务数据（私有） | `qtdata-private` |
| `-provider` | 平台数据后端 | `qtadmin-provider` |
| `-site` | React/Vite 站点 | `qtweb-site` |
| `-studio` | Flutter Web 应用 | `qtdata-studio` |

规则：`{仓库名}-{类型}`。GitHub 仓库和 OSS 桶一一对齐。

---

## 三、动手：CLI 基础操作

### 3.1 安装和配置

```bash
# 安装阿里云 CLI
curl -o /usr/local/bin/aliyun https://aliyuncli.alicdn.com/aliyun-cli-linux-latest-amd64.tgz
chmod +x /usr/local/bin/aliyun

# 配置凭证
aliyun configure set \
  --access-key-id <YourAK> \
  --access-key-secret <YourSK> \
  --region cn-hangzhou
```

### 3.2 桶操作

```bash
# 创建桶
aliyun oss mb oss://my-bucket

# 列出所有桶
aliyun oss ls

# 查看桶信息
aliyun oss stat oss://my-bucket

# 删除空桶
aliyun oss rm oss://my-bucket -b -f
```

### 3.3 对象操作

```bash
# 上传文件
aliyun oss cp ./local-file.txt oss://my-bucket/

# 递归上传目录
aliyun oss cp ./local-dir/ oss://my-bucket/ -r -f

# 下载
aliyun oss cp oss://my-bucket/file.txt ./local/

# 列出桶内容
aliyun oss ls oss://my-bucket/

# 递归删除（不可逆）
aliyun oss rm oss://my-bucket/ -r -f
```

### 3.4 权限管理

```bash
# 设桶为公共读
aliyun oss set-acl oss://my-bucket public-read --bucket

# 查看 ACL
aliyun oss stat oss://my-bucket | grep ACL
```

> 阿里云有账号级"阻止公共访问"策略。CLI 报错时，控制台通常可直接操作。

---

## 四、进阶：静态网站托管全流程

这是对象存储最复杂的用法——涉及 OSS、CDN、SSL、DNS 四个云产品的协同。

### 4.1 架构图

```
用户 → CDN（HTTPS） → OSS（私有桶）
              ↑
         SSL 证书（Let's Encrypt）
```

### 4.2 完整步骤

**Step 1：建桶 + 开静态托管**
```bash
aliyun oss mb oss://my-site
# 创建 XML 配置：IndexDocument/Suffix=index.html, ErrorDocument/Key=404.html
aliyun oss website --method put oss://my-site website-config.xml
```

**Step 2：上传 + 设公共读**
```bash
npm run build
aliyun oss cp build/ oss://my-site/ -r -f
# 然后在控制台将桶设为公共读
```

**Step 3：加 CDN 域名**
```bash
aliyun cdn AddCdnDomain \
  --DomainName "example.com" \
  --Sources '[{"content":"my-site.oss-cn-hangzhou.aliyuncs.com","type":"oss","port":443,"priority":"20"}]' \
  --CdnType "web" --Scope "domestic"
```
首次添加需在 DNS 加 TXT 记录验证域名所有权。

**Step 4：配 DNS CNAME**
```bash
# CDN 返回 CNAME 如 example.com.w.kunlunaq.com，添加 DNS 记录指向它
aliyun alidns AddDomainRecord --DomainName "example.com" --RR "@" --Type "CNAME" --Value "xxx.w.kunlunaq.com"
```

**Step 5：绑 SSL 证书**
```bash
# 签发 Let's Encrypt 泛域名证书
acme.sh --issue --dns dns_ali -d example.com -d '*.example.com'

# 上传到 CDN
aliyun cdn SetCdnDomainSSLCertificate \
  --DomainName "example.com" \
  --SSLPub "$(cat fullchain.cer)" --SSLPri "$(cat key.pem)" \
  --SSLProtocol "on" --CertType "upload"
```

### 4.3 SSL 证书选型

| 方案 | 费用 | 泛域名 | 自动续期 |
|------|------|--------|---------|
| 阿里云免费证书 | 免费（20张/年） | 不支持 | 需手动重申请 |
| Let's Encrypt + acme.sh | 免费 | 支持 | 全自动 |

**推荐 Let's Encrypt**：一张 `*.quanttide.com` 覆盖全部子域名，GitHub Actions 定时自动续期。

### 4.4 CI/CD 自动化

组织级模板已就绪（`quanttide/.github/workflows/`），任何仓库一行引用即可：

```yaml
jobs:
  deploy:
    uses: quanttide/.github/.github/workflows/deploy-site.yml@main
    with:
      build_dir: dist/
      oss_bucket: my-site
      cdn_domains: '["https://example.com/"]'
    secrets:
      ALIYUN_ACCESS_KEY_ID: ${{ secrets.ALIYUN_ACCESS_KEY_ID }}
      ALIYUN_ACCESS_KEY_SECRET: ${{ secrets.ALIYUN_ACCESS_KEY_SECRET }}
```

触发条件：**GitHub Release published**（不用 main push，防止带 bug 直接上线）。

---

## 五、数据管理：对象存储作为"数据湖"

### 5.1 批量迁移

```bash
# 云到云拷贝（不能并发写同一目标桶）
aliyun oss cp oss://source-bucket/ oss://target-bucket/ -r -f
```

### 5.2 版本控制陷阱

如果桶开了版本控制：
- `rm -r -f` 删掉可见对象后，桶仍非空——每个对象留下了 delete marker
- `aliyun oss ls` 显示 0 对象（不计数 delete marker）
- `rm -b` 报 `BucketNotEmpty`

**解决**：`aliyun oss rm oss://bucket/ -r -f --all-versions`

### 5.3 归档策略

- 不确定用途的历史数据 → `qtadmin-private/archive/`
- 归档管理与 GitHub org `quanttide-archive` 对齐
- 后续建立生命周期策略（自动转冷存、定期清理）

---

## 六、踩坑集锦

1. **`oss cp` 的 `-r` 必须放 URL 后面**：`aliyun oss cp src dest -r -f`（不是 `cp -r src dest`）
2. **云到云拷贝不能并发写同一目标桶**：报 `multiple source url`，必须串行
3. **CLI 设 public-read 被拒**：RAM 用户策略限制 → 控制台可操作
4. **桶私有 → CDN 403**：用户看到空白页。临时方案：桶设公共读；长期方案：配 CDN 回源鉴权
5. **域名归属验证**：首次加 CDN 时必须 DNS 验证（控制台操作）
6. **HTTPS 证书同步延迟**：上传证书后等 1-2 分钟 CDN 节点才生效
7. **版本控制 delete marker**：删桶报 BucketNotEmpty，必须 `--all-versions`
8. **Flutter SDK 版本**：本地 3.24.5 无法构建需要 3.44.8+ 的项目 → CI 自带最新版

---

## 七、学习者视角：我的认知升级路径

接手这个任务时，我的认知分三个阶段快速迭代：

**阶段一："这就是个网盘"** — 建桶、传文件、设权限。以为很简单。

**阶段二："原来可以这样组合"** — 发现 OSS + CDN + SSL + DNS 可以拼出一个完整的生产级静态网站。不是单一产品的能力，是组合的力量。开始理解为什么果总说"对象存储是云原生基础设施的核心"。

**阶段三："代码化才能流转"** — 手动操作的知识只在一个人脑子里。IaC（Infrastructure as Code）把部署流程写成 yml 文件，团队任何人只需要打一个 Git Tag，剩下的交给机器。这才是真正的"知识流转"。

**最重要的一个领悟**：对象存储本身很简单。真正难的是从零摸索出一套以它为中心的云原生 + 大数据 + AI 原生体系。但一旦你通过实战一个任务一个任务地把拼图拼起来，你会发现每一个单独的知识点都不难。费曼说"教是最好的学"，写这篇教程就是我的"教"，希望对你有帮助。

---

## 八、延伸阅读

- [对象存储治理任务大纲](/home/lx/量潮/对象存储治理-任务大纲.md) — 完整实战记录
- [CI 模板](https://github.com/quanttide/.github/tree/main/workflows) — 组织级部署流水线
- [运维手册](https://github.com/quanttide/.github/blob/main/docs/site-ops-handbook.md) — 日常操作参考
- [acme.sh 官方文档](https://github.com/acmesh-official/acme.sh) — SSL 证书自动化
- [阿里云 OSS 文档](https://help.aliyun.com/product/31815.html)