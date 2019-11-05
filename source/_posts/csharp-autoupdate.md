---
title: C#自动更新程序
---

# 下载更新配置文件

```csharp
xdoc = XDocument.Load("https://github.com/../releases/download/v10/version.xml");//版本配置文件地址
```

# 更新配置文件模板

```bash
<update minimum='1.0.2.4' latest='1.0.2.4'>
<release version='1.0.2.4' file='https://github.com/../releases/download/v2.1/Release.zip'>
<changes>***</changes>
</release>
</update>
```

# 获取当前程序版本
## Winform

```csharp
Version version = Assembly.GetExecutingAssembly().GetName().Version;
```

## Netcore

```csharp
Version version = System.Reflection.Assembly.GetExecutingAssembly().GetName().Version;
```

# 获取配置文件内容

```csharp
Version minimum = Version.Parse(xdoc.Element("update").Attribute("minimum").Value);
```

# 下载更新文件

```csharp
WebClient web = new WebClient();
```

## winform

```csharp
web.DownloadFileCompleted += Web_DownloadFileCompleted;
web.DownloadFileAsync(new Uri("远程地址"), "目标地址");
```

## Netcore

```csharp
web.DownloadFileCompleted += Web_DownloadFileCompleted;
web.DownloadFile(new Uri("远程地址"), "目标地址");
```

# 解压

```csharp
ZipFile.ExtractToDirectory(download_path, di.Name);
```

# 结束当前进程
## Winform
### 重写更新文件

```csharp
File.WriteAllBytes("update.bat", Resources.UpdateBat);
```

### 关闭进程

```csharp
Close();
if (Program.MainForm != null) Program.MainForm.Close();
```

## Netcore

```csharp
Process.GetCurrentProcess().Close();
```

# 更新

```csharp
Process.Start("update.bat");
```
# 更新程序模板

```bash
@echo off
set "taskname=bhp-gui.exe"
echo waiting...
:wait
ping 127.0.1 -n 3 >nul
tasklist | find "%taskname%" /i >nul 2>nul
if "%errorlevel%" NEQ "1" goto wait
echo updating...
copy /Y update\* *
rmdir /S /Q update
del /F /Q update.zip
start %taskname%
del /F /Q update.bat
```

# 源码
## Winform

```csharp
public static void Main()
        {           
            try
            {
                xdoc = XDocument.Load("https://github.com/../releases/download/v10/version.xml");//版本配置文件地址
            }
            catch { }
            if (xdoc != null)
            {
                Version version = Assembly.GetExecutingAssembly().GetName().Version;
                Version minimum = Version.Parse(xdoc.Element("update").Attribute("minimum").Value);
                if (version < minimum)
                {
                    using (UpdateDialog dialog = new UpdateDialog(xdoc))
                    {
                        dialog.ShowDialog();
                    }
                    return;
                }
            }             
            
            Application.Run(MainForm = new MainForm(xdoc));
        }
 
private readonly WebClient web = new WebClient();
private readonly string download_url;
private string download_path;
 
public UpdateDialog(XDocument xdoc)
        {
            InitializeComponent();
            Version latest = Version.Parse(xdoc.Element("update").Attribute("latest").Value);
            XElement release = xdoc.Element("update").Elements("release").First(p => p.Attribute("version").Value == latest.ToString());
            download_url = release.Attribute("file").Value;
            web.DownloadFileCompleted += Web_DownloadFileCompleted;
        }
 
private void Web_DownloadFileCompleted(object sender, AsyncCompletedEventArgs e)
        {
            if (e.Cancelled || e.Error != null) return;
            DirectoryInfo di = new DirectoryInfo("update");
            if (di.Exists) di.Delete(true);
            di.Create();
            ZipFile.ExtractToDirectory(download_path, di.Name);
            FileSystemInfo[] fs = di.GetFileSystemInfos();
            if (fs.Length == 1 && fs[0] is DirectoryInfo)
            {
                ((DirectoryInfo)fs[0]).MoveTo("update2");
                di.Delete();
                Directory.Move("update2", di.Name);
            }
            File.WriteAllBytes("update.bat", Resources.UpdateBat);
            Close();
            if (Program.MainForm != null) Program.MainForm.Close();
            Process.Start("update.bat");
        }
 
private void button2_Click(object sender, EventArgs e)
        {           
            download_path = "update.zip";
            web.DownloadFileAsync(new Uri(download_url), download_path);
        }
```

## Netcore

```csharp
static void Main(string[] args)
        {
            if (UpdateVersion()) return;         
        }
 
private static bool UpdateVersion()
        {
            XDocument xdoc = null;
            WebClient web = new WebClient();
            string download_url;
 
            try
            {
                xdoc = XDocument.Load("https://github.com/../releases/download/v10/version.xml");
            }
            catch { }
            if (xdoc != null)
            {
                Version version = System.Reflection.Assembly.GetExecutingAssembly().GetName().Version;
                Version minimum = Version.Parse(xdoc.Element("update").Attribute("minimum").Value);
                if (version < minimum)
                {
                    Version latest = Version.Parse(xdoc.Element("update").Attribute("latest").Value);
                    XElement release = xdoc.Element("update").Elements("release").First(p => p.Attribute("version").Value == latest.ToString());
                    download_url = release.Attribute("file").Value;
                    download_path = "Release.zip";
                    web.DownloadFile(new Uri(download_url), download_path);
                    UpdateFiles();
 
                    return true;
                }
            }
            return false;
        }
 
private static void UpdateFiles()
        {
            DirectoryInfo di = new DirectoryInfo("update");
            if (di.Exists) di.Delete(true);
            di.Create();
            ZipFile.ExtractToDirectory(download_path, di.Name);
            FileSystemInfo[] fs = di.GetFileSystemInfos();
            if (fs.Length == 1 && fs[0] is DirectoryInfo)
            {
                ((DirectoryInfo)fs[0]).MoveTo("update2");
                di.Delete();
                Directory.Move("update2", di.Name);
            }
            Process.GetCurrentProcess().Close();
            Process.Start("update.bat");
        }
```