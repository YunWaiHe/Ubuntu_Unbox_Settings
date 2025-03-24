# ZSH install and config

### INSTALL

zsh omz
```shell
# install zsh
sudo apt install zsh

# if need proxy
sudo echo 'alias proxy="export http_proxy=http://127.0.0.1:10809;export https_proxy=http://127.0.0.1:10809"' | sudo tee -a /etc/bash.bashrc
sudo echo 'alias unproxy="unset http_proxy;unset https_proxy"' | sudo tee -a /etc/bash.bashrc

sudo echo 'alias proxy="export http_proxy=http://127.0.0.1:10809;export https_proxy=http://127.0.0.1:10809"' | sudo tee -a /etc/zsh/zshrc
sudo echo 'alias unproxy="unset http_proxy;unset https_proxy"' | sudo tee -a /etc/zsh/zshrc

# install omz(oh-my-zsh)
sh -c "$(wget https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh -O -)"
```

plugin
```shell
# plugins 
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
git clone https://github.com/chrissicool/zsh-256color ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-256color

# install if you use conda
git clone https://github.com/conda-incubator/conda-zsh-completion.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/conda-zsh-completion
```

```shell
cat << 'EOF' | tee ~/.local/bin/update_omz_custom_plugin.sh
cd ~/.oh-my-zsh/custom/plugins
for plugin in */; do
  if [ -d "$plugin/.git" ]; then
     printf "${YELLOW}%s${RESET}\n" "${plugin%/}"
     git -C "$plugin" pull
  fi
done
EOF

chmod +x ~/.local/bin/update_omz_custom_plugin.sh
```

### CONFIG

[一份简略的ZSH PROMPT自定义指南](./ZSH_THEME_PROMPT_指南.md)

#### non-root user

my_maran.zsh-theme

```shell
cat << 'EOF' | tee $ZSH_CUSTOM/themes/my_maran.zsh-theme
PROMPT='%{$fg[cyan]%}%n%{%f%}@%{$fg[yellow]%}%M:%{$fg[magenta]%}%~ $(git_prompt_info)$(git_prompt_status)%{%f%u%}%(?,,%{$fg[red]%})$%(?,,%{%f%}) '
RPROMPT='%{$fg[blue]%}%*%{$fg[default]%}'

ZSH_THEME_GIT_PROMPT_PREFIX="%{$fg[blue]%}["
ZSH_THEME_GIT_PROMPT_SUFFIX=""
ZSH_THEME_GIT_PROMPT_DIRTY="%{$fg[red]%}-dirty%{$fg[blue]%}]%{%U%}"
ZSH_THEME_GIT_PROMPT_CLEAN="%{$fg[green]%}-clean%{$fg[blue]%}]"

ZSH_THEME_GIT_PROMPT_UNTRACKED="%{$fg[190]%}-untracked"
ZSH_THEME_GIT_PROMPT_ADDED="%{$fg[082]%}}-added"
ZSH_THEME_GIT_PROMPT_MODIFIED="%{$fg[166]%}-modified"
ZSH_THEME_GIT_PROMPT_RENAMED="%{$fg[220]%}-renamed"
ZSH_THEME_GIT_PROMPT_DELETED="%{$fg[160]%}-deleted"
ZSH_THEME_GIT_PROMPT_UNMERGED="%{$fg[082]%}-unmerged"
ZSH_THEME_GIT_PROMPT_AHEAD="%{$fg[cyan]%}-AHEAD"
ZSH_THEME_GIT_PROMPT_BEHIND="%{$fg[magenta]%}-BEHIND"
ZSH_THEME_GIT_PROMPT_DIVERGED="%{$fg_bold[red]%}--DIVERGED"
ZSH_THEME_GIT_PROMPT_STASHED="%{$fg_bold[blue]%}-STASHED"

EOF
```

.zshrc

