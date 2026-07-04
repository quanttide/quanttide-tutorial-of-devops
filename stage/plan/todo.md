# TODO

## 核心理念

ROADMAP 定目标，TODO 拆动作。

ROADMAP 说"要做什么"——增加用户认证模块、重构 API 路由。TODO 说"具体怎么做"——代码改哪里、测试补哪里、文档写哪里。

```text
ROADMAP:  [ ] 增加用户认证模块
TODO:     [ ] auth: 新增 login/logout 路由
          [ ] auth: 编写登录流程单元测试
          [ ] auth: 补充 JWT 中间件
          [ ] auth: 更新 API 文档
```

TODO 的关键是精确。每一条都能直接对应到文件或模块，让执行者拿到就能干，不需要再拆。

## 格式

```markdown
# TODO

## auth

- [ ] 新增 login/logout 路由（routes/auth.rs）
- [ ] 编写登录流程单元测试（tests/auth_test.rs）
- [ ] 补充 JWT 中间件（middleware/jwt.rs）
- [ ] 更新 API 文档（docs/api/auth.md）

## api

- [ ] 重构路由注册方式，改用宏自动注册
- [ ] 补充 400/500 错误处理测试
```

按功能模块分组，每条标注涉及的文件。

## TODO 与 ROADMAP 的对应

ROADMAP 的条目完成时，对应的 TODO 应全部打勾：

| ROADMAP | TODO |
|---------|------|
| 增加用户认证 | auth 分组下的 4 条 |
| 提升测试覆盖率 | api 分组下的 2 条 |

ROADMAP 发布后，已完成的 TODO 随版本归档或清理。
