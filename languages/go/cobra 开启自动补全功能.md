# cobra 如何开启自动补全功能

因工作原因，需要将一个由 [cobra](https://github.com/spf13/cobra) 写的命令行工具，支持在 bash 和 zsh 环境开启命令行自动补全功能。网上搜了一圈，大部分都是把 cobra github 的介绍翻译一下就完了。而且没有对命令行补全的功能进行讲解。所以就写一篇文章补充一下。

- cobra 基础：不单独介绍，网络很多的，也可以参考 [Golang之使用Cobra](https://o-my-chenjian.com/2017/09/20/Using-Cobra-With-Golang/)

## 前提

- 初始化 cobra 项目：

```shell
mkdir sulenn-cobra && cobra init --pkg-name github.com/sulenn/sulenn-cobra
# 开启 go module
go mod init github.com/sulenn/sulenn-cobra
# 修改文件名
mv main.go sulenn-cobra.go
```

- 添加若干命令

```shell
cobra add serve
cobra add create
cobra add hello
cobra add completion
```

- 查看命令

```shell
# 编译
 go build sulenn-cobra
 # 查看 help
 > ./main -h
 
A longer description that spans multiple lines and likely contains
examples and usage of using your application. For example:

Cobra is a CLI library for Go that empowers applications.
This application is a tool to generate the needed files
to quickly create a Cobra application.

Usage:
  sulenn-cobra [command]

Available Commands:
  completion  A brief description of your command
  create      A brief description of your command
  hello       A brief description of your command
  help        Help about any command
  serve       A brief description of your command

Flags:
      --config string   config file (default is $HOME/.sulenn-cobra.yaml)
  -h, --help            help for sulenn-cobra
  -t, --toggle          Help message for toggle

Use "sulenn-cobra [command] --help" for more information about a command.
```

## 开启命令行自动补全

### 开启 bash 命令行自动补全

- 修改 completion.go 文件内容：

```shell
/*
Copyright © 2020 sulenn <273409891@qq.com>
This file is part of CLI application foo.
*/
package cmd

import (
	"github.com/spf13/cobra"
	"os"
)

// completionCmd represents the completion command
var completionCmd = &cobra.Command{
	Use:                   "completion [bash|zsh]",
	Short:                 "Generate completion script",
	Long: `To load completions:

Bash:

$ source <(yourprogram completion bash)

# To load completions for each session, execute once:
Linux:
  $ yourprogram completion bash > /etc/bash_completion.d/yourprogram
MacOS:
  $ yourprogram completion bash > /usr/local/etc/bash_completion.d/yourprogram

Zsh:

$ source <(yourprogram completion zsh)

# To load completions for each session, execute once:
$ yourprogram completion zsh > "${fpath[1]}/_yourprogram"
`,
	DisableFlagsInUseLine: true,
	ValidArgs:             []string{"bash", "zsh"},
	Args:                  cobra.ExactValidArgs(1),
	Run: func(cmd *cobra.Command, args []string) {
		switch args[0] {
		case "bash":
			cmd.Root().GenBashCompletion(os.Stdout)
		case "zsh":
			cmd.Root().GenZshCompletion(os.Stdout)
		}
	},
}

func init() {
	rootCmd.AddCommand(completionCmd)

	// Here you will define your flags and configuration settings.

	// Cobra supports Persistent Flags which will work for this command
	// and all subcommands, e.g.:
	// completionCmd.PersistentFlags().String("foo", "", "A help for foo")

	// Cobra supports local flags which will only run when this command
	// is called directly, e.g.:
	// completionCmd.Flags().BoolP("toggle", "t", false, "Help message for toggle")
}

```

- 开启补全

```shell
# 编译
go build main.go
# bash 环境下执行
./sulenn-cobra completion bash > cobra_completion && sudo mv cobra_completion /etc/bash_completion.d/
```

- 重开 bash 即可使用命令行自动补全

### 开启 zsh 命令行自动补全

- 修改 completion.go 文件内容：

```shell
/*
Copyright © 2020 sulenn <273409891@qq.com>
This file is part of CLI application foo.
*/
package cmd

import (
	"bytes"
	"fmt"
	"github.com/spf13/cobra"
	"io"
	"os"
)

// completionCmd represents the completion command
var completionCmd = &cobra.Command{
	Use:                   "completion [bash|zsh]",
	Short:                 "Generate completion script",
	Long: `To load completions:

Bash:

$ source <(yourprogram completion bash)

# To load completions for each session, execute once:
Linux:
  $ yourprogram completion bash > /etc/bash_completion.d/yourprogram
MacOS:
  $ yourprogram completion bash > /usr/local/etc/bash_completion.d/yourprogram

Zsh:

$ source <(yourprogram completion zsh)

# To load completions for each session, execute once:
$ yourprogram completion zsh > "${fpath[1]}/_yourprogram"
`,
	DisableFlagsInUseLine: true,
	ValidArgs:             []string{"bash", "zsh"},
	Args:                  cobra.ExactValidArgs(1),
	Run: func(cmd *cobra.Command, args []string) {
		switch args[0] {
		case "bash":
			cmd.Root().GenBashCompletion(os.Stdout)
		case "zsh":
			err := runCompletionZsh(os.Stdout, cmd.Root())
			if err != nil {
				fmt.Printf("completion for zsh failed: %v\n", err)
				return
			}
		}
	},
}

func init() {
	rootCmd.AddCommand(completionCmd)

	// Here you will define your flags and configuration settings.

	// Cobra supports Persistent Flags which will work for this command
	// and all subcommands, e.g.:
	// completionCmd.PersistentFlags().String("foo", "", "A help for foo")

	// Cobra supports local flags which will only run when this command
	// is called directly, e.g.:
	// completionCmd.Flags().BoolP("toggle", "t", false, "Help message for toggle")
}

func runCompletionZsh(out io.Writer,cmd *cobra.Command) error {
	zshHead := "#compdef kubectl\n"

	_, err := out.Write([]byte(zshHead))
	if err != nil {
		return err
	}

	zshInitialization := `
__kubectl_bash_source() {
	alias shopt=':'
	emulate -L sh
	setopt kshglob noshglob braceexpand

	source "$@"
}

__kubectl_type() {
	# -t is not supported by zsh
	if [ "$1" == "-t" ]; then
		shift

		# fake Bash 4 to disable "complete -o nospace". Instead
		# "compopt +-o nospace" is used in the code to toggle trailing
		# spaces. We don't support that, but leave trailing spaces on
		# all the time
		if [ "$1" = "__kubectl_compopt" ]; then
			echo builtin
			return 0
		fi
	fi
	type "$@"
}

__kubectl_compgen() {
	local completions w
	completions=( $(compgen "$@") ) || return $?

	# filter by given word as prefix
	while [[ "$1" = -* && "$1" != -- ]]; do
		shift
		shift
	done
	if [[ "$1" == -- ]]; then
		shift
	fi
	for w in "${completions[@]}"; do
		if [[ "${w}" = "$1"* ]]; then
			echo "${w}"
		fi
	done
}

__kubectl_compopt() {
	true # don't do anything. Not supported by bashcompinit in zsh
}

__kubectl_ltrim_colon_completions()
{
	if [[ "$1" == *:* && "$COMP_WORDBREAKS" == *:* ]]; then
		# Remove colon-word prefix from COMPREPLY items
		local colon_word=${1%${1##*:}}
		local i=${#COMPREPLY[*]}
		while [[ $((--i)) -ge 0 ]]; do
			COMPREPLY[$i]=${COMPREPLY[$i]#"$colon_word"}
		done
	fi
}

__kubectl_get_comp_words_by_ref() {
	cur="${COMP_WORDS[COMP_CWORD]}"
	prev="${COMP_WORDS[${COMP_CWORD}-1]}"
	words=("${COMP_WORDS[@]}")
	cword=("${COMP_CWORD[@]}")
}

__kubectl_filedir() {
	# Don't need to do anything here.
	# Otherwise we will get trailing space without "compopt -o nospace"
	true
}

autoload -U +X bashcompinit && bashcompinit

# use word boundary patterns for BSD or GNU sed
LWORD='[[:<:]]'
RWORD='[[:>:]]'
if sed --help 2>&1 | grep -q 'GNU\|BusyBox'; then
	LWORD='\<'
	RWORD='\>'
fi

__kubectl_convert_bash_to_zsh() {
	sed \
	-e 's/declare -F/whence -w/' \
	-e 's/_get_comp_words_by_ref "\$@"/_get_comp_words_by_ref "\$*"/' \
	-e 's/local \([a-zA-Z0-9_]*\)=/local \1; \1=/' \
	-e 's/flags+=("\(--.*\)=")/flags+=("\1"); two_word_flags+=("\1")/' \
	-e 's/must_have_one_flag+=("\(--.*\)=")/must_have_one_flag+=("\1")/' \
	-e "s/${LWORD}_filedir${RWORD}/__kubectl_filedir/g" \
	-e "s/${LWORD}_get_comp_words_by_ref${RWORD}/__kubectl_get_comp_words_by_ref/g" \
	-e "s/${LWORD}__ltrim_colon_completions${RWORD}/__kubectl_ltrim_colon_completions/g" \
	-e "s/${LWORD}compgen${RWORD}/__kubectl_compgen/g" \
	-e "s/${LWORD}compopt${RWORD}/__kubectl_compopt/g" \
	-e "s/${LWORD}declare${RWORD}/builtin declare/g" \
	-e "s/\\\$(type${RWORD}/\$(__kubectl_type/g" \
	<<'BASH_COMPLETION_EOF'
`
	_, err = out.Write([]byte(zshInitialization))
	if err != nil {
		return err
	}

	buf := new(bytes.Buffer)
	err = cmd.GenBashCompletion(buf)
	if err != nil {
		return err
	}

	_, err = out.Write(buf.Bytes())
	if err != nil {
		return err
	}

	zshTail := `
BASH_COMPLETION_EOF
}

__kubectl_bash_source <(__kubectl_convert_bash_to_zsh)
`
	_, err = out.Write([]byte(zshTail))
	if err != nil {
		return err
	}

	return nil
}
```

- 开启补全

```shell
# 编译
go build main.go
# zsh 环境下执行
./sulenn-cobra completion zsh > ~/.cobra-completion.sh && echo 'source ~/.cobra-completion.sh' >> ~/.zshrc
```

- 重开 zsh 即可使用命令行自动补全

**zsh 环境下的命令行自动补全参考了 Kubernets 的实现。原理就是将 bash 生成的脚本转换成 zsh 兼容的格式**

**参考**：

- [Generating shell completions](https://github.com/spf13/cobra/blob/master/shell_completions.md)

- [kubernetes](https://github.com/kubernetes/kubernetes)