```shell
cat << 'EOF' | tee ~/.zshrc
export PATH=$HOME/.local/bin:$PATH
export ZSH="$HOME/.oh-my-zsh"
ZSH_THEME="my_maran"

plugins=(git
sudo
history
colored-man-pages
command-not-found
# virtualenvwrapper
zsh-256color
zsh-autosuggestions
zsh-syntax-highlighting
# conda-zsh-completion
)

export ZSH_COMPDUMP=$ZSH/cache/.zcompdump-$HOST
source $ZSH/oh-my-zsh.sh

alias ls='ls --color=auto'
alias dir='dir --color=auto'
alias vdir='vdir --color=auto'    
alias grep='grep --color=auto'
alias fgrep='fgrep --color=auto'
alias egrep='egrep --color=auto'
alias ll='ls -alF'
alias la='ls -A'
alias l='ls -CF'

alias x11_as_root='sudo xauth add $(xauth list $DISPLAY)'

setopt nonomatch

# completion rehash
autoload -Uz compinit && compinit
zstyle ':completion:*' rehash true

# zsh history behavior
setopt BANG_HIST                # Treat the '!' character specially during expansion.
setopt INC_APPEND_HISTORY       # Write to the history file immediately, not when the shell exits.
setopt HIST_EXPIRE_DUPS_FIRST   # Expire duplicate entries first when trimming history.
setopt HIST_IGNORE_DUPS         # Don't record an entry that was just recorded again.
setopt HIST_IGNORE_ALL_DUPS     # Delete old recorded entry if new entry is a duplicate.
setopt HIST_FIND_NO_DUPS        # Do not display a line previously found.
setopt HIST_IGNORE_SPACE        # Don't record an entry starting with a space.
setopt HIST_SAVE_NO_DUPS        # Don't write duplicate entries in the history file.
setopt HIST_REDUCE_BLANKS       # Remove superfluous blanks before recording entry.
setopt HIST_VERIFY              # Don't execute immediately upon history expansion.
setopt SHARE_HISTORY            # Share history between all sessions.
unsetopt EXTENDED_HISTORY       # DO NOT Write the history file in the ":start:elapsed;command" format.

# Xshell key
bindkey '\e[1~' beginning-of-line   # Home
bindkey '\e[4~' end-of-line         # End
bindkey -s "^[Op" "0"   # 0
bindkey -s "^[Ol" "."   # .
bindkey -s "^[OM" "^M"  # Enter
bindkey -s "^[Oq" "1"   # 1
bindkey -s "^[Or" "2"   # 2
bindkey -s "^[Os" "3"   # 3
bindkey -s "^[Ot" "4"   # 4
bindkey -s "^[Ou" "5"   # 5
bindkey -s "^[Ov" "6"   # 6
bindkey -s "^[Ow" "7"   # 7
bindkey -s "^[Ox" "8"   # 8
bindkey -s "^[Oy" "9"   # 9
bindkey -s "^[Ok" "+"   # +
bindkey -s "^[Om" "-"   # -
bindkey -s "^[Oj" "*"   # *
bindkey -s "^[Oo" "/"   # /

EOF
```

#### root user

before config

```shell
sudo -i
```

my_maran.zsh-theme
```shell
cat << 'EOF' | tee $ZSH_CUSTOM/themes/my_maran.zsh-theme
PROMPT='%{$fg_bold[white]$bg[red]%}%n%{%f%k%}@%{$fg[blue]%}%M:%{$fg[magenta]%}%~ $(git_prompt_info)$(git_prompt_status)%{%f%u%}%(?,,%{$fg[red]%})#%(?,,%{%f%b%}) '
RPROMPT='%{$fg[cyan]%}%*%{$fg[default]%}'

ZSH_THEME_GIT_PROMPT_PREFIX="%{$fg[blue]%}["
ZSH_THEME_GIT_PROMPT_SUFFIX=""
ZSH_THEME_GIT_PROMPT_DIRTY="%{$fg[red]%}-dirty%{$fg[blue]%}]%{%U%}"
ZSH_THEME_GIT_PROMPT_CLEAN="%{$fg[green]%}-clean%{$fg[blue]%}]"

ZSH_THEME_GIT_PROMPT_UNTRACKED="%{$fg[190]%}-untracked"
ZSH_THEME_GIT_PROMPT_ADDED="%{$fg[082]%}-added"
ZSH_THEME_GIT_PROMPT_MODIFIED="%{$fg[166]%}-modified"
ZSH_THEME_GIT_PROMPT_RENAMED="%{$fg[220]%}-renamed"
ZSH_THEME_GIT_PROMPT_DELETED="%{$fg[160]%}-deleted"
ZSH_THEME_GIT_PROMPT_UNMERGED="%{$fg[082]%}-unmerged"
ZSH_THEME_GIT_PROMPT_AHEAD="%{$fg[cyan]%}-AHEAD"
ZSH_THEME_GIT_PROMPT_BEHIND="%{$fg[magenta]%}-BEHIND"
ZSH_THEME_GIT_PROMPT_DIVERGED="%{$fg_bold[red]%}-DIVERGED"
ZSH_THEME_GIT_PROMPT_STASHED="%{$fg_bold[blue]%}-STASHED"
EOF
```

