# 清除敏感文件

开源前必备的步骤！！！！！在内部项目改造时也时常需要使用以提高安全性。

## 步骤

1. 备份当前文件
2. 命令行运行
```
git filter-branch --force --index-filter 'git rm --cached --ignore-unmatch <file_path>' --prune-empty --tag-name-filter cat -- --all
```
其中`<file_path>`填入当前的目录

3. 重新加入文件到项目目录，执行git add操作。
4. 强制更新
```shell
git push origin --force --all
git push origin --force --tags
```
5. 强制解除对本地存储库中的所有对象的引用和垃圾收集
```
git for-each-ref --format='delete %(refname)' refs/original | git update-ref --stdin
git reflog expire --expire=now --all
git gc --prune=now
```

## 参考资料

- 主要参考：https://blog.csdn.net/q258523454/article/details/83899911