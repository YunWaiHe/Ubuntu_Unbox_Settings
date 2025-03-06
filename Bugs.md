- 窗口无边框

  [Bug 2064177](https://bugs.launchpad.net/ubuntu/+source/gtk+3.0/+bug/2064177)

  `crontab -e`添加：

  ```
  @reboot $(sleep 30 && pkill -HUP mutter-x11-fram)
  ```

Wait for fix...  
