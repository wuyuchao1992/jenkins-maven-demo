BRANCH_NAME="master"
# 1. 获取第一次敏感提交ID
FIRST_COMMIT=$(git log --reverse --pretty=format:"%H" | head -n 1)
# 2. 交互式rebase，基于第一次提交之前（空白节点）重构历史
git rebase -i --root $FIRST_COMMIT

# ✅ 执行上一行后，会弹出编辑器，操作如下：
# 1. 找到第一次提交的那一行（开头是 pick）
# 2. 把这一行的 pick 改成 drop （表示删除这个提交）
# 3. 后续所有提交的 pick 都保留不变
# 4. 保存退出编辑器（vim：按 ESC → 输入 :wq → 回车）

# 3. 若rebase过程中出现冲突，解决冲突后执行：
# git add .
# git rebase --continue

# 4. 强制推送到远程，覆盖历史
git push -f origin $BRANCH_NAME
echo "✅ 第一次敏感commit已删除，代码和后续提交全保留！"