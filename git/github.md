### github api
* 获取用户项目的json数据  [github api owner](https://api.github.com/users/xiaobeicn/repos?type=owner)
* 获取现有的api https://api.github.com/ ，返回值

 ```json
 {
   "current_user_url": "https://api.github.com/user",
   "authorizations_url": "https://api.github.com/authorizations",
   "code_search_url": "https://api.github.com/search/code?q={query}{&page,per_page,sort,order}",
   "emails_url": "https://api.github.com/user/emails",
   "emojis_url": "https://api.github.com/emojis",
   "events_url": "https://api.github.com/events",
   "feeds_url": "https://api.github.com/feeds",
   "following_url": "https://api.github.com/user/following{/target}",
   "gists_url": "https://api.github.com/gists{/gist_id}",
   "hub_url": "https://api.github.com/hub",
   "issue_search_url": "https://api.github.com/search/issues?q={query}{&page,per_page,sort,order}",
   "issues_url": "https://api.github.com/issues",
   "keys_url": "https://api.github.com/user/keys",
   "notifications_url": "https://api.github.com/notifications",
   "organization_repositories_url": "https://api.github.com/orgs/{org}/repos{?type,page,per_page,sort}",
   "organization_url": "https://api.github.com/orgs/{org}",
   "public_gists_url": "https://api.github.com/gists/public",
   "rate_limit_url": "https://api.github.com/rate_limit",
   "repository_url": "https://api.github.com/repos/{owner}/{repo}",
   "repository_search_url": "https://api.github.com/search/repositories?q={query}{&page,per_page,sort,order}",
   "current_user_repositories_url": "https://api.github.com/user/repos{?type,page,per_page,sort}",
   "starred_url": "https://api.github.com/user/starred{/owner}{/repo}",
   "starred_gists_url": "https://api.github.com/gists/starred",
   "team_url": "https://api.github.com/teams",
   "user_url": "https://api.github.com/users/{user}",
   "user_organizations_url": "https://api.github.com/user/orgs",
   "user_repositories_url": "https://api.github.com/users/{user}/repos{?type,page,per_page,sort}",
   "user_search_url": "https://api.github.com/search/users?q={query}{&page,per_page,sort,order}"
 }
 ```

----

### 通过Web界面创建文件夹
由"/"结束创建新文件夹。或者指定文件扩展名，点击提交文件也可创建新文件夹

### 使用Git URL短地址 
使用Github的Git.io。Git.io将会缩短你的项目地址连接。也可以使用gitio命令，Git.io的命令行接口来缩短地址。

### 文件查找
处理新建文件，你还可以在任何存储库快速浏览这些文件。该功能不是太明显，需要快捷方式，在键盘上按"T"激活文件查找功能。在按上下键上下查找。当然，也可以输入文件名称来选择特定的文件 。

### 连接
有时候我们需要在自己的文件项目中分享或者输出特殊行。Github可以实现，我们只需要添加"#L "放在URL行号后面

### 任务清单

Github 扩展了markdown来满足自己的需要，现在你可以在Github上添加一个复选框列表，使用 - [ ] or - [x]来表示一个检查项目。请注意,复选框只会出现在列表项中。"[ ]"必须以"‐"开始。下面有一盒例子：

- [x] create a post.
- [x] create a page.
- [x] add images.
- [ ] published the post.

----







