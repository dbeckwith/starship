# 配置

您需要创建配置文件 `~/.config/starship.toml` 以供 Starship 使用。

```sh
mkdir -p ~/.config && touch ~/.config/starship.toml
```

Starship 的所有配置都在此 [TOML](https://github.com/toml-lang/toml) 配置文件中完成：

```toml
# Don't print a new line at the start of the prompt
add_newline = false

# Replace the "❯" symbol in the prompt with "➜"
[character]                            # The name of the module we are configuring is "character"
success_symbol = "[➜](bold green)"     # The "success_symbol" segment is being set to "➜" with the color "bold green"

# Disable the package module, hiding it from the prompt completely
[package]
disabled = true
```

你可以设置环境变量 `STARSHIP_CONFIG` 来修改 starship 查找配置文件 `starship.toml` 时查找的位置：

```sh
export STARSHIP_CONFIG=~/.starship
```

在 PowerShell (Windows) 中，在 `$PROFILE` 中添加下面的代码行能达到同样的效果：

```ps1
$ENV:STARSHIP_CONFIG = "$HOME\.starship"
```

### 日志

By default starship logs warnings and errors into a file named `~/.cache/starship/session_${STARSHIP_SESSION_KEY}.log`, where the session key is corresponding to a instance of your terminal. This, however can be changed using the `STARSHIP_CACHE` environment variable:

```sh
export STARSHIP_CACHE=~/.starship/cache
```

在 PowerShell (Windows) 中，在 `$PROFILE` 中添加下面的代码行能达到同样的效果：

```ps1
$ENV:STARSHIP_CACHE = "$HOME\AppData\Local\Temp"
```

### 术语

**组件（Module）**：提示符的组成部分，通过来自系统的上下文信息向用户显示各种信息。 比如“nodejs”组件会在当前目录是一个 NodeJS 项目时显示您当前安装的 NodeJS 版本。

**Variable**: Smaller sub-components that contains information provided by the module. For example, the "version" variable in the "nodejs" module contains the current version of NodeJS.

By convention, most modules have a prefix of default terminal color (e.g. `via` in "nodejs") and an empty space as a suffix.

### Format Strings

Format strings are the format that a module prints all its variables with. Most modules have an entry called `format` that configures the display format of the module. You can use texts, variables and text groups in a format string.

#### 字段

A variable contains a `$` symbol followed by the name of the variable. The name of a variable only contains letters, numbers and `_`.

For example:

- `$version` is a format string with a variable named `version`.
- `$git_branch$git_commit` is a format string with two variables named `git_branch` and `git_commit`.
- `$git_branch $git_commit` has the two variables separated with a space.

#### Text Group

A text group is made up of two different parts.

The first part, which is enclosed in a `[]`, is a [format string](#format-strings). You can add texts, variables, or even nested text groups in it.

In the second part, which is enclosed in a `()`, is a [style string](#style-strings). This can be used style the first part.

For example:

- `[on](red bold)` will print a string `on` with bold text colored red.
- `[⬢ $version](bold green)` will print a symbol `⬢` followed by the content of variable `version`, with bold text colored green.
- `[a [b](red) c](green)` will print `a b c` with `b` red, and `a` and `c` green.

#### 样式设定

Starship 中的大多数组件允许您为其设置显示样式。 显示样式可以通过一个字符串字段（一般是 `style`）来设置。 以下的例子给出了一些样式字符串并描述了它们的效果。 样式字符串的完整语法请查阅 [高级配置指南](/advanced-config/)。

- `"fg:green bg:blue"` 在蓝色背景上显示绿色文本
- `"bg:blue fg:bright-green"` 在蓝色背景上显示亮绿色文本
- `"bold fg:27"` 设置粗体字，用 27 号 [ANSI 标准色](https://i.stack.imgur.com/KTSQa.png)
- `"underline bg:#bf5700"` 在深橙色背景上显示带下划线文本
- `"bold italic fg:purple"` 设置文本为粗体、意大利体，颜色为紫色
- `""` 显式禁用所有样式

请注意，最终的显示样式将由您的终端模拟器控制。 例如，有的终端模拟器对于“bold”属性的文本是加亮颜色而不是加粗文字，有的颜色主题对“普通”和“明亮”两种属性的颜色使用相同的颜色值。 此外，要获得意大利体文本（一般设计为斜体），您的终端必须支持意大利体显示。

#### Conditional Format Strings

A conditional format string wrapped in `(` and `)` will not render if all variables inside are empty.

For example:

- `(@$region)` will show nothing if the variable `region` is `None`, otherwise `@` followed by the value of region.
- `(some text)` will always show nothing since there are no variables wrapped in the braces.
- When `$all` is a shortcut for `\[$a$b\]`, `($all)` will show nothing only if `$a` and `$b` are both `None`. This works the same as `(\[$a$b\] )`.

#### Escapable characters

The following symbols have special usage in a format string. If you want to print the following symbols, you have to escape them with a backslash (`\`).

- \$
- \\
- [
- ]
- (
- )

Note that `toml` has [its own escape syntax](https://github.com/toml-lang/toml#user-content-string). It is recommended to use a literal string (`''`) in your config. If you want to use a basic string (`""`), pay attention to escape the backslash `\`.

For example, when you want to print a `$` symbol on a new line, the following configs for `format` are equivalent:

```toml
# with basic string
format = "\n\\$"

# with multiline basic string
format = """

\\$"""

# with literal string
format = '''

\$'''
```

## 提示符

以下是关于提示符的配置项。

### 配置项

| Option         | 默认值                           | 描述                                  |
| -------------- | ----------------------------- | ----------------------------------- |
| `format`       | [见下文](#default-prompt-format) | Configure the format of the prompt. |
| `scan_timeout` | `30`                          | Starship 扫描文件的超时时间（单位：毫秒）。          |
| `add_newline`  | `true`                        | 在提示符与提示信息间换行。                       |

### 示例

```toml
# ~/.config/starship.toml

# Use custom format
format = """
[┌───────────────────>](bold green)
[│](bold green)$directory$rust$package
[└─>](bold green) """

# Wait 10 milliseconds for starship to check files under the current directory.
scan_timeout = 10

# Disable the newline at the start of the prompt
add_newline = false
```

### Default Prompt Format

The default `format` is used to define the format of the prompt, if empty or no `format` is provided. 默认设置如下：

```toml
format = "$all"

# Which is equivalent to
format = """
$username\
$hostname\
$shlvl\
$kubernetes\
$directory\
$git_branch\
$git_commit\
$git_state\
$git_status\
$hg_branch\
$docker_context\
$package\
$cmake\
$dart\
$dotnet\
$elixir\
$elm\
$erlang\
$golang\
$helm\
$java\
$julia\
$nim\
$nodejs\
$ocaml\
$perl\
$php\
$purescript\
$python\
$ruby\
$rust\
$swift\
$terraform\
$zig\
$nix_shell\
$conda\
$memory_usage\
$aws\
$gcloud\
$openstack\
$env_var\
$crystal\
$cmd_duration\
$custom\
$line_break\
$lua\
$jobs\
$battery\
$time\
$status\
$character"""
```

## AWS

`aws` 组件显示当前 AWS 主机所在区域与配置信息。 各组件基于 `AWS_REGION`，`AWS_DEFAULT_REGION` 和 `AWS_PROFILE` 环境变量与 `~/.aws/config` 文件。

When using [aws-vault](https://github.com/99designs/aws-vault) the profile is read from the `AWS_VAULT` env var.

### 配置项

| Option           | 默认值                                              | 描述                        |
| ---------------- | ------------------------------------------------ | ------------------------- |
| `format`         | `'on [$symbol$profile(\($region\))]($style) '` | 组件格式化模板。                  |
| `symbol`         | `"☁️ "`                                          | 这个字段的内容会显示在当前 AWS 配置信息之前。 |
| `region_aliases` |                                                  | 地区缩写列表，用来显示在 AWS 主机名之后。   |
| `style`          | `"bold yellow"`                                  | 此组件的样式。                   |
| `disabled`       | `false`                                          | 禁用 `AWS` 组件。              |

### Variables

| 字段        | 示例               | 描述                      |
| --------- | ---------------- | ----------------------- |
| region    | `ap-northeast-1` | The current AWS region  |
| profile   | `astronauts`     | The current AWS profile |
| symbol    |                  | `symbol`对应值             |
| style\* |                  | `style`对应值              |

\*: This variable can only be used as a part of a style string

### Examples

#### Display everything

```toml
# ~/.config/starship.toml

[aws]
format = 'on [$symbol$profile(\($region\))]($style) '
style = "bold blue"
symbol = "🅰 "
[aws.region_aliases]
ap-southeast-2 = "au"
us-east-1 = "va"
```

#### Display region

```toml
# ~/.config/starship.toml

[aws]
format = "on [$symbol$region]($style) "
style = "bold blue"
symbol = "🅰 "
[aws.region_aliases]
ap-southeast-2 = "au"
us-east-1 = "va"
```

#### Display profile

```toml
# ~/.config/starship.toml

[aws]
format = "on [$symbol$profile]($style) "
style = "bold blue"
symbol = "🅰 "
```

## Battery

`battery` 组件显示电池充电情况和当前充电状态。 这个组件只会在当前电量低于 10% 时显示。

### 配置项

| Option               | 默认值                               | 描述               |
| -------------------- | --------------------------------- | ---------------- |
| `full_symbol`        | `"•"`                             | 显示于电池充满时。        |
| `charging_symbol`    | `"⇡"`                             | 显示于正在充电时。        |
| `discharging_symbol` | `"⇣"`                             | 显示于电池放电时。        |
| `format`             | `"[$symbol$percentage]($style) "` | 组件格式化模板。         |
| `display`            | [见下文](#battery-display)           | 电量显示阈值和样式。       |
| `disabled`           | `false`                           | 禁用 `battery` 组件。 |

<details>
<summary>也有一些给不常见的电源状态设立的字段。</summary>

| 字段               | 描述         |
| ---------------- | ---------- |
| `unknown_symbol` | 显示于电池状态未知时 |
| `empty_symbol`   | 显示于电池状态为空时 |

注意：如果状态为 `unknown` 或 `empty`，电池指示器将被隐藏，除非您在配置中指定相关选项。

</details>

### 示例

```toml
# ~/.config/starship.toml

[battery]
full_symbol = "🔋"
charging_symbol = "⚡️"
discharging_symbol = "💀"
```

### Battery 组件的显示

`display` 选项用于定义电池指示器的显示阈值（threshold）和显示效果（style）。 如果 `display` 没有设置， 默认设置如下：

```toml
[[battery.display]]
threshold = 10
style = "bold red"
```

#### 配置项

`display` 字段的子字段如下：

| Option      | 描述               |
| ----------- | ---------------- |
| `threshold` | 当前显示设置的电量上限（见示例） |
| `style`     | 若组件被显示，则使用此样式    |

#### 示例

```toml
[[battery.display]]  # 当电量在 0% 到 10% 时以 "bold red" 样式显示
threshold = 10
style = "bold red"

[[battery.display]]  # 当电量在 10% 到 30% 时以 "bold yellow" 样式显示
threshold = 30
style = "bold yellow"

# 当电量在 30% 时以上时，电池指示器组件将不会显示出来

```

## Character

`character` 组件用于在您输入终端的文本旁显示一个字符（通常是一个箭头）。

这个字符可以告诉您最后一个命令是否执行成功。 It can do this in two ways:

- changing color (`red`/`green`)
- changing shape (`❯`/`✖`)

By default it only changes color. If you also want to change it's shape take a look at [this example](#with-custom-error-shape).

### 配置项

| Option           | 默认值                 | 描述                                                                               |
| ---------------- | ------------------- | -------------------------------------------------------------------------------- |
| `format`         | `"$symbol "`        | The format string used before the text input.                                    |
| `success_symbol` | `"[❯](bold green)"` | The format string used before the text input if the previous command succeeded.  |
| `error_symbol`   | `"[❯](bold red)"`   | The format string used before the text input if the previous command failed.     |
| `vicmd_symbol`   | `"[❮](bold green)"` | The format string used before the text input if the shell is in vim normal mode. |
| `disabled`       | `false`             | 禁用 `character` 组件。                                                               |

### Variables

| 字段     | 示例 | 描述                                                                    |
| ------ | -- | --------------------------------------------------------------------- |
| symbol |    | A mirror of either `success_symbol`, `error_symbol` or `vicmd_symbol` |

### Examples

#### With custom error shape

```toml
# ~/.config/starship.toml

[character]
success_symbol = "[➜](bold green) "
error_symbol = "[✗](bold red) "
```

#### Without custom error shape

```toml
# ~/.config/starship.toml

[character]
success_symbol = "[➜](bold green) "
error_symbol = "[➜](bold red) "
```

#### With custom vim shape

```toml
# ~/.config/starship.toml

[character]
vicmd_symbol = "[V](bold green) "
```

## CMake

The `cmake` module shows the currently installed version of CMake if any of the following conditions are met:

- The current directory contains a `CMakeLists.txt` file
- The current directory contains a `CMakeCache.txt` file

### 配置项

| Option     | 默认值                                | 描述                                           |
| ---------- | ---------------------------------- | -------------------------------------------- |
| `format`   | `"via [$symbol$version]($style) "` | 组件格式化模板。                                     |
| `symbol`   | `"喝 "`                             | The symbol used before the version of cmake. |
| `style`    | `"bold blue"`                      | 此组件的样式。                                      |
| `disabled` | `false`                            | Disables the `cmake` module.                 |

### Variables

| 字段        | 示例        | 描述                   |
| --------- | --------- | -------------------- |
| version   | `v3.17.3` | The version of cmake |
| symbol    |           | `symbol`对应值          |
| style\* |           | `style`对应值           |

\*: This variable can only be used as a part of a style string

## Command Duration

`cmd_duration` 组件显示上一个命令执行的时间。 此组件只在命令执行时间长于两秒时显示，或者当其 `min_time` 字段被设置时，按此值为执行时间的显示下限。

::: warning 不要在 Bash 里捕获 DEBUG 信号

如果您正在 `bash` 上使用 Starship，在运行 `eval $(starship)` 后，不要捕获 `DEBUG` 信号，否则此组件**将会**坏掉。

:::

需要在自动每一条命令前执行某些操作的 Bash 用户可以使用 [rcaloras 的 bash_preexec 框架](https://github.com/rcaloras/bash-preexec)。 只需要在执行 `eval $(starship init $0)` 前简单地定义 `preexec_functions` 和 `precmd_functions` 两个列表，就可以照常运行了。

### 配置项

| Option               | 默认值                           | 描述                                                    |
| -------------------- | ----------------------------- | ----------------------------------------------------- |
| `min_time`           | `2_000`                       | 显示此组件所需的最短执行时长（单位：毫秒）。                                |
| `show_milliseconds`  | `false`                       | 除了秒数外在执行时长中额外显示毫秒。                                    |
| `format`             | `"took [$duration]($style) "` | 组件格式化模板。                                              |
| `style`              | `"bold yellow"`               | 此组件的样式。                                               |
| `disabled`           | `false`                       | 禁用 `cmd_duration` 组件。                                 |
| `show_notifications` | `false`                       | Show desktop notifications when command completes.    |
| `min_time_to_notify` | `45_000`                      | Shortest duration for notification (in milliseconds). |

::: tip

Showing desktop notifications requires starship to be built with `rust-notify` support. You check if your starship supports notifications by running `STARSHIP_LOG=debug starship module cmd_duration -d 60000` when `show_notifications` is set to `true`.

:::

### Variables

| 字段        | 示例       | 描述                                      |
| --------- | -------- | --------------------------------------- |
| duration  | `16m40s` | The time it took to execute the command |
| style\* |          | `style`对应值                              |

\*: This variable can only be used as a part of a style string

### 示例

```toml
# ~/.config/starship.toml

[cmd_duration]
min_time = 500
format = "underwent [$duration](bold yellow)"
```

## Conda

The `conda` module shows the current conda environment, if `$CONDA_DEFAULT_ENV` is set.

::: tip

This does not suppress conda's own prompt modifier, you may want to run `conda config --set changeps1 False`.

:::

### 配置项

| Option              | 默认值                                | 描述                                                                                                               |
| ------------------- | ---------------------------------- | ---------------------------------------------------------------------------------------------------------------- |
| `truncation_length` | `1`                                | 如果这个 conda 环境是通过 `conda create -p [path]` 创建的，环境路径的目录深度应该被截断到此数量。 `0` 表示不用截断。 另请参阅 [`directory`](#directory) 组件。 |
| `symbol`            | `"🅒 "`                             | 在环境名之前显示的符号。                                                                                                     |
| `style`             | `"bold green"`                     | 此组件的样式。                                                                                                          |
| `format`            | `"[$symbol$environment]($style) "` | 组件格式化模板。                                                                                                         |
| `ignore_base`       | `true`                             | Ignores `base` environment when activated.                                                                       |
| `disabled`          | `false`                            | 禁用 `conda` 组件。                                                                                                   |

### Variables

| 字段          | 示例           | 描述                            |
| ----------- | ------------ | ----------------------------- |
| environment | `astronauts` | The current conda environment |
| symbol      |              | `symbol`对应值                   |
| style\*   |              | `style`对应值                    |

\*: This variable can only be used as a part of a style string

### 示例

```toml
# ~/.config/starship.toml

[conda]
format = "[$symbol$environment](dimmed green) "
```

## Crystal

The `crystal` module shows the currently installed version of Crystal. The module will be shown if any of the following conditions are met:

- 当前目录包含一个 `shard.yml` 文件
- The current directory contains a `.cr` file

### 配置项

| Option     | 默认值                                | 描述                                                        |
| ---------- | ---------------------------------- | --------------------------------------------------------- |
| `symbol`   | `"🔮 "`                             | The symbol used before displaying the version of crystal. |
| `style`    | `"bold red"`                       | 此组件的样式。                                                   |
| `format`   | `"via [$symbol$version]($style) "` | 组件格式化模板。                                                  |
| `disabled` | `false`                            | Disables the `crystal` module.                            |

### Variables

| 字段        | 示例        | 描述                       |
| --------- | --------- | ------------------------ |
| version   | `v0.32.1` | The version of `crystal` |
| symbol    |           | `symbol`对应值              |
| style\* |           | `style`对应值               |

\*: This variable can only be used as a part of a style string

### 示例

```toml
# ~/.config/starship.toml

[crystal]
format = "via [✨ $version](bold blue) "
```

## Dart

The `dart` module shows the currently installed version of Dart. The module will be shown if any of the following conditions are met:

- The current directory contains a file with `.dart` extension
- The current directory contains a `.dart_tool` directory
- The current directory contains a `pubspec.yaml` or `pubspec.lock` file

### 配置项

| Option     | 默认值                                | 描述                                              |
| ---------- | ---------------------------------- | ----------------------------------------------- |
| `format`   | `"via [$symbol$version]($style) "` | 组件格式化模板。                                        |
| `symbol`   | `"🎯 "`                             | A format string representing the symbol of Dart |
| `style`    | `"bold blue"`                      | 此组件的样式。                                         |
| `disabled` | `false`                            | Disables the `dart` module.                     |

### Variables

| 字段        | 示例       | 描述                    |
| --------- | -------- | --------------------- |
| version   | `v2.8.4` | The version of `dart` |
| symbol    |          | `symbol`对应值           |
| style\* |          | `style`对应值            |

\*: This variable can only be used as a part of a style string

### 示例

```toml
# ~/.config/starship.toml

[dart]
format = "via [🔰 $version](bold red) "
```

## Directory

The `directory` module shows the path to your current directory, truncated to three parent folders. Your directory will also be truncated to the root of the git repo that you're currently in.

When using the fish style pwd option, instead of hiding the path that is truncated, you will see a shortened name of each directory based on the number you enable for the option.

For example, given `~/Dev/Nix/nixpkgs/pkgs` where `nixpkgs` is the repo root, and the option set to `1`. You will now see `~/D/N/nixpkgs/pkgs`, whereas before it would have been `nixpkgs/pkgs`.

### 配置项

| Option              | 默认值                                                | 描述                                                    |
| ------------------- | -------------------------------------------------- | ----------------------------------------------------- |
| `truncation_length` | `3`                                                | 当前目录路径被截断后最多保留的父目录数量。                                 |
| `truncate_to_repo`  | `true`                                             | 是否只截断到您当前处于的 git 仓库根目录下。                              |
| `format`            | `"[$path]($style)[$read_only]($read_only_style) "` | 组件格式化模板。                                              |
| `style`             | `"bold cyan"`                                      | 此组件的样式。                                               |
| `disabled`          | `false`                                            | 禁用 `directory` 组件。                                    |
| `read_only`         | `"🔒"`                                              | The symbol indicating current directory is read only. |
| `read_only_style`   | `"red"`                                            | The style for the read only symbol.                   |
| `truncation_symbol` | `""`                                               | The symbol to prefix to truncated paths. eg: "…/"     |

<details>
<summary>This module has a few advanced configuration options that control how the directory is displayed.</summary>

| Advanced Option             | 默认值    | 描述                                               |
| --------------------------- | ------ | ------------------------------------------------ |
| `substitutions`             |        | A table of substitutions to be made to the path. |
| `fish_style_pwd_dir_length` | `0`    | 使用 fish shell 当前目录路径逻辑时每个省略目录名使用的字符数。            |
| `use_logical_path`          | `true` | 显示由 shell 提供的逻辑路径（`PWD`）而不是 OS 提供的路径。            |

`substitutions` allows you to define arbitrary replacements for literal strings that occur in the path, for example long network prefixes or development directories (i.e. Java). Note that this will disable the fish style PWD.

```toml
[directory.substitutions]
"/Volumes/network/path" = "/net"
"src/com/long/java/path" = "mypath"
```

`fish_style_pwd_dir_length` interacts with the standard truncation options in a way that can be surprising at first: if it's non-zero, the components of the path that would normally be truncated are instead displayed with that many characters. For example, the path `/built/this/city/on/rock/and/roll`, which would normally be displayed as as `rock/and/roll`, would be displayed as `/b/t/c/o/rock/and/roll` with `fish_style_pwd_dir_length = 1`--the path components that would normally be removed are displayed with a single character. For `fish_style_pwd_dir_length = 2`, it would be `/bu/th/ci/on/rock/and/roll`.

</details>

### Variables

| 字段        | 示例                    | 描述                         |
| --------- | --------------------- | -------------------------- |
| path      | `"D:/Projects"`       | The current directory path |
| style\* | `"black bold dimmed"` | `style`对应值                 |

\*: This variable can only be used as a part of a style string

### 示例

```toml
# ~/.config/starship.toml

[directory]
truncation_length = 8
truncation_symbol = "…/"
```

## Docker Context

The `docker_context` module shows the currently active [Docker context](https://docs.docker.com/engine/context/working-with-contexts/) if it's not set to `default`.

### 配置项

| Option            | 默认值                                | 描述                                                                                      |
| ----------------- | ---------------------------------- | --------------------------------------------------------------------------------------- |
| `format`          | `"via [$symbol$context]($style) "` | 组件格式化模板。                                                                                |
| `symbol`          | `"🐳 "`                             | The symbol used before displaying the Docker context.                                   |
| `style`           | `"blue bold"`                      | 此组件的样式。                                                                                 |
| `only_with_files` | `false`                            | Only show when there's a `docker-compose.yml` or `Dockerfile` in the current directory. |
| `disabled`        | `true`                             | Disables the `docker_context` module.                                                   |

### Variables

| 字段        | 示例             | 描述                         |
| --------- | -------------- | -------------------------- |
| context   | `test_context` | The current docker context |
| symbol    |                | `symbol`对应值                |
| style\* |                | `style`对应值                 |

\*: This variable can only be used as a part of a style string

### 示例

```toml
# ~/.config/starship.toml

[docker_context]
format = "via [🐋 $context](blue bold)"
```

## Dotnet

The `dotnet` module shows the relevant version of the .NET Core SDK for the current directory. If the SDK has been pinned in the current directory, the pinned version is shown. Otherwise the module shows the latest installed version of the SDK.

This module will only be shown in your prompt when one or more of the following files are present in the current directory:

- `global.json`
- `project.json`
- `Directory.Build.props`
- `Directory.Build.targets`
- `Packages.props`
- `*.sln`
- `*.csproj`
- `*.fsproj`
- `*.xproj`

You'll also need the .NET Core SDK installed in order to use it correctly.

Internally, this module uses its own mechanism for version detection. Typically it is twice as fast as running `dotnet --version`, but it may show an incorrect version if your .NET project has an unusual directory layout. If accuracy is more important than speed, you can disable the mechanism by setting `heuristic = false` in the module options.

The module will also show the Target Framework Moniker (<https://docs.microsoft.com/en-us/dotnet/standard/frameworks#supported-target-framework-versions>) when there is a csproj file in the current directory.

### 配置项

| Option      | 默认值                                      | 描述                             |
| ----------- | ---------------------------------------- | ------------------------------ |
| `format`    | `"v[$symbol$version( 🎯 $tfm)]($style) "` | 组件格式化模板。                       |
| `symbol`    | `"•NET "`                                | 这个字段的内容会显示在当前 .NET 版本之前。       |
| `heuristic` | `true`                                   | 使用更快的版本探测机制以保证 starship 的运行速度。 |
| `style`     | `"bold blue"`                            | 此组件的样式。                        |
| `disabled`  | `false`                                  | 禁用 `dotnet` 组件。                |

### Variables

| 字段        | 示例               | 描述                                                                 |
| --------- | ---------------- | ------------------------------------------------------------------ |
| version   | `v3.1.201`       | The version of `dotnet` sdk                                        |
| tfm       | `netstandard2.0` | The Target Framework Moniker that the current project is targeting |
| symbol    |                  | `symbol`对应值                                                        |
| style\* |                  | `style`对应值                                                         |

\*: This variable can only be used as a part of a style string

### 示例

```toml
# ~/.config/starship.toml

[dotnet]
symbol = "🥅 "
style = "green"
heuristic = false
```

## Elixir

The `elixir` module shows the currently installed version of Elixir and Erlang/OTP. The module will be shown if any of the following conditions are met:

- 当前目录包含一个 `mix.exs` 文件.

### 配置项

| Option     | 默认值                                                       | 描述                                                              |
| ---------- | --------------------------------------------------------- | --------------------------------------------------------------- |
| `symbol`   | `"💧 "`                                                    | The symbol used before displaying the version of Elixir/Erlang. |
| `style`    | `"bold purple"`                                           | 此组件的样式。                                                         |
| `format`   | `'via [$symbol$version \(OTP $otp_version\)]($style) '` | The format for the module elixir.                               |
| `disabled` | `false`                                                   | Disables the `elixir` module.                                   |

### Variables

| 字段          | 示例      | 描述                          |
| ----------- | ------- | --------------------------- |
| version     | `v1.10` | The version of `elixir`     |
| otp_version |         | The otp version of `elixir` |
| symbol      |         | `symbol`对应值                 |
| style\*   |         | `style`对应值                  |

\*: This variable can only be used as a part of a style string

### 示例

```toml
# ~/.config/starship.toml

[elixir]
symbol = "🔮 "
```

## Elm

The `elm` module shows the currently installed version of Elm. The module will be shown if any of the following conditions are met:

- 当前目录包含一个 `elm.json` 文件
- 当前目录包含 `elm-package.json` 文件
- The current directory contains a `.elm-version` file
- The current directory contains a `elm-stuff` folder
- The current directory contains a `*.elm` files

### 配置项

| Option     | 默认值                                | 描述                                              |
| ---------- | ---------------------------------- | ----------------------------------------------- |
| `format`   | `"via [$symbol$version]($style) "` | 组件格式化模板。                                        |
| `symbol`   | `"🌳 "`                             | A format string representing the symbol of Elm. |
| `style`    | `"cyan bold"`                      | 此组件的样式。                                         |
| `disabled` | `false`                            | Disables the `elm` module.                      |

### Variables

| 字段        | 示例        | 描述                   |
| --------- | --------- | -------------------- |
| version   | `v0.19.1` | The version of `elm` |
| symbol    |           | `symbol`对应值          |
| style\* |           | `style`对应值           |

\*: This variable can only be used as a part of a style string

### 示例

```toml
# ~/.config/starship.toml

[elm]
format = "via [ $version](cyan bold) "
```

## Environment Variable

The `env_var` module displays the current value of a selected environment variable. The module will be shown only if any of the following conditions are met:

- 设置的 `variable` 是一个已存在的环境变量
- 未定义 `variable`，但定义了 `default`

### 配置项

| Option     | 默认值                            | 描述                  |
| ---------- | ------------------------------ | ------------------- |
| `symbol`   |                                | 这个字段的内容会显示在环境变量值之前。 |
| `variable` |                                | 要显示的环境变量。           |
| `default`  |                                | 所选变量未定义时显示的默认值。     |
| `format`   | `"with [$env_value]($style) "` | 组件格式化模板。            |
| `disabled` | `false`                        | 禁用 `env_var` 组件。    |

### Variables

| 字段        | 示例                                          | 描述                                         |
| --------- | ------------------------------------------- | ------------------------------------------ |
| env_value | `Windows NT` (if _variable_ would be `$OS`) | The environment value of option `variable` |
| symbol    |                                             | `symbol`对应值                                |
| style\* | `black bold dimmed`                         | `style`对应值                                 |

\*: This variable can only be used as a part of a style string

### 示例

```toml
# ~/.config/starship.toml

[env_var]
variable = "SHELL"
default = "unknown shell"
```

## Erlang

The `erlang` module shows the currently installed version of Erlang/OTP. The module will be shown if any of the following conditions are met:

- 当前目录包含一个 `rebar.config` 文件.
- 当前目录包含一个 `erlang.mk` 文件.

### 配置项

| Option     | 默认值                                | 描述                                                       |
| ---------- | ---------------------------------- | -------------------------------------------------------- |
| `symbol`   | `" "`                             | The symbol used before displaying the version of erlang. |
| `style`    | `"bold red"`                       | 此组件的样式。                                                  |
| `format`   | `"via [$symbol$version]($style) "` | 组件格式化模板。                                                 |
| `disabled` | `false`                            | Disables the `erlang` module.                            |

### Variables

| 字段        | 示例        | 描述                      |
| --------- | --------- | ----------------------- |
| version   | `v22.1.3` | The version of `erlang` |
| symbol    |           | `symbol`对应值             |
| style\* |           | `style`对应值              |

\*: This variable can only be used as a part of a style string

### 示例

```toml
# ~/.config/starship.toml

[erlang]
format = "via [e $version](bold red) "
```

## Gcloud

The `gcloud` module shows the current configuration for [`gcloud`](https://cloud.google.com/sdk/gcloud) CLI. This is based on the `~/.config/gcloud/active_config` file and the `~/.config/gcloud/configurations/config_{CONFIG NAME}` file and the `CLOUDSDK_CONFIG` env var.

### 配置项

| Option           | 默认值                                              | 描述                                                              |
| ---------------- | ------------------------------------------------ | --------------------------------------------------------------- |
| `format`         | `'on [$symbol$account(\($region\))]($style) '` | 组件格式化模板。                                                        |
| `symbol`         | `"☁️ "`                                          | The symbol used before displaying the current GCP profile.      |
| `region_aliases` |                                                  | Table of region aliases to display in addition to the GCP name. |
| `style`          | `"bold blue"`                                    | 此组件的样式。                                                         |
| `disabled`       | `false`                                          | Disables the `gcloud` module.                                   |

### Variables

| 字段        | 示例                | 描述                                                                 |
| --------- | ----------------- | ------------------------------------------------------------------ |
| region    | `us-central1`     | The current GCP region                                             |
| account   | `foo@example.com` | The current GCP profile                                            |
| project   |                   | The current GCP project                                            |
| active    | `default`         | The active config name written in `~/.config/gcloud/active_config` |
| symbol    |                   | `symbol`对应值                                                        |
| style\* |                   | `style`对应值                                                         |

\*: This variable can only be used as a part of a style string

### Examples

#### Display account and project

```toml
# ~/.config/starship.toml

[gcloud]
format = 'on [$symbol$account(\($project\))]($style) '
```

#### Display active config name only

```toml
# ~/.config/starship.toml

[gcloud]
format = "[$symbol$active]($style) "
style = "bold yellow"
```

#### Display account and aliased region

```toml
# ~/.config/starship.toml

[gcloud]
symbol = "️🇬️ "
[gcloud.region_aliases]
us-central1 = "uc1"
asia-northeast1 = "an1"
```

## Git Branch

The `git_branch` module shows the active branch of the repo in your current directory.

### 配置项

| Option               | 默认值                              | 描述                                                                                   |
| -------------------- | -------------------------------- | ------------------------------------------------------------------------------------ |
| `always_show_remote` | `false`                          | Shows the remote tracking branch name, even if it is equal to the local branch name. |
| `format`             | `"on [$symbol$branch]($style) "` | 组件格式化模板。 Use `"$branch"` to refer to the current branch name.                        |
| `symbol`             | `" "`                           | A format string representing the symbol of git branch.                               |
| `style`              | `"bold purple"`                  | 此组件的样式。                                                                              |
| `truncation_length`  | `2^63 - 1`                       | Truncates a git branch to X graphemes.                                               |
| `truncation_symbol`  | `"…"`                            | 此字段的内容用来表示分支名称被截断。 You can use `""` for no symbol.                                   |
| `only_attached`      | `false`                          | Only show the branch name when not in a detached HEAD state.                         |
| `disabled`           | `false`                          | Disables the `git_branch` module.                                                    |

### Variables

| 字段        | 示例       | 描述                                                                                                   |
| --------- | -------- | ---------------------------------------------------------------------------------------------------- |
| branch    | `master` | The current branch name, falls back to `HEAD` if there's no current branch (e.g. git detached HEAD). |
| remote    | `master` | The remote branch name.                                                                              |
| symbol    |          | `symbol`对应值                                                                                          |
| style\* |          | `style`对应值                                                                                           |

\*: This variable can only be used as a part of a style string

### 示例

```toml
# ~/.config/starship.toml

[git_branch]
symbol = "🌱 "
truncation_length = 4
truncation_symbol = ""
```

## Git Commit

The `git_commit` module shows the current commit hash and also the tag (if any) of the repo in your current directory.

### 配置项

| Option               | 默认值                                                    | 描述                                                    |
| -------------------- | ------------------------------------------------------ | ----------------------------------------------------- |
| `commit_hash_length` | `7`                                                    | 显示的 git 提交哈希值的长度。                                     |
| `format`             | `"[\\($hash\\)]($style) [\\($tag\\)]($style)"` | 组件格式化模板。                                              |
| `style`              | `"bold green"`                                         | 此组件的样式。                                               |
| `only_detached`      | `true`                                                 | Only show git commit hash when in detached HEAD state |
| `tag_disabled`       | `true`                                                 | Disables showing tag info in `git_commit` module.     |
| `tag_symbol`         | `"🏷 "`                                                 | Tag symbol prefixing the info shown                   |
| `disabled`           | `false`                                                | Disables the `git_commit` module.                     |

### Variables

| 字段        | 示例        | 描述                          |
| --------- | --------- | --------------------------- |
| hash      | `b703eb3` | The current git commit hash |
| style\* |           | `style`对应值                  |

\*: This variable can only be used as a part of a style string

### 示例

```toml
# ~/.config/starship.toml

[git_commit]
commit_hash_length = 4
tag_symbol = "🔖 "
```

## Git State

The `git_state` module will show in directories which are part of a git repository, and where there is an operation in progress, such as: _REBASING_, _BISECTING_, etc. If there is progress information (e.g., REBASING 3/10), that information will be shown too.

### 配置项

| Option         | 默认值                                                             | 描述                                                                                      |
| -------------- | --------------------------------------------------------------- | --------------------------------------------------------------------------------------- |
| `rebase`       | `"REBASING"`                                                    | A format string displayed when a `rebase` is in progress.                               |
| `merge`        | `"MERGING"`                                                     | A format string displayed when a `merge` is in progress.                                |
| `revert`       | `"REVERTING"`                                                   | A format string displayed when a `revert` is in progress.                               |
| `cherry_pick`  | `"CHERRY-PICKING"`                                              | A format string displayed when a `cherry-pick` is in progress.                          |
| `bisect`       | `"BISECTING"`                                                   | A format string displayed when a `bisect` is in progress.                               |
| `am`           | `"AM"`                                                          | A format string displayed when an `apply-mailbox` (`git am`) is in progress.            |
| `am_or_rebase` | `"AM/REBASE"`                                                   | A format string displayed when an ambiguous `apply-mailbox` or `rebase` is in progress. |
| `style`        | `"bold yellow"`                                                 | 此组件的样式。                                                                                 |
| `format`       | `'\([$state( $progress_current/$progress_total)]($style)\) '` | 组件格式化模板。                                                                                |
| `disabled`     | `false`                                                         | 禁用 `git_state` 模块                                                                       |

### Variables

| 字段               | 示例         | 描述                             |
| ---------------- | ---------- | ------------------------------ |
| state            | `REBASING` | The current state of the repo  |
| progress_current | `1`        | The current operation progress |
| progress_total   | `2`        | The total operation progress   |
| style\*        |            | `style`对应值                     |

\*: This variable can only be used as a part of a style string

### 示例

```toml
# ~/.config/starship.toml

[git_state]
format = '[\($state( $progress_current of $progress_total)\)]($style) '
cherry_pick = "[🍒 PICKING](bold red)"
```

## Git Status

The `git_status` module shows symbols representing the state of the repo in your current directory.

### 配置项

| Option       | 默认值                                             | 描述                                  |
| ------------ | ----------------------------------------------- | ----------------------------------- |
| `format`     | `'([\[$all_status$ahead_behind\]]($style) )'` | The default format for `git_status` |
| `conflicted` | `"="`                                           | 这个分支有合并冲突。                          |
| `ahead`      | `"⇡"`                                           | The format of `ahead`               |
| `behind`     | `"⇣"`                                           | The format of `behind`              |
| `diverged`   | `"⇕"`                                           | The format of `diverged`            |
| `untracked`  | `"?"`                                           | The format of `untracked`           |
| `stashed`    | `"$"`                                           | The format of `stashed`             |
| `modified`   | `"!"`                                           | The format of `modified`            |
| `staged`     | `"+"`                                           | The format of `staged`              |
| `renamed`    | `"»"`                                           | The format of `renamed`             |
| `deleted`    | `"✘"`                                           | The format of `deleted`             |
| `style`      | `"bold red"`                                    | 此组件的样式。                             |
| `disabled`   | `false`                                         | 禁用 `git_status` 组件。                 |

### Variables

The following variables can be used in `format`:

| 字段             | 描述                                                                                            |
| -------------- | --------------------------------------------------------------------------------------------- |
| `all_status`   | Shortcut for`$conflicted$stashed$deleted$renamed$modified$staged$untracked`                   |
| `ahead_behind` | Displays `diverged` `ahead` or `behind` format string based on the current status of the repo |
| `conflicted`   | Displays `conflicted` when this branch has merge conflicts.                                   |
| `untracked`    | Displays `untracked` when there are untracked files in the working directory.                 |
| `stashed`      | Displays `stashed` when a stash exists for the local repository.                              |
| `modified`     | Displays `modified` when there are file modifications in the working directory.               |
| `staged`       | Displays `staged` when a new file has been added to the staging area.                         |
| `renamed`      | Displays `renamed` when a renamed file has been added to the staging area.                    |
| `deleted`      | Displays `deleted` when a file's deletion has been added to the staging area.                 |
| style\*      | `style`对应值                                                                                    |

\*: This variable can only be used as a part of a style string

The following variables can be used in `diverged`:

| 字段             | 描述                                             |
| -------------- | ---------------------------------------------- |
| `ahead_count`  | Number of commits ahead of the tracking branch |
| `behind_count` | Number of commits behind the tracking branch   |

The following variables can be used in `conflicted`, `ahead`, `behind`, `untracked`, `stashed`, `modified`, `staged`, `renamed` and `deleted`:

| 字段      | 描述        |
| ------- | --------- |
| `count` | 显示相应的文件数量 |

### 示例

```toml
# ~/.config/starship.toml

[git_status]
conflicted = "🏳"
ahead = "🏎💨"
behind = "😰"
diverged = "😵"
untracked = "🤷‍"
stashed = "📦"
modified = "📝"
staged = '[++\($count\)](green)'
renamed = "👅"
deleted = "🗑"
```

Show ahead/behind count of the branch being tracked

```toml
# ~/.config/starship.toml

[git_status]
ahead = "⇡${count}"
diverged = "⇕⇡${ahead_count}⇣${behind_count}"
behind = "⇣${count}"
```

## Golang

The `golang` module shows the currently installed version of Golang. The module will be shown if any of the following conditions are met:

- 当前目录包含 `go.mod` 文件
- 当前目录包含 `go.sum` 文件
- 当前目录包含 `glide.yaml` 文件
- 当前目录包含 `Gopkg.yml` 文件
- 当前目录包含 `Gopkg.lock` 文件
- The current directory contains a `.go-version` file
- 当前目录包含 `Godeps` 目录
- 当前目录包含一个使用 `.go` 扩展名的文件

### 配置项

| Option     | 默认值                                | 描述                                             |
| ---------- | ---------------------------------- | ---------------------------------------------- |
| `format`   | `"via [$symbol$version]($style) "` | 组件格式化模板。                                       |
| `symbol`   | `"🐹 "`                             | A format string representing the symbol of Go. |
| `style`    | `"bold cyan"`                      | 此组件的样式。                                        |
| `disabled` | `false`                            | 禁用 `golang` 组件。                                |

### Variables

| 字段        | 示例        | 描述                  |
| --------- | --------- | ------------------- |
| version   | `v1.12.1` | The version of `go` |
| symbol    |           | `symbol`对应值         |
| style\* |           | `style`对应值          |

\*: This variable can only be used as a part of a style string

### 示例

```toml
# ~/.config/starship.toml

[golang]
format = "via [🏎💨 $version](bold cyan) "
```

## Helm

The `helm` module shows the currently installed version of Helm. The module will be shown if any of the following conditions are met:

- 当前目录包含一个 `helmfile.yaml` 文件
- The current directory contains a `Chart.yaml` file

### 配置项

| Option     | 默认值                                | 描述                                               |
| ---------- | ---------------------------------- | ------------------------------------------------ |
| `format`   | `"via [$symbol$version]($style) "` | 组件格式化模板。                                         |
| `symbol`   | `"⎈ "`                             | A format string representing the symbol of Helm. |
| `style`    | `"bold white"`                     | 此组件的样式。                                          |
| `disabled` | `false`                            | Disables the `helm` module.                      |

### Variables

| 字段        | 示例       | 描述                    |
| --------- | -------- | --------------------- |
| version   | `v3.1.1` | The version of `helm` |
| symbol    |          | `symbol`对应值           |
| style\* |          | `style`对应值            |

\*: This variable can only be used as a part of a style string

### 示例

```toml
# ~/.config/starship.toml

[helm]
format = "via [⎈ $version](bold white) "
```

## Hostname

The `hostname` module shows the system hostname.

### 配置项

| Option     | 默认值                         | 描述                                                                 |
| ---------- | --------------------------- | ------------------------------------------------------------------ |
| `ssh_only` | `true`                      | 仅在连接到 SSH 会话时显示主机名。                                                |
| `trim_at`  | `"."`                       | 当主机名过长被截断时，会截断成第一次匹配该字符串之前的主机名。 `"."` 会让主机名截断到第一个点处。 `""` 会禁用任何截断。 |
| `format`   | `"[$hostname]($style) in "` | 组件格式化模板。                                                           |
| `style`    | `"bold dimmed green"`       | 此组件的样式。                                                            |
| `disabled` | `false`                     | 禁用 `hostname` 组件。                                                  |

### Variables

| 字段        | 示例 | 描述          |
| --------- | -- | ----------- |
| symbol    |    | `symbol`对应值 |
| style\* |    | `style`对应值  |

\*: This variable can only be used as a part of a style string

### 示例

```toml
# ~/.config/starship.toml

[hostname]
ssh_only = false
format =  "on [$hostname](bold red) "
trim_at = ".companyname.com"
disabled = false
```

## Java

The `java` module shows the currently installed version of Java. The module will be shown if any of the following conditions are met:

- The current directory contains a `pom.xml`, `build.gradle.kts`, `build.sbt` or `.java-version` file
- 当前目录包含一个扩展名为 `.java`，`.class`，`.gradle` 或 `.jar` 的文件

### 配置项

| Option     | 默认值                                    | 描述                                              |
| ---------- | -------------------------------------- | ----------------------------------------------- |
| `format`   | `"via [${symbol}${version}]($style) "` | 组件格式化模板。                                        |
| `symbol`   | `"☕ "`                                 | A format string representing the symbol of Java |
| `style`    | `"red dimmed"`                         | 此组件的样式。                                         |
| `disabled` | `false`                                | 禁用 `java` 组件。                                   |

### Variables

| 字段        | 示例    | 描述                    |
| --------- | ----- | --------------------- |
| version   | `v14` | The version of `java` |
| symbol    |       | `symbol`对应值           |
| style\* |       | `style`对应值            |

\*: This variable can only be used as a part of a style string

### 示例

```toml
# ~/.config/starship.toml

[java]
symbol = "🌟 "
```

## Jobs

The `jobs` module shows the current number of jobs running. The module will be shown only if there are background jobs running. The module will show the number of jobs running if there is more than 1 job, or more than the `threshold` config value, if it exists.

### 配置项

| Option      | 默认值                           | 描述                                               |
| ----------- | ----------------------------- | ------------------------------------------------ |
| `threshold` | `1`                           | 如果超过此字段的值，显示任务数量。                                |
| `format`    | `"[$symbol$number]($style) "` | 组件格式化模板。                                         |
| `symbol`    | `"✦"`                         | A format string representing the number of jobs. |
| `style`     | `"bold blue"`                 | 此组件的样式。                                          |
| `disabled`  | `false`                       | 禁用 `jobs` 组件。                                    |

### Variables

| 字段        | 示例  | 描述                 |
| --------- | --- | ------------------ |
| number    | `1` | The number of jobs |
| symbol    |     | `symbol`对应值        |
| style\* |     | `style`对应值         |

\*: This variable can only be used as a part of a style string

### 示例

```toml
# ~/.config/starship.toml

[jobs]
symbol = "+ "
threshold = 4
```

## Julia

The `julia` module shows the currently installed version of Julia. The module will be shown if any of the following conditions are met:

- The current directory contains a `Project.toml` file
- The current directory contains a `Manifest.toml` file
- The current directory contains a file with the `.jl` extension

### 配置项

| Option     | 默认值                                | 描述                                                |
| ---------- | ---------------------------------- | ------------------------------------------------- |
| `format`   | `"via [$symbol$version]($style) "` | 组件格式化模板。                                          |
| `symbol`   | `"ஃ "`                             | A format string representing the symbol of Julia. |
| `style`    | `"bold purple"`                    | 此组件的样式。                                           |
| `disabled` | `false`                            | Disables the `julia` module.                      |

### Variables

| 字段        | 示例       | 描述                     |
| --------- | -------- | ---------------------- |
| version   | `v1.4.0` | The version of `julia` |
| symbol    |          | `symbol`对应值            |
| style\* |          | `style`对应值             |

\*: This variable can only be used as a part of a style string

### 示例

```toml
# ~/.config/starship.toml

[julia]
symbol = "∴ "
```

## Kubernetes

Displays the current Kubernetes context name and, if set, the namespace from the kubeconfig file. The namespace needs to be set in the kubeconfig file, this can be done via `kubectl config set-context starship-cluster --namespace astronaut`. If the `$KUBECONFIG` env var is set the module will use that if not it will use the `~/.kube/config`.

::: tip

This module is disabled by default. To enable it, set `disabled` to `false` in your configuration file.

:::

### 配置项

| Option            | 默认值                                                  | 描述                                                                    |
| ----------------- | ---------------------------------------------------- | --------------------------------------------------------------------- |
| `symbol`          | `"☸ "`                                               | A format string representing the symbol displayed before the Cluster. |
| `format`          | `'[$symbol$context( \($namespace\))]($style) in '` | 组件格式化模板。                                                              |
| `style`           | `"cyan bold"`                                        | 此组件的样式。                                                               |
| `context_aliases` |                                                      | Table of context aliases to display.                                  |
| `disabled`        | `true`                                               | Disables the `kubernetes` module.                                     |

### Variables

| 字段        | 示例                   | 描述                                       |
| --------- | -------------------- | ---------------------------------------- |
| context   | `starship-cluster`   | The current kubernetes context           |
| namespace | `starship-namespace` | If set, the current kubernetes namespace |
| symbol    |                      | `symbol`对应值                              |
| style\* |                      | `style`对应值                               |

\*: This variable can only be used as a part of a style string

### 示例

```toml
# ~/.config/starship.toml

[kubernetes]
format = 'on [⛵ $context \($namespace\)](dimmed green) '
disabled = false
[kubernetes.context_aliases]
"dev.local.cluster.k8s" = "dev"
```

## Line Break

The `line_break` module separates the prompt into two lines.

### 配置项

| Option     | 默认值     | 描述                          |
| ---------- | ------- | --------------------------- |
| `disabled` | `false` | 禁用 `line_break` 组件，使提示成为单行。 |

### 示例

```toml
# ~/.config/starship.toml

[line_break]
disabled = true
```

## Lua

The `lua` module shows the currently installed version of Lua. The module will be shown if any of the following conditions are met:

- The current directory contains a `.lua-version` file
- The current directory contains a `lua` directory
- The current directory contains a file with the `.lua` extension

### 配置项

| Option       | 默认值                                | 描述                                                                         |
| ------------ | ---------------------------------- | -------------------------------------------------------------------------- |
| `format`     | `"via [$symbol$version]($style) "` | 组件格式化模板。                                                                   |
| `symbol`     | `"🌙 "`                             | A format string representing the symbol of Lua.                            |
| `style`      | `"bold blue"`                      | 此组件的样式。                                                                    |
| `lua_binary` | `"lua"`                            | Configures the lua binary that Starship executes when getting the version. |
| `disabled`   | `false`                            | Disables the `lua` module.                                                 |

### Variables

| 字段        | 示例       | 描述                   |
| --------- | -------- | -------------------- |
| version   | `v5.4.0` | The version of `lua` |
| symbol    |          | `symbol`对应值          |
| style\* |          | `style`对应值           |

\*: This variable can only be used as a part of a style string

### 示例

```toml
# ~/.config/starship.toml

[lua]
format = "via [🌕 $version](bold blue) "
```

## Memory Usage

The `memory_usage` module shows current system memory and swap usage.

By default the swap usage is displayed if the total system swap is non-zero.

::: tip

This module is disabled by default. To enable it, set `disabled` to `false` in your configuration file.

:::

### 配置项

| Option      | 默认值                                           | 描述                                                       |
| ----------- | --------------------------------------------- | -------------------------------------------------------- |
| `threshold` | `75`                                          | Hide the memory usage unless it exceeds this percentage. |
| `format`    | `"via $symbol [${ram}( | ${swap})]($style) "` | 组件格式化模板。                                                 |
| `symbol`    | `"🐏"`                                         | The symbol used before displaying the memory usage.      |
| `style`     | `"bold dimmed white"`                         | 此组件的样式。                                                  |
| `disabled`  | `true`                                        | Disables the `memory_usage` module.                      |

### Variables

| 字段               | 示例            | 描述                                                                 |
| ---------------- | ------------- | ------------------------------------------------------------------ |
| ram              | `31GiB/65GiB` | The usage/total RAM of the current system memory.                  |
| ram_pct          | `48%`         | The percentage of the current system memory.                       |
| swap\*\*     | `1GiB/4GiB`   | The swap memory size of the current system swap memory file.       |
| swap_pct\*\* | `77%`         | The swap memory percentage of the current system swap memory file. |
| symbol           | `🐏`           | `symbol`对应值                                                        |
| style\*        |               | `style`对应值                                                         |

\*: This variable can only be used as a part of a style string \*\*: The SWAP file information is only displayed if detected on the current system

### 示例

```toml
# ~/.config/starship.toml

[memory_usage]
disabled = false
threshold = -1
symbol = " "
style = "bold dimmed green"
```

## Mercurial Branch

The `hg_branch` module shows the active branch of the repo in your current directory.

### 配置项

| Option              | 默认值                              | 描述                                                                                           |
| ------------------- | -------------------------------- | -------------------------------------------------------------------------------------------- |
| `symbol`            | `" "`                           | The symbol used before the hg bookmark or branch name of the repo in your current directory. |
| `style`             | `"bold purple"`                  | 此组件的样式。                                                                                      |
| `format`            | `"on [$symbol$branch]($style) "` | 组件格式化模板。                                                                                     |
| `truncation_length` | `2^63 - 1`                       | Truncates the hg branch name to X graphemes                                                  |
| `truncation_symbol` | `"…"`                            | 此字段的内容用来表示分支名称被截断。                                                                           |
| `disabled`          | `true`                           | Disables the `hg_branch` module.                                                             |

### Variables

| 字段        | 示例       | 描述                          |
| --------- | -------- | --------------------------- |
| branch    | `master` | The active mercurial branch |
| symbol    |          | `symbol`对应值                 |
| style\* |          | `style`对应值                  |

\*: This variable can only be used as a part of a style string

### 示例

```toml
# ~/.config/starship.toml

[hg_branch]
format = "on [🌱 $branch](bold purple)"
truncation_length = 4
truncation_symbol = ""
```

## Nim

The `nim` module shows the currently installed version of Nim. The module will be shown if any of the following conditions are met:

- The current directory contains a `nim.cfg` file
- The current directory contains a file with the `.nim` extension
- The current directory contains a file with the `.nims` extension
- The current directory contains a file with the `.nimble` extension

### 配置项

| Option     | 默认值                                | 描述                                                    |
| ---------- | ---------------------------------- | ----------------------------------------------------- |
| `format`   | `"via [$symbol$version]($style) "` | The format for the module                             |
| `symbol`   | `"👑 "`                             | The symbol used before displaying the version of Nim. |
| `style`    | `"bold yellow"`                    | 此组件的样式。                                               |
| `disabled` | `false`                            | Disables the `nim` module.                            |

### Variables

| 字段        | 示例       | 描述                    |
| --------- | -------- | --------------------- |
| version   | `v1.2.0` | The version of `nimc` |
| symbol    |          | `symbol`对应值           |
| style\* |          | `style`对应值            |

\*: This variable can only be used as a part of a style string

### 示例

```toml
# ~/.config/starship.toml

[nim]
style = "yellow"
symbol = "🎣 "
```

## Nix-shell

The `nix_shell` module shows the nix-shell environment. The module will be shown when inside a nix-shell environment.

### 配置项

| Option       | 默认值                                            | 描述                                                    |
| ------------ | ---------------------------------------------- | ----------------------------------------------------- |
| `format`     | `'via [$symbol$state( \($name\))]($style) '` | 组件格式化模板。                                              |
| `symbol`     | `"❄️ "`                                        | A format string representing the symbol of nix-shell. |
| `style`      | `"bold blue"`                                  | 此组件的样式。                                               |
| `impure_msg` | `"impure"`                                     | A format string shown when the shell is impure.       |
| `pure_msg`   | `"pure"`                                       | A format string shown when the shell is pure.         |
| `disabled`   | `false`                                        | Disables the `nix_shell` module.                      |

### Variables

| 字段        | 示例      | 描述                         |
| --------- | ------- | -------------------------- |
| state     | `pure`  | The state of the nix-shell |
| name      | `lorri` | The name of the nix-shell  |
| symbol    |         | `symbol`对应值                |
| style\* |         | `style`对应值                 |

\*: This variable can only be used as a part of a style string

### 示例

```toml
# ~/.config/starship.toml

[nix_shell]
disabled = true
impure_msg = "[impure shell](bold red)"
pure_msg = "[pure shell](bold green)"
format = 'via [☃️ $state( \($name\))](bold blue) '
```

## NodeJS

The `nodejs` module shows the currently installed version of NodeJS. The module will be shown if any of the following conditions are met:

- The current directory contains a `package.json` file
- The current directory contains a `.node-version` file
- The current directory contains a `node_modules` directory
- The current directory contains a file with the `.js`, `.mjs` or `.cjs` extension
- The current directory contains a file with the `.ts` extension

### 配置项

| Option     | 默认值                                | 描述                                                 |
| ---------- | ---------------------------------- | -------------------------------------------------- |
| `format`   | `"via [$symbol$version]($style) "` | 组件格式化模板。                                           |
| `symbol`   | `"⬢ "`                             | A format string representing the symbol of NodeJS. |
| `style`    | `"bold green"`                     | 此组件的样式。                                            |
| `disabled` | `false`                            | Disables the `nodejs` module.                      |

###  Variables

| 字段        | 示例         | 描述                    |
| --------- | ---------- | --------------------- |
| version   | `v13.12.0` | The version of `node` |
| symbol    |            | `symbol`对应值           |
| style\* |            | `style`对应值            |

\*: This variable can only be used as a part of a style string

### 示例

```toml
# ~/.config/starship.toml

[nodejs]
format = "via [🤖 $version](bold green) "
```

## OCaml

The `ocaml` module shows the currently installed version of OCaml. The module will be shown if any of the following conditions are met:

- The current directory contains a file with `.opam` extension or `_opam` directory
- The current directory contains a `esy.lock` directory
- The current directory contains a `dune` or `dune-project` file
- The current directory contains a `jbuild` or `jbuild-ignore` file
- The current directory contains a `.merlin` file
- The current directory contains a file with `.ml`, `.mli`, `.re` or `.rei` extension

### 配置项

| Option     | 默认值                                | 描述                                                      |
| ---------- | ---------------------------------- | ------------------------------------------------------- |
| `format`   | `"via [$symbol$version]($style) "` | The format string for the module.                       |
| `symbol`   | `"🐫 "`                             | The symbol used before displaying the version of OCaml. |
| `style`    | `"bold yellow"`                    | 此组件的样式。                                                 |
| `disabled` | `false`                            | Disables the `ocaml` module.                            |

### Variables

| 字段        | 示例        | 描述                     |
| --------- | --------- | ---------------------- |
| version   | `v4.10.0` | The version of `ocaml` |
| symbol    |           | `symbol`对应值            |
| style\* |           | `style`对应值             |

\*: This variable can only be used as a part of a style string

### 示例

```toml
# ~/.config/starship.toml

[ocaml]
format = "via [🐪 $version]($style) "
```

## OpenStack

The `openstack` module shows the current OpenStack cloud and project. The module only active when the `OS_CLOUD` env var is set, in which case it will read `clouds.yaml` file from any of the [default locations](https://docs.openstack.org/python-openstackclient/latest/configuration/index.html#configuration-files). to fetch the current project in use.

### 配置项

| Option     | 默认值                                                 | 描述                                                             |
| ---------- | --------------------------------------------------- | -------------------------------------------------------------- |
| `format`   | `"on [$symbol$cloud(\\($project\\))]($style) "` | 组件格式化模板。                                                       |
| `symbol`   | `"☁️ "`                                             | The symbol used before displaying the current OpenStack cloud. |
| `style`    | `"bold yellow"`                                     | 此组件的样式。                                                        |
| `disabled` | `false`                                             | Disables the `OpenStack` module.                               |

### Variables

| 字段        | 示例     | 描述                            |
| --------- | ------ | ----------------------------- |
| cloud     | `corp` | The current OpenStack cloud   |
| project   | `dev`  | The current OpenStack project |
| symbol    |        | `symbol`对应值                   |
| style\* |        | `style`对应值                    |

\*: This variable can only be used as a part of a style string

### 示例

```toml
# ~/.config/starship.toml

[openstack]
format = "on [$symbol$cloud(\\($project\\))]($style) "
style = "bold yellow"
symbol = "☁️ "
```

## Package Version

The `package` module is shown when the current directory is the repository for a package, and shows its current version. The module currently supports `npm`, `cargo`, `poetry`, `composer`, `gradle`, `julia`, `mix` and `helm` packages.

- **npm** – The `npm` package version is extracted from the `package.json` present in the current directory
- **cargo** – The `cargo` package version is extracted from the `Cargo.toml` present in the current directory
- **poetry** – The `poetry` package version is extracted from the `pyproject.toml` present in the current directory
- **composer** – The `composer` package version is extracted from the `composer.json` present in the current directory
- **gradle** – The `gradle` package version is extracted from the `build.gradle` present
- **julia** - The package version is extracted from the `Project.toml` present
- **mix** - The `mix` package version is extracted from the `mix.exs` present
- **helm** - The `helm` chart version is extracted from the `Chart.yaml` present
- **maven** - The `maven` package version is extracted from the `pom.xml` present

> ⚠ 此组件显示的是源代码在当前目录中的软件包的版本，而不是包管理器的版本。

### 配置项

| Option            | 默认值                                | 描述                                                         |
| ----------------- | ---------------------------------- | ---------------------------------------------------------- |
| `format`          | `"via [$symbol$version]($style) "` | 组件格式化模板。                                                   |
| `symbol`          | `"📦 "`                             | The symbol used before displaying the version the package. |
| `style`           | `"bold 208"`                       | 此组件的样式。                                                    |
| `display_private` | `false`                            | Enable displaying version for packages marked as private.  |
| `disabled`        | `false`                            | Disables the `package` module.                             |

### Variables

| 字段        | 示例       | 描述                          |
| --------- | -------- | --------------------------- |
| version   | `v1.0.0` | The version of your package |
| symbol    |          | `symbol`对应值                 |
| style\* |          | `style`对应值                  |

\*: This variable can only be used as a part of a style string

### 示例

```toml
# ~/.config/starship.toml

[package]
format = "via [🎁 $version](208 bold) "
```

## Perl

The `perl` module shows the currently installed version of Perl. The module will be shown if any of the following conditions are met:

- The current directory contains a `Makefile.PL` or `Build.PL` file
- The current directory contains a `cpanfile` or `cpanfile.snapshot` file
- The current directory contains a `META.json` file or `META.yml` file
- The current directory contains a `.perl-version` file
- The current directory contains a `.pl`, `.pm` or `.pod`

### 配置项

| Option     | 默认值                                | 描述                                                    |
| ---------- | ---------------------------------- | ----------------------------------------------------- |
| `format`   | `"via [$symbol$version]($style) "` | The format string for the module.                     |
| `symbol`   | `"🐪 "`                             | The symbol used before displaying the version of Perl |
| `style`    | `"bold 149"`                       | 此组件的样式。                                               |
| `disabled` | `false`                            | Disables the `perl` module.                           |

### Variables

| 字段        | 示例        | 描述                    |
| --------- | --------- | --------------------- |
| version   | `v5.26.1` | The version of `perl` |
| symbol    |           | `symbol`对应值           |
| style\* |           | `style`对应值            |

### 示例

```toml
# ~/.config/starship.toml

[perl]
format = "via [🦪 $version]($style) "
```

## PHP

The `php` module shows the currently installed version of PHP. The module will be shown if any of the following conditions are met:

- The current directory contains a `composer.json` file
- The current directory contains a `.php-version` file
- The current directory contains a `.php` file

### 配置项

| Option     | 默认值                                | 描述                                                    |
| ---------- | ---------------------------------- | ----------------------------------------------------- |
| `format`   | `"via [$symbol$version]($style) "` | 组件格式化模板。                                              |
| `symbol`   | `"🐘 "`                             | The symbol used before displaying the version of PHP. |
| `style`    | `"147 bold"`                       | 此组件的样式。                                               |
| `disabled` | `false`                            | Disables the `php` module.                            |

### Variables

| 字段        | 示例       | 描述                   |
| --------- | -------- | -------------------- |
| version   | `v7.3.8` | The version of `php` |
| symbol    |          | `symbol`对应值          |
| style\* |          | `style`对应值           |

\*: This variable can only be used as a part of a style string

### 示例

```toml
# ~/.config/starship.toml

[php]
format = "via [🔹 $version](147 bold) "
```

## PureScript

The `purescript` module shows the currently installed version of PureScript version. The module will be shown if any of the following conditions are met:

- The current directory contains a `spago.dhall` file
- The current directory contains a \*.purs files

### 配置项

| Option     | 默认值                                | 描述                                                           |
| ---------- | ---------------------------------- | ------------------------------------------------------------ |
| `format`   | `"via [$symbol$version]($style) "` | 组件格式化模板。                                                     |
| `symbol`   | `"<=> "`                     | The symbol used before displaying the version of PureScript. |
| `style`    | `"bold white"`                     | 此组件的样式。                                                      |
| `disabled` | `false`                            | Disables the `purescript` module.                            |

### Variables

| 字段        | 示例       | 描述                          |
| --------- | -------- | --------------------------- |
| version   | `0.13.5` | The version of `purescript` |
| symbol    |          | `symbol`对应值                 |
| style\* |          | `style`对应值                  |

\*: This variable can only be used as a part of a style string

### 示例

```toml
# ~/.config/starship.toml

[purescript]
format = "via [$symbol$version](bold white)"
```

## Python

The `python` module shows the currently installed version of Python and the current Python virtual environment if one is activated.

If `pyenv_version_name` is set to `true`, it will display the pyenv version name. Otherwise, it will display the version number from `python --version`.

The module will be shown if any of the following conditions are met:

- The current directory contains a `.python-version` file
- The current directory contains a `requirements.txt` file
- The current directory contains a `pyproject.toml` file
- The current directory contains a file with the `.py` extension (and `scan_for_pyfiles` is true)
- The current directory contains a `Pipfile` file
- The current directory contains a `tox.ini` file
- The current directory contains a `setup.py` file
- The current directory contains a `__init__.py` file
- A virtual environment is currently activated

### 配置项

| Option               | 默认值                                                                       | 描述                                                                            |
| -------------------- | ------------------------------------------------------------------------- | ----------------------------------------------------------------------------- |
| `format`             | `'via [${symbol}${pyenv_prefix}${version}( \($virtualenv\))]($style) '` | 组件格式化模板。                                                                      |
| `symbol`             | `"🐍 "`                                                                    | A format string representing the symbol of Python                             |
| `style`              | `"yellow bold"`                                                           | 此组件的样式。                                                                       |
| `pyenv_version_name` | `false`                                                                   | Use pyenv to get Python version                                               |
| `pyenv_prefix`       | `pyenv`                                                                   | Prefix before pyenv version display, only used if pyenv is used               |
| `scan_for_pyfiles`   | `true`                                                                    | If false, Python files in the current directory will not show this module.    |
| `python_binary`      | `python`                                                                  | Configures the python binary that Starship executes when getting the version. |
| `disabled`           | `false`                                                                   | Disables the `python` module.                                                 |

### Variables

| 字段           | 示例              | 描述                                         |
| ------------ | --------------- | ------------------------------------------ |
| version      | `"v3.8.1"`      | The version of `python`                    |
| symbol       | `"🐍 "`          | `symbol`对应值                                |
| style        | `"yellow bold"` | `style`对应值                                 |
| pyenv_prefix | `"pyenv "`      | Mirrors the value of option `pyenv_prefix` |
| virtualenv   | `"venv"`        | The current `virtualenv` name              |


### 示例

```toml
# ~/.config/starship.toml

[python]
symbol = "👾 "
pyenv_version_name = true
```

Using the `python3` binary to get the version.

Note - The `python_binary` variable changes the binary that Starship executes to get the version of Python, it doesn't change the arguments that are used.

```toml
# ~/.config/starship.toml

[python]
python_binary = "python3"
```

## Ruby

The `ruby` module shows the currently installed version of Ruby. The module will be shown if any of the following conditions are met:

- The current directory contains a `Gemfile` file
- The current directory contains a `.ruby-version` file
- The current directory contains a `.rb` file

### 配置项

| Option     | 默认值                                | 描述                                               |
| ---------- | ---------------------------------- | ------------------------------------------------ |
| `format`   | `"via [$symbol$version]($style) "` | 组件格式化模板。                                         |
| `symbol`   | `"💎 "`                             | A format string representing the symbol of Ruby. |
| `style`    | `"bold red"`                       | 此组件的样式。                                          |
| `disabled` | `false`                            | Disables the `ruby` module.                      |

### Variables

| 字段        | 示例       | 描述                    |
| --------- | -------- | --------------------- |
| version   | `v2.5.1` | The version of `ruby` |
| symbol    |          | `symbol`对应值           |
| style\* |          | `style`对应值            |

\*: This variable can only be used as a part of a style string

### 示例

```toml
# ~/.config/starship.toml

[ruby]
symbol = "🔺 "
```

## Rust

The `rust` module shows the currently installed version of Rust. The module will be shown if any of the following conditions are met:

- The current directory contains a `Cargo.toml` file
- The current directory contains a file with the `.rs` extension

### 配置项

| Option     | 默认值                                | 描述                                              |
| ---------- | ---------------------------------- | ----------------------------------------------- |
| `format`   | `"via [$symbol$version]($style) "` | 组件格式化模板。                                        |
| `symbol`   | `"🦀 "`                             | A format string representing the symbol of Rust |
| `style`    | `"bold red"`                       | 此组件的样式。                                         |
| `disabled` | `false`                            | Disables the `rust` module.                     |

### Variables

| 字段        | 示例                | 描述                     |
| --------- | ----------------- | ---------------------- |
| version   | `v1.43.0-nightly` | The version of `rustc` |
| symbol    |                   | `symbol`对应值            |
| style\* |                   | `style`对应值             |

\*: This variable can only be used as a part of a style string

### 示例

```toml
# ~/.config/starship.toml

[rust]
format = "via [⚙️ $version](red bold)"
```

## SHLVL

The `shlvl` module shows the current SHLVL ("shell level") environment variable, if it is set to a number and meets or exceeds the specified threshold.

### 配置项

| Option      | 默认值                          | 描述                                      |
| ----------- | ---------------------------- | --------------------------------------- |
| `threshold` | `2`                          | Display threshold.                      |
| `format`    | `"[$symbol$shlvl]($style) "` | 组件格式化模板。                                |
| `symbol`    | `"↕️ "`                      | The symbol used to represent the SHLVL. |
| `style`     | `"bold yellow"`              | 此组件的样式。                                 |
| `disabled`  | `true`                       | Disables the `shlvl` module.            |

### Variables

| 字段        | 示例  | 描述                         |
| --------- | --- | -------------------------- |
| shlvl     | `3` | The current value of SHLVL |
| symbol    |     | `symbol`对应值                |
| style\* |     | `style`对应值                 |

\*: This variable can only be used as a part of a style string

### 示例

```toml
# ~/.config/starship.toml

[shlvl]
disabled = false
format = "$shlvl level(s) down"
threshold = 3
```

## Singularity

The `singularity` module shows the current singularity image, if inside a container and `$SINGULARITY_NAME` is set.

### 配置项

| Option     | 默认值                              | 描述                                               |
| ---------- | -------------------------------- | ------------------------------------------------ |
| `format`   | `'[$symbol\[$env\]]($style) '` | 组件格式化模板。                                         |
| `symbol`   | `""`                             | A format string displayed before the image name. |
| `style`    | `"bold dimmed blue"`             | 此组件的样式。                                          |
| `disabled` | `false`                          | Disables the `singularity` module.               |

### Variables

| 字段        | 示例           | 描述                            |
| --------- | ------------ | ----------------------------- |
| env       | `centos.img` | The current singularity image |
| symbol    |              | `symbol`对应值                   |
| style\* |              | `style`对应值                    |

\*: This variable can only be used as a part of a style string

### 示例

```toml
# ~/.config/starship.toml

[singularity]
format = '[📦 \[$env\]]($style) '
```

## Status

The `status` module displays the exit code of the previous command. The module will be shown only if the exit code is not `0`.

::: tip

This module is disabled by default. To enable it, set `disabled` to `false` in your configuration file. :::

### 配置项

| Option     | 默认值                        | 描述                                                     |
| ---------- | -------------------------- | ------------------------------------------------------ |
| `format`   | `[$symbol$status]($style)` | The format of the module                               |
| `symbol`   | `"✖"`                      | A format string representing the symbol for the status |
| `style`    | `"bold red"`               | 此组件的样式。                                                |
| `disabled` | `true`                     | Disables the `status` module.                          |

### Variables

| 字段        | 示例    | 描述                                |
| --------- | ----- | --------------------------------- |
| status    | `127` | The exit code of the last command |
| symbol    |       | `symbol`对应值                       |
| style\* |       | `style`对应值                        |

\*: This variable can only be used as a part of a style string

### 示例

```toml

# ~/.config/starship.toml

[status]
style = "bg:blue"
symbol = "💣 "
format = '[\[$symbol$status\]]($style) '
disabled = false

```

## Swift

The `swift` module shows the currently installed version of Swift. The module will be shown if any of the following conditions are met:

- The current directory contains a `Package.swift` file
- The current directory contains a file with the `.swift` extension

### 配置项

| Option     | 默认值                                | 描述                                               |
| ---------- | ---------------------------------- | ------------------------------------------------ |
| `format`   | `"via [$symbol$version]($style) "` | 组件格式化模板。                                         |
| `symbol`   | `"🐦 "`                             | A format string representing the symbol of Swift |
| `style`    | `"bold 202"`                       | 此组件的样式。                                          |
| `disabled` | `false`                            | Disables the `swift` module.                     |

### Variables

| 字段        | 示例       | 描述                     |
| --------- | -------- | ---------------------- |
| version   | `v5.2.4` | The version of `swift` |
| symbol    |          | `symbol`对应值            |
| style\* |          | `style`对应值             |

\*: This variable can only be used as a part of a style string

### 示例

```toml
# ~/.config/starship.toml

[swift]
format = "via [🏎  $version](red bold)"
```

## Terraform

The `terraform` module shows the currently selected terraform workspace and version. By default the terraform version is not shown, since this is slow on current versions of terraform when a lot of plugins are in use. If you still want to enable it, [follow the example shown below](#with-version). The module will be shown if any of the following conditions are met:

- The current directory contains a `.terraform` folder
- Current directory contains a file with the `.tf` or `.hcl` extensions

### 配置项

| Option     | 默认值                                  | 描述                                                    |
| ---------- | ------------------------------------ | ----------------------------------------------------- |
| `format`   | `"via [$symbol$workspace]($style) "` | The format string for the module.                     |
| `symbol`   | `"💠 "`                               | A format string shown before the terraform workspace. |
| `style`    | `"bold 105"`                         | 此组件的样式。                                               |
| `disabled` | `false`                              | Disables the `terraform` module.                      |

### Variables

| 字段        | 示例         | 描述                              |
| --------- | ---------- | ------------------------------- |
| version   | `v0.12.24` | The version of `terraform`      |
| workspace | `default`  | The current terraform workspace |
| symbol    |            | `symbol`对应值                     |
| style\* |            | `style`对应值                      |

\*: This variable can only be used as a part of a style string

### 示例

#### With Version

```toml
# ~/.config/starship.toml

[terraform]
format = "[🏎💨 $version$workspace]($style) "
```

#### Without version

```toml
# ~/.config/starship.toml

[terraform]
format = "[🏎💨 $workspace]($style) "
```

## Time

The `time` module shows the current **local** time. The `format` configuration value is used by the [`chrono`](https://crates.io/crates/chrono) crate to control how the time is displayed. Take a look [at the chrono strftime docs](https://docs.rs/chrono/0.4.7/chrono/format/strftime/index.html) to see what options are available.

::: tip

This module is disabled by default. To enable it, set `disabled` to `false` in your configuration file.

:::

### 配置项

| Option            | 默认值                     | 描述                                                                                                                                 |
| ----------------- | ----------------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| `format`          | `"at [$time]($style) "` | The format string for the module.                                                                                                  |
| `use_12hr`        | `false`                 | Enables 12 hour formatting                                                                                                         |
| `time_format`     | see below               | The [chrono format string](https://docs.rs/chrono/0.4.7/chrono/format/strftime/index.html) used to format the time.                |
| `style`           | `"bold yellow"`         | The style for the module time                                                                                                      |
| `utc_time_offset` | `"local"`               | Sets the UTC offset to use. Range from -24 &lt; x &lt; 24. Allows floats to accommodate 30/45 minute timezone offsets. |
| `disabled`        | `true`                  | Disables the `time` module.                                                                                                        |
| `time_range`      | `"-"`                   | Sets the time range during which the module will be shown. Times must be specified in 24-hours format                              |

If `use_12hr` is `true`, then `time_format` defaults to `"%r"`. Otherwise, it defaults to `"%T"`. Manually setting `time_format` will override the `use_12hr` setting.

### Variables

| 字段        | 示例         | 描述                |
| --------- | ---------- | ----------------- |
| time      | `13:08:10` | The current time. |
| style\* |            | `style`对应值        |

\*: This variable can only be used as a part of a style string

### 示例

```toml
# ~/.config/starship.toml

[time]
disabled = false
format = '🕙[\[ $time \]]($style) '
time_format = "%T"
utc_time_offset = "-5"
time_range = "10:00:00-14:00:00"
```

## Username

The `username` module shows active user's username. The module will be shown if any of the following conditions are met:

- The current user is root
- The current user isn't the same as the one that is logged in
- The user is currently connected as an SSH session
- The variable `show_always` is set to true

### 配置项

| Option        | 默认值                     | 描述                                    |
| ------------- | ----------------------- | ------------------------------------- |
| `style_root`  | `"bold red"`            | The style used when the user is root. |
| `style_user`  | `"bold yellow"`         | The style used for non-root users.    |
| `format`      | `"[$user]($style) in "` | 组件格式化模板。                              |
| `show_always` | `false`                 | Always shows the `username` module.   |
| `disabled`    | `false`                 | Disables the `username` module.       |

### Variables

| 字段      | 示例           | 描述                                                                                          |
| ------- | ------------ | ------------------------------------------------------------------------------------------- |
| `style` | `"red bold"` | Mirrors the value of option `style_root` when root is logged in and `style_user` otherwise. |
| `user`  | `"matchai"`  | The currently logged-in user ID.                                                            |

### 示例

```toml
# ~/.config/starship.toml

[username]
style_user = "white bold"
style_root = "black bold"
format = "user: [$user]($style) "
disabled = false
show_always = true
```

## Zig

The `zig` module shows the currently installed version of Zig. The module will be shown if any of the following conditions are met:

- The current directory contains a `.zig` file

### 配置项

| Option     | 默认值                                | 描述                                                    |
| ---------- | ---------------------------------- | ----------------------------------------------------- |
| `symbol`   | `"↯ "`                             | The symbol used before displaying the version of Zig. |
| `style`    | `"bold yellow"`                    | 此组件的样式。                                               |
| `format`   | `"via [$symbol$version]($style) "` | 组件格式化模板。                                              |
| `disabled` | `false`                            | Disables the `zig` module.                            |

### Variables

| 字段        | 示例       | 描述                   |
| --------- | -------- | -------------------- |
| version   | `v0.6.0` | The version of `zig` |
| symbol    |          | `symbol`对应值          |
| style\* |          | `style`对应值           |

\*: This variable can only be used as a part of a style string

### 示例

```toml
# ~/.config/starship.toml

[zig]
symbol = "⚡️ "
```

## Custom commands

The `custom` modules show the output of some arbitrary commands.

These modules will be shown if any of the following conditions are met:

- The current directory contains a file whose name is in `files`
- The current directory contains a directory whose name is in `directories`
- The current directory contains a file whose extension is in `extensions`
- The `when` command returns 0

::: tip

Multiple custom modules can be defined by using a `.`.

:::

::: tip

The order in which custom modules are shown can be individually set by including `${custom.foo}` in the top level `format` (as it includes a dot, you need to use `${...}`). By default, the `custom` module will simply show all custom modules in the order they were defined.

:::

::: tip

[Issue #1252](https://github.com/starship/starship/discussions/1252) contains examples of custom modules. If you have an interesting example not covered there, feel free to share it there!

:::

### 配置项

| Option        | 默认值                           | 描述                                                                                                                         |
| ------------- | ----------------------------- | -------------------------------------------------------------------------------------------------------------------------- |
| `command`     |                               | The command whose output should be printed. The command will be passed on stdin to the shell.                              |
| `when`        |                               | A shell command used as a condition to show the module. The module will be shown if the command returns a `0` status code. |
| `shell`       |                               | [See below](#custom-command-shell)                                                                                         |
| `description` | `"<custom module>"`     | The description of the module that is shown when running `starship explain`.                                               |
| `files`       | `[]`                          | The files that will be searched in the working directory for a match.                                                      |
| `directories` | `[]`                          | The directories that will be searched in the working directory for a match.                                                |
| `extensions`  | `[]`                          | The extensions that will be searched in the working directory for a match.                                                 |
| `symbol`      | `""`                          | The symbol used before displaying the command output.                                                                      |
| `style`       | `"bold green"`                | 此组件的样式。                                                                                                                    |
| `format`      | `"[$symbol$output]($style) "` | 组件格式化模板。                                                                                                                   |
| `disabled`    | `false`                       | Disables this `custom` module.                                                                                             |

### Variables

| 字段        | 描述                                     |
| --------- | -------------------------------------- |
| output    | The output of shell command in `shell` |
| symbol    | `symbol`对应值                            |
| style\* | `style`对应值                             |

\*: This variable can only be used as a part of a style string

#### Custom command shell

`shell` accepts a non-empty list of strings, where:

- The first string is the path to the shell to use to execute the command.
- Other following arguments are passed to the shell.

If unset, it will fallback to STARSHIP_SHELL and then to "sh" on Linux, and "cmd /C" on Windows.

The `command` will be passed in on stdin.

If `shell` is not given or only contains one element and Starship detects PowerShell will be used, the following arguments will automatically be added: `-NoProfile -Command -`. This behavior can be avoided by explicitly passing arguments to the shell, e.g.

```toml
shell = ["pwsh", "-Command", "-"]
```

::: warning Make sure your custom shell configuration exits gracefully

If you set a custom command, make sure that the default Shell used by starship will properly execute the command with a graceful exit (via the `shell` option).

For example, PowerShell requires the `-Command` parameter to execute a one liner. Omitting this parameter might throw starship into a recursive loop where the shell might try to load a full profile environment with starship itself again and hence re-execute the custom command, getting into a never ending loop.

Parameters similar to `-NoProfile` in PowerShell are recommended for other shells as well to avoid extra loading time of a custom profile on every starship invocation.

Automatic detection of shells and proper parameters addition are currently implemented, but it's possible that not all shells are covered. [Please open an issue](https://github.com/starship/starship/issues/new/choose) with shell details and starship configuration if you hit such scenario.

:::

### 示例

```toml
# ~/.config/starship.toml

[custom.foo]
command = "echo foo"  # shows output of command
files = ["foo"]       # can specify filters
when = """ test "$HOME" == "$PWD" """
format = " transcending [$output]($style)"

[custom.time]
command = "time /T"
files = ["*.pst"]
shell = ["pwsh.exe", "-NoProfile", "-Command", "-"]
```
