最近接到一个需求，需要在某个时刻触发弹出一个toast，但是不是立即显示，需要延时3秒显示。
但是用户在这3秒里面能够做很多事，比如点击跳转另一个页面后，就不再显示这个toast，于是就需要移除。
思路是采用HandLer来实现延时显示和移除，同时为了能够在其他地方也使用。
需要注意的是，处理移除的handler实例必须和显示的handler是同一个实例，否则是不能移除的。
代码还用到了单例，具体代码如下：
```
public class MainActivity extends AppCompatActivity {

    Button buttonStart, buttonStop ;

    /** toast延迟显示时间 */
    private static final int DELAY_TIME = 2000;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        buttonStart = (Button) findViewById(R.id.button);
        buttonStop = (Button) findViewById(R.id.button2);

        buttonStart.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                MyToastHandler.getInstance(MainActivity.this, "show toast")
                            .sendEmptyMessageDelayed(MyToastHandler.SHOW_TOAST, DELAY_TIME);

            }
        });

        buttonStop.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                // 只有已经初始化了，才发送移除Toast的消息
                if (MyToastHandler.hasInstance()) {
                    MyToastHandler.getInstance(null, null)
                            .sendEmptyMessageDelayed(MyToastHandler.REMOVE_TOAST, 0);
                }
            }
        });

    }

    /**
     * 防止内存泄漏，采用static形式
     */
    private static class MyToastHandler extends Handler {
        /** 使用弱引用避免内存泄漏 */
        private final WeakReference<Context> mContextWeakReference;
        /** 显示toast ID */
        public static final int SHOW_TOAST = 0x00;
        /** 移除toast ID */
        public static final int REMOVE_TOAST = 0x01;
        /** 主feed智能导流 business 数据 */
        private final String text;
        /** 单例 */
        private static volatile MyToastHandler mMyToastHandler;

        public static MyToastHandler getInstance(Context context, String msg) {
            if (null == mMyToastHandler) {
                synchronized (MyToastHandler.class) {
                    if (null == mMyToastHandler) {
                        mMyToastHandler = new MyToastHandler(context, msg);
                    }
                }
            }
            return mMyToastHandler;
        }

        /**
         * 用于判断是否已经初始化
         */
        public static boolean hasInstance() {
            return mMyToastHandler != null;
        }

        /**
         * 构造函数
         *
         * @param context 上下文内容
         * @param text     消息文本
         */
        private MyToastHandler(Context context, String text) {
            super(Looper.getMainLooper());
            mContextWeakReference = new WeakReference<>(context);
            this.text = text;
        }

        @Override
        public void handleMessage(Message msg) {
            if (msg.what == SHOW_TOAST) {
                final Context context = mContextWeakReference.get();
                if (context != null) {
                    Toast.makeText(context, text, Toast.LENGTH_SHORT).show();
                }
            } else if (msg.what == REMOVE_TOAST) {
                removeMessages(SHOW_TOAST);
            }
        }
    }

}
```
布局同<录制播放音频.md>
