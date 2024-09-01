## Terminal

```shell
sudo apt install dconf-editor

# 示例配置
cat << 'EOF'| tee gnome_terminal_dracula_theme.txt
[/]
background-color='#282A36'
bold-color='#6E46A4'
bold-color-same-as-fg=true
bold-is-bright=true
custom-command='/usr/bin/zsh'
default-size-columns=96
foreground-color='#F8F8F2'
palette=['rgb(33,34,44)', 'rgb(255,85,85)', 'rgb(80,250,123)', 'rgb(241,250,140)', 'rgb(0,175,255)', 'rgb(255,121,198)', 'rgb(139,233,253)', 'rgb(248,248,242)', 'rgb(98,114,164)', 'rgb(255,110,110)', 'rgb(105,255,148)', 'rgb(255,255,165)', 'rgb(0,215,255)', 'rgb(255,146,223)', 'rgb(164,255,255)', 'rgb(255,255,255)']
use-custom-command=true
use-theme-colors=true
visible-name='Dracula'
EOF
# 新建配置，去dconf-editor复制路径7512993d-2577-4f9b-a035-2959d776c1dd
dconf load /org/gnome/terminal/legacy/profiles:/:7512993d-2577-4f9b-a035-2959d776c1dd/ < gnome_terminal_dracula_theme.txt
dconf write /org/gnome/terminal/legacy/profiles:/list "['b1dcc9dd-5262-4d8d-a863-c897e6d979b9','7512993d-2577-4f9b-a035-2959d776c1dd']"
# 完成后选中名为Dracula的配置文件

# 配置重置
dconf reset -f /org/gnome/terminal/
```

## Font

```shell
sudo apt install font-manager
sudo apt update && sudo apt install ttf-mscorefonts-installer

# 安装字体至系统范围
sudo cp *.ttf /usr/local/share/fonts/
sudo fc-cache -f -v # 重建字体缓存
```