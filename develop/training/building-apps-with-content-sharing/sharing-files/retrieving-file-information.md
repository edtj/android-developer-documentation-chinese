#获取文件信息

在一个客户端应用试图获取一个文件的内容 URI 之前，应该先从服务器请求获取这个文件的信息，包括文件的数据类型和文件大小。数据类型可以帮助客户端应用确认是否可以操作此文件，文件大小可以有助于应用建立建立缓冲和缓存。

<small>本课将展示如何通过查询服务端应用程序的 [FileProvider](https://developer.android.google.cn/reference/android/support/v4/content/FileProvider.html) 来获取文件的 MIME (*MIME：全称 Multipurpose Internet Mail Extensions，多功能 Internet 邮件扩充服务。它是一种多用途网际邮件扩充协议*) 类型和文件大小。</small>

##获取文件的 MIME 类型

<small>客户端应用要根据一个文件的数据类型去判断如何处理文件的内容。客户端应用可以通过 [ContentResolver.getType()](https://developer.android.google.cn/reference/android/content/ContentResolver.html#getType(android.net.Uri)) 去获取一个包含 URI 的分享文件的数据类型。该方法返回文件的 MIME 类型。默认情况下，一个 [FileProvider](https://developer.android.google.cn/reference/android/support/v4/content/FileProvider.html) 通过文件的后缀名来确定其 MIME 类型。</small>

<small>接下来的代码片段介绍了服务端把 Content URI 反馈给客户端应用后，客户端如何获取文件的 MIME 类型：</small>
   
```java
     ...
    /*
     * Get the file's content URI from the incoming Intent, then
     * get the file's MIME type
     */
    Uri returnUri = returnIntent.getData();
    String mimeType = getContentResolver().getType(returnUri);
    ...
```
##获取文件名和文件大小

<small>[FileProvider](https://developer.android.google.cn/reference/android/support/v4/content/FileProvider.html) 类有一个 [query()](https://developer.android.google.cn/reference/android/support/v4/content/FileProvider.html#query(android.net.Uri, java.lang.String[], java.lang.String, java.lang.String[], java.lang.String)) 方法的默认实现，它返回一个 [Cursor](https://developer.android.google.cn/reference/android/database/Cursor.html) 对象，该 [Cursor](https://developer.android.google.cn/reference/android/database/Cursor.html) 对象包含了 Content URI 所关联的文件的名称和大小。默认的实现返回下面两列信息：</small>


<small>[DISPLAY_NAME](https://developer.android.google.cn/reference/android/provider/OpenableColumns.html#DISPLAY_NAME)</small>

<small>文件名，[String](https://developer.android.google.cn/reference/java/lang/String.html) 类型。这个值和 [File.getName()](https://developer.android.google.cn/reference/java/io/File.html#getName()) 所返回的值一样。</small>

<small>[SIZE](https://developer.android.google.cn/reference/android/provider/OpenableColumns.html#SIZE)</small>

<small>文件大小，以字节为单位，`long` 类型。这个值和 [File.length()](https://developer.android.google.cn/reference/java/io/File.html#length()) 所返回的值一样。</small>

<small>客户端应用可以通过将 [query()](https://developer.android.com/reference/android/support/v4/content/FileProvider.html#query(android.net.Uri, java.lang.String[], java.lang.String, java.lang.String[], java.lang.String)) 的除了 Content URI 之外的其他参数都设置为 `null`，来同时获取文件的 [DISPLAY_NAME](https://developer.android.com/reference/android/provider/OpenableColumns.html#DISPLAY_NAME) 和 [SIZE](https://developer.android.com/reference/android/provider/OpenableColumns.html#SIZE)。例如，下面的代码片段获取一个文件的 [DISPLAY_NAME](https://developer.android.com/reference/android/provider/OpenableColumns.html#DISPLAY_NAME) 和 [SIZE](https://developer.android.com/reference/android/provider/OpenableColumns.html#SIZE)，然后在两个 [TextView](https://developer.android.google.cn/reference/android/widget/TextView.html) 中将他们显示出来：</small>

```java
/*
     * Get the file's content URI from the incoming Intent,
     * then query the server app to get the file's display name
     * and size.
     */
    Uri returnUri = returnIntent.getData();
    Cursor returnCursor =
            getContentResolver().query(returnUri, null, null, null, null);
    /*
     * Get the column indexes of the data in the Cursor,
     * move to the first row in the Cursor, get the data,
     * and display it.
     */
    int nameIndex = returnCursor.getColumnIndex(OpenableColumns.DISPLAY_NAME);
    int sizeIndex = returnCursor.getColumnIndex(OpenableColumns.SIZE);
    returnCursor.moveToFirst();
    TextView nameView = (TextView) findViewById(R.id.filename_text);
    TextView sizeView = (TextView) findViewById(R.id.filesize_text);
    nameView.setText(returnCursor.getString(nameIndex));
    sizeView.setText(Long.toString(returnCursor.getLong(sizeIndex)));
    ...

```

>翻译：[@misparking](https://github.com/misparking)   
>校对：[@ifeegoo](https://github.com/ifeegoo)  
>原始文档：<https://developer.android.google.cn/training/secure-file-sharing/retrieve-info.html>  