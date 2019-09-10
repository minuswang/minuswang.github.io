---
title: Android文件MD5/SHA1/SHA256校验
date: 2019-09-10 15:29:56
tags:
---

一般进行文件下载需求时，都会附带有文件完整性和正确性校验，避免下载的文件不完整或者被第三方劫持注入，貌似很多都是采用MD5校验，但也有采用sha1或者sha256校验，最近做的两个项目中刚好先后遇到了md5和sha256校验，跟了一下MessageDigest源码，提取出工具类供以后使用，同时在此贡献出来，如有不正确的地方欢迎指正。

    1、采用枚举定义需要校验的类型，屏蔽直接传入不支持的校验方式，增加容错率：

```java
public enum TypeEnum {
        MD5, SHA1, SHA256
    }
```
    2、获取指定文件的md5/sha1/sha256

```java
private static String getFileSignature(File file, TypeEnum typeEnum) {
        MessageDigest digest;
        String type = "";
        switch (typeEnum) {
            case MD5:
                type = "MD5";
                break;
            case SHA1:
                type = "SHA-1";
                break;
            case SHA256:
                type = "SHA-256";
                break;
        }
        if (TextUtils.isEmpty(type)) {
            LogUtil.e(Constants.LOG_TAG, "type undefined");
            return null;
        }
        try {
            digest = MessageDigest.getInstance(type);
        } catch (NoSuchAlgorithmException e) {
            e.printStackTrace();
            return null;
        }
 
        InputStream is;
        try {
            is = new FileInputStream(file);
        } catch (FileNotFoundException e) {
            e.printStackTrace();
            LogUtil.e(TAG, "Exception while getting FileInputStream");
            return null;
        }
 
        byte[] buffer = new byte[8192];
        int read;
        try {
            while ((read = is.read(buffer)) > 0) {
                digest.update(buffer, 0, read);
            }
            byte[] md5sum = digest.digest();
            BigInteger bigInt = new BigInteger(1, md5sum);
            String output = bigInt.toString(16);
            output = String.format("%32s", output).replace(' ', '0');
            return output;
        } catch (IOException e) {
            e.printStackTrace();
            LogUtil.e(TAG,"Unable to process file for ");
            return null;
        } finally {
            try {
                is.close();
            } catch (IOException e) {
                e.printStackTrace();
                LogUtil.e(TAG, "Exception on closing inputstream:" );
            }
        }
    }
```
3、与服务器给的文件md5/sha1/sha256对比

```java
public static boolean validateFile(TypeEnum typeEnum, String standardStr, File fileToCheck) {
        if (TextUtils.isEmpty(standardStr) || fileToCheck == null) {
            LogUtil.e(TAG, "MD5 string empty or updateFile null");
            return false;
        }
 
        String calculatedDigest = getFileSignature(fileToCheck, typeEnum);
 
        if (TextUtils.isEmpty(calculatedDigest)) {
            LogUtil.d(TAG, "calculatedDigest null");
            return false;
        }
        return calculatedDigest.equalsIgnoreCase(standardStr);
    }
```

全部代码如下：
```java
public class FileValidateUtil {
 
    private static final String TAG = Constants.LOG_TAG;
 
    public enum TypeEnum {
        MD5, SHA1, SHA256
    }
 
    /**
     * @param typeEnum
     * @param standardStr
     * @param fileToCheck
     * @return
     */
    public static boolean validateFile(TypeEnum typeEnum, String standardStr, File fileToCheck) {
        if (TextUtils.isEmpty(standardStr) || fileToCheck == null) {
            LogUtil.e(TAG, "MD5 string empty or updateFile null");
            return false;
        }
 
        String calculatedDigest = getFileSignature(fileToCheck, typeEnum);
 
        if (TextUtils.isEmpty(calculatedDigest)) {
            LogUtil.d(TAG, "calculatedDigest null");
            return false;
        }
        return calculatedDigest.equalsIgnoreCase(standardStr);
    }
 
    private static String getFileSignature(File file, TypeEnum typeEnum) {
        MessageDigest digest;
        String type = "";
        switch (typeEnum) {
            case MD5:
                type = "MD5";
                break;
            case SHA1:
                type = "SHA-1";
                break;
            case SHA256:
                type = "SHA-256";
                break;
        }
        if (TextUtils.isEmpty(type)) {
            LogUtil.e(Constants.LOG_TAG, "type undefined");
            return null;
        }
        try {
            digest = MessageDigest.getInstance(type);
        } catch (NoSuchAlgorithmException e) {
            e.printStackTrace();
            return null;
        }
 
        InputStream is;
        try {
            is = new FileInputStream(file);
        } catch (FileNotFoundException e) {
            e.printStackTrace();
            LogUtil.e(TAG, "Exception while getting FileInputStream");
            return null;
        }
 
        byte[] buffer = new byte[8192];
        int read;
        try {
            while ((read = is.read(buffer)) > 0) {
                digest.update(buffer, 0, read);
            }
            byte[] md5sum = digest.digest();
            BigInteger bigInt = new BigInteger(1, md5sum);
            String output = bigInt.toString(16);
            output = String.format("%32s", output).replace(' ', '0');
            return output;
        } catch (IOException e) {
            e.printStackTrace();
            LogUtil.e(TAG,"Unable to process file for ");
            return null;
        } finally {
            try {
                is.close();
            } catch (IOException e) {
                e.printStackTrace();
                LogUtil.e(TAG, "Exception on closing inputstream:" );
            }
        }
    }
 
}

```
————————————————
版权声明：本文为CSDN博主「校长_494344756」的原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/qq_35168368/article/details/80064472