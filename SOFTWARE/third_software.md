# third party software

## Chrome

```shell
# install
wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
sudo dpkg -i google-chrome-stable_current_amd64.deb
```

```shell
# config policy
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
  "PrivacySandboxFingerprintingProtectionEnabled": false,
  "PrivacySandboxIpProtectionEnabled": false,
  "PrivacySandboxPromptEnabled": false,
  "PrivacySandboxSiteEnabledAdsEnabled": false,
  "ShoppingListEnabled": false
}
EOF
```

## Typora

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
```

## VScode

```shell
wget https://code.visualstudio.com/sha/download\?build\=stable\&os\=linux-deb-x64 --trust-server-names
```


## Ubuntu-Cleaner

```shell
sudo add-apt-repository ppa:gerardpuig/ppa
sudo apt update
sudo apt install ubuntu-cleaner
```

## Beyond Compare

https://www.scootersoftware.com/kb/linux_install#debian

something maybe helpful

[https://gist.github.com/rise-worlds/5a5917780663aada8028f96b15057a67](https://gist.github.com/rise-worlds/5a5917780663aada8028f96b15057a67)

[https://gist.github.com/satish-setty/04e1058d3043f4d10e2d52feebe135e8](https://gist.github.com/satish-setty/04e1058d3043f4d10e2d52feebe135e8)