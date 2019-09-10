---
title: Android计算文件的MD5和SHA1
date: 2019-09-10 15:36:04
tags:
---

项目需要，计算文件的MD5和SHA1值，找了一些代码效率比较低，有的还晦涩难懂，这里给出测试后通过，速度也相对较快的代码。

```java
        /**
	 * Get the md5 value of the filepath specified file
	 * @param filePath The filepath of the file
	 * @return The md5 value
	 */
	public String fileToMD5(String filePath) {
	    InputStream inputStream = null;
	    try {
	        inputStream = new FileInputStream(filePath); // Create an FileInputStream instance according to the filepath
	        byte[] buffer = new byte[1024]; // The buffer to read the file
	        MessageDigest digest = MessageDigest.getInstance("MD5"); // Get a MD5 instance
	        int numRead = 0; // Record how many bytes have been read
	        while (numRead != -1) {
	            numRead = inputStream.read(buffer);
	            if (numRead > 0)
	                digest.update(buffer, 0, numRead); // Update the digest
	        }
	        byte [] md5Bytes = digest.digest(); // Complete the hash computing
	        return convertHashToString(md5Bytes); // Call the function to convert to hex digits
	    } catch (Exception e) {
	        return null;
	    } finally {
	        if (inputStream != null) {
	            try {
	                inputStream.close(); // Close the InputStream
	            } catch (Exception e) { }
	        }
	    }
	}
	
	/**
	 * Get the sha1 value of the filepath specified file
	 * @param filePath The filepath of the file
	 * @return The sha1 value
	 */
	public String fileToSHA1(String filePath) {
	    InputStream inputStream = null;
	    try {
	        inputStream = new FileInputStream(filePath); // Create an FileInputStream instance according to the filepath
	        byte[] buffer = new byte[1024]; // The buffer to read the file
	        MessageDigest digest = MessageDigest.getInstance("SHA-1"); // Get a SHA-1 instance
	        int numRead = 0; // Record how many bytes have been read
	        while (numRead != -1) {
	            numRead = inputStream.read(buffer);
	            if (numRead > 0)
	                digest.update(buffer, 0, numRead); // Update the digest
	        }
	        byte [] sha1Bytes = digest.digest(); // Complete the hash computing
	        return convertHashToString(sha1Bytes); // Call the function to convert to hex digits
	    } catch (Exception e) {
	        return null;
	    } finally {
	        if (inputStream != null) {
	            try {
	                inputStream.close(); // Close the InputStream
	            } catch (Exception e) { }
	        }
	    }
	}

	/**
	 * Convert the hash bytes to hex digits string
	 * @param hashBytes
	 * @return The converted hex digits string
	 */
	private static String convertHashToString(byte[] hashBytes) {
		String returnVal = "";
		for (int i = 0; i < hashBytes.length; i++) {
			returnVal += Integer.toString(( hashBytes[i] & 0xff) + 0x100, 16).substring(1);
		}
		return returnVal.toLowerCase();
	}

```    
从StackOverflow上找到的，为了提高速度，可以将buffer开的大一点，还有的使用JNI编写的，可以参考。


android apk安全之完整性校验
最近项目中在做安全监测,介于这个原因调查了一些第三方的加固防编译平台和自己使用中的心得,总结了apk安全的完整性校验的监测.

完整性校验原理
完整性校验就是我们用各种算法来计算一个文件的完整性，防止这个文件被修改。其中常用的方法就是计算一个文件的CRC32的值或者计算一个文件的哈希值。我们在防止apk被反编译的方法中也可以采用这种方法。我们知道apk生成的classes.dex主要由Java文件生成的，它是整个apk的逻辑实现。所以我们可以对classes.dex文件进行完整性校验，来保证整个程序的逻辑不被修改。如果我们想要保证整个apk文件的完整性，也可以对整个apk文件进行完整性校验。下面我们分别来实现对classes.dex文件和apk文件的完整性校验。

1.用crc32对classes.dex文件的完整性进行校验
代码如下：
```java
//验证apk中classes.dex文件的crc32的值,即对dex文件进行完整性校验
    public static boolean checkDexCrcValue() {
        String apkPath = BaseApplication.getInstance().getPackageCodePath();
        Long dexCrc = Long.parseLong(QianJinSuoApplication.getInstance().getString(R.string.classesdex_crc));
        try {
            ZipFile zipfile = new ZipFile(apkPath);
            ZipEntry dexentry = zipfile.getEntry("classes.dex");
            Log.i("checkDexCrcValue", "classes.dexcrc=" + dexentry.getCrc());
            if (dexentry.getCrc() == dexCrc) {
                return true;
            }

        } catch (IOException e) {
            e.printStackTrace();
        }

        return false;
    }
```

tips:一旦确定了classesdex_crc,代码逻辑不可以再进行修改了。否则需要改相应的classesdex_crc.
用哈希值对整个apk完整性进行校验
由于我们要对整个apk的完整性进行校验，所以我们的算出哈希值就不能存在资源文件中了因为apk中任何的改动都会引起最终apk生成的哈希值的不同。

（1）首先实现apk中计算自身哈希值的代码，如下：

```java
    public static boolean checkApkSha(){

        String apkPath = QianJinSuoApplication.getInstance().getPackageCodePath();

        MessageDigest msgDigest = null;

        try {

            msgDigest = MessageDigest.getInstance("SHA-1");

            byte[] bytes = new byte[1024];

            int byteCount;

            FileInputStream fis = new FileInputStream(new File(apkPath));

            while ((byteCount = fis.read(bytes)) > 0)

            {

                msgDigest.update(bytes, 0, byteCount);

            }

            BigInteger bi = new BigInteger(1, msgDigest.digest());

            String sha = bi.toString(16);
            Log.i("checkApkSha", "apk sha=" + sha);

            fis.close();

            if(BaseApplication.getInstance().getString(R.string.apk_sha).equals(sha)){
                return true;
            }

            //这里添加从服务器中获取哈希值然后进行对比校验

        } catch (Exception e) {

            e.printStackTrace();
        }
        return false;
    }
```

(2) 用Linux下的sha1sum命令计算我们的apk的哈希值，命令如下：
shasum verification.apk

将(2)中生成的哈希值存到服务器上，然后在我们的代码中从服务器获取进行完整性比较。

上面我们用计算crc32和哈希值的方法分别对classes.dex文件和整个apk完整性进行了校验，当然两个校验方法也可以互换使用