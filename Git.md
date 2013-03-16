## GIT分支操作说明

相关资料

* http://github.github.com/github-flavored-markdown/
* https://github.com/github/markup#readme
* http://wowubuntu.com/markdown/

开Branch 最大的好處除了可以不影响 stable 和其他分支版本的开发，另一个超棒的地方是”你可以決定 Merge 的方式”。
Git 的 Merge 方式可以分成四種：

* Straight merge 預設的合併模式，會有全部的被合併的 branch commits 記錄加上一個 merge-commit，看線圖會有兩條 Parents 線，並保留所有 commit log。
* Squashed commit 壓縮成只有一個 merge-commit，不會有被合併的 log。SVN 的 merge 即是如此。
* cherry-pick 只合併指定的 commit
* rebase 變更 branch 的分支點：找到要合併的兩個 branch 的共同的祖先，然後先只用要被 merge 的 branch 來 commit 一遍，然後再用目前 branch 再 commit 上去。這方式僅適合還沒分享給別人的 local branch，因為等於砍掉重練 commit log



###删除远程分支

	$ git push origin :<your-branch>

###建立本地 local branch

	$ git branch <new_branch_name>

###改名字 (如果有同名會失敗，改用 -M 可以強制覆蓋)

	$ git branch -m <old_name> <new_name>

###列出目前有那些 branch 以及目前在那個 branch

	$ git branch

###切換 branch (注意到如果你有檔案修改了卻還沒 commit，會不能切換 branch，解法稍後會談)

	git checkout <branch_name> 

###(<from_branch_name>) 本地建立 branch 並立即 checkout 切換過去

	git checkout -b <new_branch_name> 

###刪除 local branch

	git branch -d <branch_name> 

###合併另一個 branch，若沒有 conflict 衝突會直接 commit。若需要解決衝突則會再多一個 commit。

	git merge <branch_name>

###將另一個 branch 的 commit 合併為一筆，特別適合需要做實驗的 fixes bug 或 new feature，最後只留結果。合併完不會幫你先 commit。

	git merge --squash <branch_name>
	