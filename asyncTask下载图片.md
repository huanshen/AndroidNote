要访问互联网，请务必在以下位置指定以下权限AndroidManifest.xml：

```
   <uses-permission android:name="android.permission.INTERNET" />
   <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
```
发送HTTP请求（“硬”的方式）
发送HTTP请求涉及以下概念步骤：

声明一个URL连接
打开InputStream来连接
根据数据类型下载和解码
包装在AsyncTask中并在后台线程中执行
这将转化为下面的网络代码来发送一个简单的请求

```
package com.example.shenjiaqi.myapplication;

import android.content.Context;
import android.graphics.Bitmap;
import android.graphics.BitmapFactory;
import android.net.ConnectivityManager;
import android.net.NetworkInfo;
import android.os.AsyncTask;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.util.Log;
import android.widget.ImageView;
import android.widget.Toast;

import java.io.IOException;
import java.io.InputStream;
import java.net.HttpURLConnection;
import java.net.URL;

public class MainActivity extends AppCompatActivity {

    private ImageView ivBasicImage;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        ivBasicImage = (ImageView) findViewById(R.id.ivBasicImage);
        String url = "http://img5.imgtn.bdimg.com/it/u=49366202,632101467&fm=27&gp=0.jpg";
        // Download image from URL and display within ImageView

        new ImageDownloadTask(ivBasicImage).execute(url);
        if (isNetworkAvailable()){
            Toast.makeText(MainActivity.this, "有网", Toast.LENGTH_SHORT).show();
        }else{
            Toast.makeText(MainActivity.this, "无网", Toast.LENGTH_SHORT).show();
        }
    }

    // Defines the background task to download and then load the image within the ImageView
    private class ImageDownloadTask extends AsyncTask<String, Void, Bitmap> {
        ImageView imageView;

        public ImageDownloadTask(ImageView imageView) {
            this.imageView = imageView;
        }

        protected Bitmap doInBackground(String... addresses) {
            Bitmap bitmap = null;
            InputStream in = null;
            try {
                // 1. Declare a URL Connection
                URL url = new URL(addresses[0]);
                HttpURLConnection conn = (HttpURLConnection) url.openConnection();
                // 2. Open InputStream to connection
                conn.connect();
                in = conn.getInputStream();
                // 3. Download and decode the bitmap using BitmapFactory
                bitmap = BitmapFactory.decodeStream(in);
            } catch (IOException e) {
                e.printStackTrace();
            } finally {
                if (in != null)
                    try {
                        in.close();
                    } catch (IOException e) {
                        Log.e("pic", "Exception while closing inputstream" + e);
                    }
            }
            return bitmap;
        }

        // Fires after the task is completed, displaying the bitmap into the ImageView
        @Override
        protected void onPostExecute(Bitmap result) {
            // Set bitmap image for the result
            imageView.setImageBitmap(result);
        }

    }

    private Boolean isNetworkAvailable() {
        ConnectivityManager connectivityManager
                = (ConnectivityManager) getSystemService(Context.CONNECTIVITY_SERVICE);
        NetworkInfo activeNetworkInfo = connectivityManager.getActiveNetworkInfo();
        return activeNetworkInfo != null && activeNetworkInfo.isConnectedOrConnecting();
    }


}

```
上面我们还通过 ConnectivityManager 来获取网络的连接情况。
下面是布局文件：
```
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <ImageView
        android:id="@+id/ivBasicImage"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" />

</LinearLayout>
```
