# 使用Token访问Git仓库

可用Token包括：

- 个人令牌，在Coding的个人设置中生成，代替个人账号的用户名和密码。
- 项目令牌，在项目设置的项目令牌中生成，拥有这个项目的访问权限。
- 临时令牌，在CI中系统自动生成的临时令牌，使用以后销毁。

访问格式：

```url
https://${PROJECT_TOKEN_GK}:${PROJECT_TOKEN}@e.coding.net/<company-name>/${PROJECT_NAME}/${DEPOT_NAME}.git
```

其中，<company-name>在我们这里是`quanttide`，其他变量在环境变量中有说明。

## 个人令牌

https://help.coding.net/docs/member/tokens.html

在“个人账户设置>访问令牌”中获取和设置。

代替个人账号的用户名和密码，拥有个人账号的权限。

## 项目令牌

https://help.coding.net/docs/project-settings/deploy-tokens.html

在“开发者设置>项目令牌”获取和设置。

设置时注意：
- 过期时间必须设置，设置一个比较久远的时间（比如2030年）就可以当成永久令牌使用。
- 手动授权有限的代码仓库、制品库和其他功能模块，以防止非必要授权带来的安全风险。

设置后一般来说要手动放到凭据管理中才能被CI使用。

## 临时令牌

https://help.coding.net/docs/ci/configuration/env.html

每次 CI 执行的时候都会自动生成的临时令牌，使用完毕后会自动销毁。
文档没有直接写明，在环境变量中提到。

- `PROJECT_TOKEN_GK`：临时令牌用户名
- `PROJECT_TOKEN`：临时令牌密钥

注意，此令牌没有写的权限，不能提交，需要换有权限的项目令牌。
