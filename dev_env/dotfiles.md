把dotfiles放到Github了。以下是我对thoughtbot rcm工具的理解，没有看过代码，理解可能有误，但是按这个理解看上去能工作。

核心是`DOTFILES_DIRS="$HOME/.dotfiles"`这个配置，在`DOTFILES_DIRS`下的文件就会被rcm管理起来。如果不在`.rcrc`里配置，默认就是`~/.dotfiles`。

以`.zshrc`为例，如果没有建立过dotfiles目录，使用`mkrc .zshrc`会做三件事：

1.  创建.dotfiles目录
2.  以zshrc为文件名，将.zshrc移动到.dotfiles目录
3.  `ln -s ~/.dotfiles/zshrc ~/.zshrc`

如果已经有了dotfiles目录，使用`lsrc`可以看到该目录下的文件以及对应在`$HOME`下的链接。如果在dotfiles里新增了一个文件，使用`rcup`可以把它link到`$HOME`下。

在实际使用中，每个rc文件可以在文件末尾加上类似下面的代码来执行另一个文件中的内容。这主要是方便在多台电脑上支持不同的配置。rcm本身提供一些如按hostname执行代码的功能。但目前感觉没必要用得这么深入，我最主要还是用来同步aliases。
	
	# Local config
	[[ -f ~/.zshrc.local ]] && source ~/.zshrc.local