.zshrc
```shell
cat << 'EOF' | tee ~/.zshrc
export PATH=$HOME/.local/bin:$PATH
export ZSH="$HOME/.oh-my-zsh"
ZSH_THEME="my_maran"

plugins=(git
sudo
history
colored-man-pages
command-not-found
virtualenvwrapper
zsh-256color
zsh-autosuggestions
zsh-syntax-highlighting
)

export ZSH_COMPDUMP=$ZSH/cache/.zcompdump-$HOST
source $ZSH/oh-my-zsh.sh

alias ls='ls --color=auto'
alias dir='dir --color=auto'
alias vdir='vdir --color=auto'    
alias grep='grep --color=auto'
alias fgrep='fgrep --color=auto'
alias egrep='egrep --color=auto'
alias ll='ls -alF'
alias la='ls -A'
alias l='ls -CF'

setopt nonomatch

# completion rehash
autoload -Uz compinit && compinit
zstyle ':completion:*' rehash true

# History behavior
setopt BANG_HIST                # Treat the '!' character specially during expansion.
setopt INC_APPEND_HISTORY       # Write to the history file immediately, not when the shell exits.
setopt HIST_EXPIRE_DUPS_FIRST   # Expire duplicate entries first when trimming history.
setopt HIST_FIND_NO_DUPS        # Do not display a line previously found.
setopt HIST_REDUCE_BLANKS       # Remove superfluous blanks before recording entry.
setopt HIST_VERIFY              # Don't execute immediately upon history expansion.
# espically for root
setopt EXTENDED_HISTORY         # Write the history file in the ":start:elapsed;command" format.
unsetopt HIST_IGNORE_DUPS       # record an entry that was just recorded again.
unsetopt HIST_IGNORE_ALL_DUPS   # DO NOT Delete old recorded entry if new entry is a duplicate.
unsetopt HIST_IGNORE_SPACE      # record an entry starting with a space.
unsetopt HIST_SAVE_NO_DUPS      # write duplicate entries in the history file.
unsetopt SHARE_HISTORY          # DO NOT Share history between all sessions.

# Xshell key
bindkey '\e[1~' beginning-of-line   # Home
bindkey '\e[4~' end-of-line         # End
bindkey -s "^[Op" "0"   # 0
bindkey -s "^[Ol" "."   # .
bindkey -s "^[OM" "^M"  # Enter
bindkey -s "^[Oq" "1"   # 1
bindkey -s "^[Or" "2"   # 2
bindkey -s "^[Os" "3"   # 3
bindkey -s "^[Ot" "4"   # 4
bindkey -s "^[Ou" "5"   # 5
bindkey -s "^[Ov" "6"   # 6
bindkey -s "^[Ow" "7"   # 7
bindkey -s "^[Ox" "8"   # 8
bindkey -s "^[Oy" "9"   # 9
bindkey -s "^[Ok" "+"   # +
bindkey -s "^[Om" "-"   # -
bindkey -s "^[Oj" "*"   # *
bindkey -s "^[Oo" "/"   # /

EOF
```
