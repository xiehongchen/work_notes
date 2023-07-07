在master分支增加分支

![git push origin dev2](/Users/mac/Library/Application Support/typora-user-images/image-20230615123058063.png)

git checkout master

git pull

git checkout -b dev2

git push origin dev2

git branch --set-upstream-to=origin/dev2



### 暂存代码，切换分支

git branch  // 查看当前分支

git status //查看修改文件

git stash // 将本地改动暂存到“栈”里面

git checkout master // 切换到master分支

git pull // 拉取master分支最新的代码



当我们再想切换回当前的feature分支

git checkout feature // 切换回到feature分支

git stash show //显示当前放在栈里的文件

git stash pop // 再将刚才暂存到“栈”里面的代码取出来

git stash apply stash@{index}	//取出代码，不过这个不会删除该记录

git status //此时查看就出现了暂存前修改文件

git stash drop stash@{版本}   移除不需要的stash

git stash clear   清空所有的stash

git stash show -p/--patch   查看指定的stash的diff
