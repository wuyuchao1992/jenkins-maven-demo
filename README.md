###########################################################
# 第一步：只修改这个参数（必填）
###########################################################
BRANCH_NAME="master"    # 你的分支名，确认是master即可

###########################################################
# 第二步：复制整个脚本，在本地仓库根目录执行，无需手动修改
###########################################################
echo "🔍 正在获取所有提交记录，找到第一次敏感提交ID..."
# 获取所有commit ID，按时间排序（第一行就是第一次提交，第二行是第二次）
COMMIT_LIST=$(git log --reverse --pretty=format:"%H")
FIRST_COMMIT=$(echo "$COMMIT_LIST" | head -n 1)  # 第一次敏感提交ID
SECOND_COMMIT=$(echo "$COMMIT_LIST" | head -n 2 | tail -n 1)  # 第二次正常提交ID

echo "✅ 找到敏感提交ID：$FIRST_COMMIT"
echo "✅ 找到第一个正常提交ID：$SECOND_COMMIT"

# 1. 基于第二次提交，创建一个新分支（跳过第一次敏感提交）
git checkout -b temp-branch $SECOND_COMMIT

# 2. 把后续所有提交（从第二次到现在）都 cherry-pick 到新分支
# 这里获取 第二次提交之后的所有commit ID
LATER_COMMITS=$(echo "$COMMIT_LIST" | sed '1,2d')
for commit in $LATER_COMMITS; do
  git cherry-pick $commit
done

# 3. 切换回master分支，强制覆盖为新分支的内容（剔除第一次提交）
git checkout $BRANCH_NAME
git reset --hard temp-branch

# 4. 强制推送到远程，彻底覆盖远程历史（删除第一次敏感提交）
git push -f origin $BRANCH_NAME

# 5. 删除临时分支（清理残留）
git branch -D temp-branch

echo "🎉 操作完成！第一次提交的敏感数据已彻底删除，后续所有提交均保留！"