# Git版本控制系统

Git是目前世界上最先进的分布式版本控制系统，并且是由linus大神开发，可免费使用。目前在各大公司都被广泛应用，所以掌握好Git是必不可少的技能。

## 基本概念

要学好Git就必须对一些基本概念有一个清晰的认识。下面我们先理一下Git相关的概念。

### 版本库

版本库（repository），又叫仓库，是git管理的一个目录，可以记录、管理该目录内的文件及其内容的增删改。Git记录、管理的文件内容的信息都保存在版本库根目录的名为.git的隐藏文件夹中。Git与SVN不同，Git按照元数据存储信息，SVN按照文件存储信息；Git关心文件是否发生变化，如果发生变化则记录修改后文件的快照的，svn记录哪些文件发生了什么变化（这种策略的不同是因为Git是分布式的，svn是集中式的）。Git和svn的存储机制分别如下面两张图所示。

那么如何把一个普通的目录变成Git版本库呢？可以在这个目录中跑`git init`命令，那么Git就会在这个目录中创建一个.git的隐藏目录，进而记录该目录中文件的快照。

### 工作区/暂存区

在Git中，文件有三种状态：已经修改的（modified）、已经暂存的（staged）、已经提交的（committed）。不同的文件也位于不同的区：工作区、暂存区、版本库。在版本库中，记录的是版本库建立开始提交的所有的版本快照；在暂存区，记录的是要提交到版本库的文件内容的快照，可以理解为提交版本库前的缓存区；工作区即用户目前正在做的修改，如果不提交到暂存区，Git不会保存文件内容的快照。用户在工作区对文件进行修改，通过`git add`命令将工作区的文件提交到暂存区，如果该内容没有问题，可当作一个新的版本吧，则再通过`git commit`命令将暂存区的文件提交到版本库。

### 远程仓库

虽然Git是分布式的，但是仍然有远程仓库的概念。与svn不同，Git远程仓库和本地仓库都是完整的版本库，只是将两个版本库建立起了链接而已。远程仓库一般用于担任核心仓库的功能，也就是在多人开发时，拉取代码从远程仓库克隆，推送代码也是推送到远程仓库，项目要发布的代码也是通过远程仓库来获取，所以，尽量保证推送到远程仓库的是该版本最终的代码。

### 分支

分支在实际的项目开发中占据这重要的位置，当我们需要开发一个新的功能时，就需要创建一个分支，在分支上开发就不会影响到主干代码，功能开发完成后在合并到主干就好。
