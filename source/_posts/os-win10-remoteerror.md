---
title: win10远程错误配置
---

{% img /image/os_win10_remote_error.png "os_win10_remote_error" %}

# gpedit.msc
进入控制台：`cmd`
{% img /image/os_win10_gpedit.png "os_win10_gpedit" %}

# 本地组策略编辑器
计算机配置>管理模板>系统>凭据分配>加密Oracle修正
{% img /image/os_win10_remote_error2.png "os_win10_remote_error2" %}

# 策略设置
已启用、易受攻击
{% img /image/os_win10_remote_error3.png "os_win10_remote_error3" %}