---
title: Android P适配静默安装
date: 2018-12-11 14:44:06
tags:
---
https://stackoverflow.com/questions/5803999/install-apps-silently-with-granted-install-packages-permission

https://www.javatips.net/api/MyLibrary-master/lib/MyCommon/src/main/java/cm/android/common/pm/Installer.java

[Android包管理機制（二）PackageInstaller安裝APK](https://hk.saowen.com/a/267b202111d5ac492b2a9562baff56b3507a508ebcce05d41ebac68b8776f712)

Android P上通过pm命令执行静默安装失效。原因是pm命令在P上面已经移除。Google建议以文件流的方式执行安装，大体过程如下：
```
// 适配android9的安装方法。
public void install28(String apkFilePath) {
    File apkFile = new File(apkFilePath);
    PackageInstaller packageInstaller = getPackageManager().getPackageInstaller();
    PackageInstaller.SessionParams sessionParams 
        = new PackageInstaller.SessionParams(PackageInstaller
        .SessionParams.MODE_FULL_INSTALL);
    sessionParams.setSize(apkFile.length());

    int sessionId = createSession(packageInstaller, sessionParams);
    if (sessionId != -1) {
        boolean copySuccess = copyInstallFile(packageInstaller, sessionId, apkFilePath);
        if (copySuccess) {
            execInstallCommand(packageInstaller, sessionId);
        }
    }
}
```

```
private int createSession(PackageInstaller packageInstaller, 
                        PackageInstaller.SessionParams sessionParams) {
    int sessionId = -1;
    try {
        sessionId = packageInstaller.createSession(sessionParams);
    } catch (IOException e) {
        e.printStackTrace();
    }
    return sessionId;
}

private boolean copyInstallFile(PackageInstaller packageInstaller, 
            int sessionId, String apkFilePath) {
    InputStream in = null;
    OutputStream out = null;
    PackageInstaller.Session session = null;
    boolean success = false;
    try {
        File apkFile = new File(apkFilePath);
        session = packageInstaller.openSession(sessionId);
        out = session.openWrite("base.apk", 0, apkFile.length());
        in = new FileInputStream(apkFile);
        int total = 0, c;
        byte[] buffer = new byte[65536];
        while ((c = in.read(buffer)) != -1) {
            total += c;
            out.write(buffer, 0, c);
        }
        session.fsync(out);
        Log.i(TAG, "streamed " + total + " bytes");
        success = true;
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        closeQuietly(out);
        closeQuietly(in);
        closeQuietly(session);
    }
    return success;
}

private void execInstallCommand(PackageInstaller packageInstaller, int sessionId) {
    PackageInstaller.Session session = null;
    try {
        session = packageInstaller.openSession(sessionId);
        Intent intent = new Intent(this, InstallResultReceiver.class);
        PendingIntent pendingIntent = PendingIntent.getBroadcast(this,
                1, intent,
                PendingIntent.FLAG_UPDATE_CURRENT);
        session.commit(pendingIntent.getIntentSender());
    } catch (IOException e) {
        e.printStackTrace();
    } catch (InterruptedException e) {
        e.printStackTrace();
    } finally {
        closeQuietly(session);
    }
}
```

在InstallResultReceiver中接收安装结果
```
public class InstallResultReceiver extends BroadcastReceiver {
    @Override
    public void onReceive(Context context, Intent intent) {
        if (intent!= null) {
            final int status = intent.getIntExtra(PackageInstaller.EXTRA_STATUS,
                    PackageInstaller.STATUS_FAILURE);
            if (status == PackageInstaller.STATUS_SUCCESS) {
                // success
            } else {
                Log.e(TAG, intent.getStringExtra(PackageInstaller.EXTRA_STATUS_MESSAGE));
            }
    }
}
```

链接：https://www.jianshu.com/p/54d6cbc5e689
