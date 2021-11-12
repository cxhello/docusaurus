```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import javax.servlet.http.HttpServletResponse;
import java.io.BufferedInputStream;
import java.io.BufferedOutputStream;
import java.io.IOException;
import java.net.HttpURLConnection;
import java.net.URL;
import java.net.URLEncoder;

/**
 * @author cxhello
 * @date 2021/4/14
 */
public class DownloadUtil {

    private static final Logger logger = LoggerFactory.getLogger(DownloadUtil.class);

    /**
     * 通过url下载文件
     * @param urlStr
     * @param fileName
     * @param response
     */
    public static void download(String urlStr, String fileName, HttpServletResponse response) {
        String suffix = urlStr.substring(urlStr.lastIndexOf("."));
        BufferedInputStream bis = null;
        BufferedOutputStream bos = null;
        try {
            fileName = fileName + suffix;
            response.setHeader("Content-Disposition", "attachment;filename="
                    .concat(String.valueOf(URLEncoder.encode(fileName, "UTF-8"))));
            URL url = new URL(urlStr);
            HttpURLConnection conn = (HttpURLConnection) url.openConnection();
            bis = new BufferedInputStream(conn.getInputStream());
            bos = new BufferedOutputStream(response.getOutputStream());
            byte[] bytes = new byte[1024];
            int bytesRead;
            while ((bytesRead = bis.read(bytes)) != -1) {
                bos.write(bytes, 0, bytesRead);
            }
            response.flushBuffer();
        } catch (IOException e) {
            logger.error("System error", e);
        } finally {
            try {
                bis.close();
                bos.close();
            } catch (IOException e) {
                logger.error("System error", e);
            }
        }
    }

}
```

