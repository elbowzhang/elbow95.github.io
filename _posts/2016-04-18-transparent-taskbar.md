---
layout: post
title: Make your win10 taskbar more transparency
date: '2016-4-19'
categories: [windows]
---

The new OS of Windows - win10, has the setting to transparent the taskbar which can't help you to make it complete transparency.But you can use the following steps to achieve the order without any third party software.

## Make sure you have open the option of the setting

You have to open the transparent option in the `Setting->Personalization->Color` if you want to continue the following steps.

<div class="image-wrapper" style="text-align: center">
  <img src="http://odyloipwl.bkt.clouddn.com/post/2016-04-18/setting.jpg">
</div>

## Open the Regedit Editor

Press `win + R` keys to open the Run window and then input `regedit`, finally click `OK` to open the Regedit Editor.

<div class="image-wrapper" style="text-align: center">
  <img src="http://odyloipwl.bkt.clouddn.com/post/2016-04-18/run.jpg">
</div>

## Add a new value in Regedit

Expand the `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced` in the Regedit Editor.<br>
Then add a new DWORD(32-bit) value named `UseOLEDTaskbarTransparency` on the right side.

<div class="image-wrapper" style="text-align: center">
  <img src="http://odyloipwl.bkt.clouddn.com/post/2016-04-18/regedit.jpg" width="650px">
</div>

Double-click the value you have just added, then change the Value data to `1`, click OK to save.

<div class="image-wrapper" style="text-align: center">
  <img src="http://odyloipwl.bkt.clouddn.com/post/2016-04-18/value.jpg">
</div>

## Make changes to take effect

After you have completed the steps above, log off and log in the system again ,or restart the Explorer. Then you can enjoy the complete transparency taskbar.

<div class="image-wrapper" style="text-align: center">
  <img src="http://odyloipwl.bkt.clouddn.com/post/2016-04-18/taskbar.jpg">
</div>
