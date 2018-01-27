本指南描述了转换从网络请求获取的JSON数据并将其转换为简单模型对象的过程。
这里我们采用了okHttp3来请求数据：
添加引用：
```
compile 'com.squareup.okhttp3:okhttp:3.4.1'
```
创建数据模型：
```
package com.example.shenjiaqi.myapplication;

import org.json.JSONObject;


public class Book {
    public int count;
    public String title;

    public static Book toModel(JSONObject jsonObject) {
        Book book = new Book();
        book.count = jsonObject.optInt("count");
        book.title = jsonObject.optString("title");
        return book;
    }

    @Override
    public String toString() {
        return "count: " + count+" title: "+title;
    }
}

```

然后我们开始请求数据，并转化为 String 打印出来
```
public class MainActivity extends AppCompatActivity {

    private ImageView ivBasicImage;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        ivBasicImage = (ImageView) findViewById(R.id.ivBasicImage);

        String url = "http://api.douban.com/v2/movie/top250?start=25&count=1";
        requestBlog(url);
    }

    public void requestBlog(String url) {

        OkHttpClient mHttpClient = new OkHttpClient();

        Request request = new Request.Builder().url(url).build();

        mHttpClient.newCall(request).enqueue(new Callback() {
            @Override
            public void onFailure(Call call, IOException e) {

            }

            @Override
            public void onResponse(Call call, Response response) throws IOException {
                String json = response.body().string();
                try {
                    JSONObject jsonObject = new JSONObject(json);
                    Book book = Book.toModel(jsonObject);
                    Log.d("shen", book.toString());

                } catch (JSONException e) {
                    e.printStackTrace();
                }
            }
        });


    }

}
```
