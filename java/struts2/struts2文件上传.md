# Struts2接收图片
Action 接收 File 对象需要在自定义 Action 类定义三个属性，并且需要 getter 和 setter 方法。

1. File 类型的属性接收所上传的文件；
2. FileName 结尾的 String 类型的属性接收上传文件 的名称；
3. ContentType 结尾的 String 类型的属性接收上传文件的类型。

```java
public class UserAction extends ActionSupport {
    private File upload;//上传文件对象
    private String uploadFileName;//上传文件的名称
    private String uploadContentType;//上传文件的类型

    public File getUpload() {
        return upload;
    }

    public void setUpload(File upload) {
        this.upload = upload;
    }

    public String getUploadFileName() {
        return uploadFileName;
    }

    public void setUploadFileName(String uploadFileName) {
        this.uploadFileName = uploadFileName;
    }

    public String getUploadContentType() {
        return uploadContentType;
    }

    public void setUploadContentType(String uploadContentType) {
        this.uploadContentType = uploadContentType;
    }
}
```