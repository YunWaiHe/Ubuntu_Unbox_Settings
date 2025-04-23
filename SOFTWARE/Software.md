# Software

Column headers:

- **Name**: Name of software
- **Download/Product Link**:  Download link or other link (`spk:// `means link with Spark-Store format)
- **Add APT Repo**: 
  - **Auto**: Automatically add apt source after installation
  - **Manual**: Manual add apt source
  - **No Repo**:  There's no source to add
- **Extra Config**: Extra config



[TOC]

## App Stores

| Name        | Download/Product Link                          | Add APT Repo | Extra Config                  |
| ----------- | ---------------------------------------------- | ------------ | ----------------------------- |
| Snap Store  | https://snapcraft.io/install/snap-store/ubuntu |              | [Snap Store](#jump_snapstore) |
| Spark Store | https://www.spark-app.store                    |              |                               |



### Snap Store<span id="jump_snapstore"></span>

```shell
sudo apt update
sudo apt install snapd
sudo snap install --channel=latest/stable/ubuntu-24.04 snap-store
# refresh to default store of ubuntu 24.04 
sudo snap refresh snap-store --channel=latest/stable/ubuntu-24.04
```





## Browser

| Name          | Download/Product Link                                        | Add APT Repo                                                 | Extra Config             |
| ------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------ |
| Google Chrome | [google-chrome-stable_current_amd64.deb](https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb) | Auto                                                         | [Chrome](#jump_chrome)   |
| Firefox       | https://www.mozilla.org/en-US/firefox                        | [Manual](https://support.mozilla.org/en-US/kb/install-firefox-linux) | [Firefox](#jump_firefox) |
| AdsPower      | https://www.adspower.com/download                            | No Repo                                                      |                          |



### Google Chrome

#### 0. Installation


```shell
wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
sudo dpkg -i google-chrome-stable_current_amd64.deb
```

keep version 134

```shell
wget http://dl.google.com/linux/chrome/deb/pool/main/g/google-chrome-stable/google-chrome-stable_134.0.6998.165-1_amd64.deb
sudo dpkg -i google-chrome-stable_134.0.6998.165-1_amd64.deb
sudo apt-mark hold google-chrome-stable
```

Another way to downgrade:

```shell
wget -q https://pkgs.geos.ed.ac.uk/keys/GeoSciences-Package-Service.gpg -O- | sudo tee /etc/apt/keyrings/pkgs.geos.ed.ac.uk.asc

gpg -n -q --import --import-options import-show /etc/apt/keyrings/pkgs.geos.ed.ac.uk.asc | awk '/pub/{getline; gsub(/^ +| +$/,""); if($0 == "7859D35F0CD88A9E939AE644CB330CFDB2D2256F") print "\nThe key fingerprint matches ("$0").\n"; else print "\nVerification failed: the fingerprint ("$0") does not match the expected one.\n"}'

cat << 'EOF' | sudo tee /etc/apt/pkgs.geos.ed.ac.uk.list
deb [arch=amd64 signed-by=/etc/apt/keyrings/pkgs.geos.ed.ac.uk.asc] https://pkgs.geos.ed.ac.uk/chrome/ stable main
EOF

apt-cache madison google-chrome-stable
```



#### 1. Chrome Policies<span id='jump_chrome'></span>

https://chromeenterprise.google/policies/

[chrome://policy/](chrome://policy/)

Example Policies Config:

```shell
sudo mkdir -p /etc/opt/chrome/policies/managed
sudo touch /etc/opt/chrome/policies/managed/policy.json

cat << 'EOF' | sudo tee /etc/opt/chrome/policies/managed/policy.json
{
  "EnableMediaRouter": false,
  "LensDesktopNTPSearchEnabled": false,
  "LensOverlaySettings": 1,
  "LensRegionSearchEnabled": false,
  "FeedbackSurveysEnabled": false,
  "UserFeedbackAllowed": false,
  "HelpMeWriteSettings": 2,
  "GenAILocalFoundationalModelSettings": 1,
  "CreateThemesSettings": 2,
  "DevToolsGenAiSettings": 2,
  "HistorySearchSettings": 2,
  "TabOrganizerSettings": 2,
  "ExtensionManifestV2Availability": 2,
  "MetricsReportingEnabled": false,
  "PromotionsEnabled": false,
  "QuicAllowed": false,
  "SharedClipboardEnabled": false,
  "UrlKeyedAnonymizedDataCollectionEnabled": false,
  "WebRtcEventLogCollectionAllowed": false,
  "WebRtcTextLogCollectionAllowed": false,
  "DnsOverHttpsMode": "off",
  "RelatedWebsiteSetsEnabled": false,
  "RemoteAccessHostAllowRemoteAccessConnections": false,
  "RemoteAccessHostAllowRemoteSupportConnections": false,
  "RemoteAccessHostClipboardSizeBytes": 0,
  "WPADQuickCheckEnabled": false,
  "PasswordSharingEnabled": false,
  "PaymentMethodQueryEnabled": false,
  "PrivacySandboxAdMeasurementEnabled": false,
  "PrivacySandboxAdTopicsEnabled": false,
  "PrivacySandboxPromptEnabled": false,
  "PrivacySandboxSiteEnabledAdsEnabled": false,
  "ShoppingListEnabled": false
}
EOF
```

#### 2. Extensions

In general, extensions downloaded from the Chrome Web Store will automatically sync; local extensions and the majority of extension settings won't.

Remember to **BACKUP** and **RESTORE** !!! (local extensions and settings of extensions)



### FireFox

#### 0. Installation

https://support.mozilla.org/en-US/kb/install-firefox-linux#w_install-firefox-deb-package-for-debian-based-distributions

```shell
sudo install -d -m 0755 /etc/apt/keyrings
wget -q https://packages.mozilla.org/apt/repo-signing-key.gpg -O- | sudo tee /etc/apt/keyrings/packages.mozilla.org.asc > /dev/null

gpg -n -q --import --import-options import-show /etc/apt/keyrings/packages.mozilla.org.asc | awk '/pub/{getline; gsub(/^ +| +$/,""); if($0 == "35BAA0B33E9EB396F59CA838C0BA5CE6DC6315A3") print "\nThe key fingerprint matches ("$0").\n"; else print "\nVerification failed: the fingerprint ("$0") does not match the expected one.\n"}'

cat << 'EOF' | sudo tee /etc/apt/sources.list.d/mozilla.list
deb [signed-by=/etc/apt/keyrings/packages.mozilla.org.asc] https://packages.mozilla.org/apt mozilla main
EOF

# highly recommended
sudo snap remove --purge firefox
cat << 'EOF' | sudo tee /etc/apt/preferences.d/snap-apps-disable
Package: chromium* firefox* thunderbird*
Pin: version /.*snap.*/
Pin-Priority: -99
EOF
apt policy firefox

cat << 'EOF' | sudo tee /etc/apt/preferences.d/mozilla
Package: *
Pin: origin packages.mozilla.org
Pin-Priority: 1000
EOF

# installation
sudo apt update && sudo apt install firefox

# language pack
sudo apt install firefox-l10n-zh-cn

# check apt package from mozilla source
apt list | grep /mozilla
# firefox
sudo apt install firefox firefox-l10n-zh-cn
# firefox-beta
sudo apt install firefox-beta firefox-beta-l10n-zh-cn
# firefox-nightly
sudo apt install firefox-nightly firefox-nightly-l10n-zh-cn
# firefox-esr
sudo apt install firefox-esr firefox-esr-l10n-zh-cn
```

#### 1. Other settings<span id='jump_firefox'></span>

https://support.mozilla.org/en-US/kb/install-firefox-linux#w_data-migration

https://support.mozilla.org/en-US/kb/install-firefox-linux#w_security-features-warning



## Messaging

| Name            | Download/Product Link                 | Add APT Repo | Extra Config |
| --------------- | ------------------------------------- | ------------ | ------------ |
| Telegram        | https://desktop.telegram.org          | No Repo      |              |
| Discord         | https://discord.com/download          | No Repo      |              |
| Wexin for Linux | https://linux.weixin.qq.com           | No Repo      |              |
| QQ              | https://im.qq.com/linuxqq/index.shtml | No Repo      |              |



## Office

| Name                 | Download/Product Link                                        | Add APT Repo | Extra Config                     |
| -------------------- | ------------------------------------------------------------ | ------------ | -------------------------------- |
| WPS Office for Linux | https://www.wps.cn/product/wpslinux<br />https://www.wps.com/zh-hant/download | No Repo      | [WPS](#jump_wps)                 |
| Tencent Meeting      | https://meeting.tencent.com/download                         | No Repo      |                                  |
| Zotero               | https://www.zotero.org                                       | No Repo      | Extensions and WebDAV            |
| 训读PDF              | [spk://store/office/com.xundupdf.spark](spk://store/office/com.xundupdf.spark) | No Repo      |                                  |
| LibreOffice          | https://www.libreoffice.org/download                         | No Repo      | [LibreOffice](#jump_libreoffice) |
| draw.io              | https://www.drawio.com                                       | No Repo      |                                  |
| Typora               | https://typora.io                                            | Manual       | [Typora](#jump_typora)           |



### WPS<span id='jump_wps'></span>

Some missing fonts may be found at  [FontList.md](../APPEARANCE/FontList.md).



### LibreOffice<span id='jump_libreoffice'></span>

**(Optional)** Disable libreoffice from ubuntu source

```shell
cat << 'EOF' | sudo tee /etc/apt/preferences.d/libreoffice
Package: libreoffice*
Pin: release o=Ubuntu
Pin-Priority: -1
EOF
```



### Typora<span id='jump_typora'></span>

```shell
wget -qO - https://typora.io/linux/public-key.asc | sudo tee /etc/apt/trusted.gpg.d/typora.asc
sudo add-apt-repository 'deb https://typora.io/linux ./'
sudo apt update
sudo apt install typora pandoc

# Dracula Theme
wget https://github.com/Teyler7/dracula-typora-theme/archive/refs/heads/main.zip && unzip main.zip && rm main.zip
cp -r dracula-typora-theme-main/theme/* ~/.config/Typora/themes/

# base.user.css config
cd ~/.config/Typora/themes
cat << 'EOF' | tee base.user.css
.CodeMirror-wrap .CodeMirror-code pre {
  font-family: "Ubuntu Sans Mono","Noto Sans CJK SC";
}

body {
  font-family: "Noto Sans CJK SC";
}

:root {
  --monospace: "Ubuntu Sans Mono","Noto Sans CJK SC";
}
EOF

# Dracula CodeMirror Config
{ cat - dracula.css > tempcss && mv tempcss dracula.css; } <<'EOF'
.cm-s-inner.CodeMirror, .cm-s-inner .CodeMirror-gutters {
  background-color: #282a36 !important;
  color: #f8f8f2 !important;
  border: none !important;
}
.cm-s-inner .CodeMirror-gutters { color: #282a36; }
/* .cm-s-inner .CodeMirror-cursor { border-left: solid thin #f8f8f0 !important; } */
.cm-s-inner .CodeMirror-cursor { border-left: 1.6px solid #ffffff !important; }
.cm-s-inner .CodeMirror-linenumber { color: #6D8A88 !important; }
.cm-s-inner .CodeMirror-selected { background: rgba(255, 255, 255, 0.10) !important; }
.cm-s-inner .CodeMirror-line::selection, .cm-s-inner .CodeMirror-line > span::selection, .cm-s-inner .CodeMirror-line > span > span::selection { background: rgba(255, 255, 255, 0.10) !important; }
.cm-s-inner .CodeMirror-line::-moz-selection, .cm-s-inner .CodeMirror-line > span::-moz-selection, .cm-s-inner .CodeMirror-line > span > span::-moz-selection { background: rgba(255, 255, 255, 0.10) !important; }
.cm-s-inner span.cm-comment { color: #FF79C6 !important; }
.cm-s-inner span.cm-string, .cm-s-inner span.cm-string-2 { color: #f1fa8c !important; }
.cm-s-inner span.cm-number { color: #bd93f9 !important; }
.cm-s-inner span.cm-variable { color: #50fa7b !important; }
.cm-s-inner span.cm-variable-2 { color: white !important; }
.cm-s-inner span.cm-def { color: #50fa7b !important; }
.cm-s-inner span.cm-operator { color: #ff79c6 !important; }
.cm-s-inner span.cm-keyword { color: #ff79c6 !important; }
.cm-s-inner span.cm-atom { color: #bd93f9 !important; }
.cm-s-inner span.cm-meta { color: #f8f8f2 !important; }
.cm-s-inner span.cm-tag { color: #ff79c6 !important; }
.cm-s-inner span.cm-attribute { color: #50fa7b !important; }
.cm-s-inner span.cm-qualifier { color: #50fa7b !important; }
.cm-s-inner span.cm-property { color: #66d9ef !important; }
.cm-s-inner span.cm-builtin { color: #50fa7b !important; }
.cm-s-inner span.cm-variable-3, .cm-s-inner span.cm-type { color: #ffb86c !important; }
.cm-s-inner .CodeMirror-activeline-background { background: rgba(255,255,255,0.1) !important; }
.cm-s-inner .CodeMirror-matchingbracket { text-decoration: underline; color: white !important; }

EOF


sed -i 's/  --code-cursor-color: var(--dracula-current-line);/  --code-cursor-color: #ffffff !important;/g' ./dracula.css
```



## Image

| Name     | Download/Product Link | Add APT Repo | Extra Config |
| -------- | --------------------- | ------------ | ------------ |
| nomacs   | apt install nomacs    |              |              |
| shotwell | apt install shotwell  |              |              |
| GIMP     | apt install gimp      |              |              |

## Audio

| Name | Download/Product Link | Add APT Repo | Extra Config |
| ---- | --------------------- | ------------ | ------------ |
|      |                       |              |              |

## Video

| Name | Download/Product Link | Add APT Repo | Extra Config |
| ---- | --------------------- | ------------ | ------------ |
| vlc  | apt install vlc       |              |              |
| mpv  | apt install mpv       |              |              |
| GIMP | apt install gimp      |              |              |



## Downloader

| Name   | Download/Product Link       | Add APT Repo | Extra Config |
| ------ | --------------------------- | ------------ | ------------ |
| Motrix | https://motrix.app/download | No Repo      |              |



## Cloud Drive

| Name          | Download/Product Link                | Add APT Repo | Extra Config |
| ------------- | ------------------------------------ | ------------ | ------------ |
| Baidu NetDisk | https://pan.baidu.com/download#linux | No Repo      |              |



## Remote Desktop

| Name     | Download/Product Link | Add APT Repo | Extra Config |
| -------- | --------------------- | ------------ | ------------ |
| RustDesk | https://rustdesk.com  | No Repo      |              |



## System_Cleaner

| Name           | Download/Product Link                        | Add APT Repo | Extra Config |
| -------------- | -------------------------------------------- | ------------ | ------------ |
| bleachbit      | https://www.bleachbit.org/download           | No Repo      |              |
| Ubuntu Cleaner | https://github.com/gerardpuig/ubuntu-cleaner | Manual       |              |

### Ubuntu Cleaner

```shell
sudo add-apt-repository ppa:gerardpuig/ppa
sudo apt update && sudo apt install ubuntu-cleaner
```



## System_DiskManager

| Name          | Download/Product Link     | Add APT Repo | Extra Config |
| ------------- | ------------------------- | ------------ | ------------ |
| baobab        | apt install baobab        |              |              |
| gsmartcontrol | apt install gsmartcontrol |              |              |
| gparted       | apt install gparted       |              |              |



## System_Monitor

| Name | Download/Product Link | Add APT Repo | Extra Config |
| ---- | --------------------- | ------------ | ------------ |
| htop | apt install htop      |              |              |
| btop | apt install btop      |              |              |



## System_FileUtilities

| Name          | Download/Product Link           | Add APT Repo | Extra Config |
| ------------- | ------------------------------- | ------------ | ------------ |
| gtkhash       | apt install gtkhash             |              |              |
| BeyondCompare | https://www.scootersoftware.com | Auto         |              |

### BeyondCompare

https://www.scootersoftware.com/kb/linux_install#debian

something maybe helpful

[https://gist.github.com/rise-worlds/5a5917780663aada8028f96b15057a67](https://gist.github.com/rise-worlds/5a5917780663aada8028f96b15057a67)

[https://gist.github.com/satish-setty/04e1058d3043f4d10e2d52feebe135e8](https://gist.github.com/satish-setty/04e1058d3043f4d10e2d52feebe135e8)



## System_Terminal

| Name  | Download/Product Link | Add APT Repo | Extra Config |
| ----- | --------------------- | ------------ | ------------ |
| byobu | apt install byobu     |              |              |



## Development

| Name | Download/Product Link | Add APT Repo | Extra Config |
| ---- | --------------------- | ------------ | ------------ |
|      |                       |              |              |



wget https://code.visualstudio.com/sha/download\?build\=stable\&os\=linux-deb-x64 --trust-server-names



## Other

| Name         | Desc       | Download/Product Link                  | Add APT Repo | Extra Config |
| ------------ | ---------- | -------------------------------------- | ------------ | ------------ |
| Snipaste     | Screenshot | https://www.snipaste.com/download.html | No Repo      |              |
| alacarte     | .desktop   |                                        |              |              |
| dconf-editor |            | apt install dconf-editor               |              |              |
| gnome-tweaks |            | apt install gnome-tweaks               |              |              |
