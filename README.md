# gitBranchManagerDescript
git分支管理最佳实现
master：作为发布分支，随时可以将分支上的代码部署到生产环境上。如果在生产环境上发现问题，则以 master 为基准创建 hotfix/* 分支来修复问题；
develop：作为开发分支，所有最新的功能都将在该分支下进行开发，develop 也将是所有分支中功能最全，代码最新的一个分支；
feature/*：命名规则feature/功能名称，作为新功能的开发分支，该分支从 develop 创建，开发完毕之后需要重新合并到 develop；
release/*：命名规则release/v+发布的版本号，作为预发布分支，release/* 只能从 develop 创建，且在 git flow 中同一个时间点，只能存在一个预发布分支。只有当上一个版本发布成功之后删除该分支，之后才能进行下一个版本的发布。如果在预发布过程中发现了问题，只能在 release/* 分支上进行修改；
hotfix/*：命名规则hotfix/v+bug修复的版本号，作为热修复分支，只能从 master 分支分离出来。主要是用来修复在生产环境上发现的 bug，修复完成并测试通过后需要将该分支合并回 develop 及 master 上，并删除该分支；
