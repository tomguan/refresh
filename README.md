


## SwipeRefresh 支持单个View 的下拉刷新以及上拉加载
## SwipeNest 垂直布局一个或多个NestScrollChilder 实现下拉刷新(不支持下拉加载） 
## MuilpAdapter 快速实现 ListView 多种类型展示 

### SwipeRefresh UI

|刷新（可自定义）|下拉加载获取新数据|上拉加载数据全部获得|
|---|---|----
|![github](https://github.com/powyin/nest-scroll/blob/master/app/src/main/res/raw/refresh_pre.gif)|![github](https://github.com/powyin/nest-scroll/blob/master/app/src/main/res/raw/refresh_load_2.gif)|![github](https://github.com/powyin/nest-scroll/blob/master/app/src/main/res/raw/refresh_load_1.gif)|


### SwipeNest UI

|刷新（可自定义）|平滑多个NestScrollChilder之间的滚动|自定义刷新|
|---|---|----
|![github](https://github.com/powyin/nest-scroll/blob/master/app/src/main/res/raw/nest_pre.gif)|![github](https://github.com/powyin/nest-scroll/blob/master/app/src/main/res/raw/nest_pre_1.gif)|![github](https://github.com/powyin/nest-scroll/blob/master/app/src/main/res/raw/nest_pre_2.gif)|


### how to use  SwipeRefresh

      <com.powyin.scroll.widget.SwipeRefresh
        android:id = "@+id/re"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:fresh_model="BOTH" >
        <ListView
            android:id="@+id/my_list"
            android:background="#ffffffff"
            android:layout_width="match_parent"
            android:layout_height="match_parent"/>
        <!--或者是RecyclerView-->
        <!--或者 Any View-->
        <!--<android.support.v7.widget.RecyclerView-->
            <!--android:background="#ffffff"-->
            <!--android:id="@+id/my_recycle"-->
            <!--android:overScrollMode="never"-->
            <!--android:layout_width="match_parent"-->
            <!--android:layout_height="match_parent"/>-->
    </com.powyin.scroll.widget.SwipeRefresh>
    
### how to use  SwipeNest 

      <com.powyin.scroll.widget.SwipeNest
        android:id="@+id/nest_combine"
        android:background="#e5e5e5"
        android:layout_width="match_parent"
        android:layout_height="match_parent" >

        <android.support.v4.widget.NestedScrollView
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:overScrollMode="never">
            <FrameLayout
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:background="#b7b7b7"
                android:clickable="true">
                <ImageView
                    android:layout_width="match_parent"
                    android:layout_height="300dp"
                    android:src="@drawable/pic_4"
                    android:scaleType="centerCrop"  />
            </FrameLayout>
        </android.support.v4.widget.NestedScrollView>

        <android.support.v7.widget.RecyclerView
            android:background="#ffffff"
            android:id="@+id/my_recycle"
            android:overScrollMode="never"
            android:layout_width="match_parent"
            android:layout_height="match_parent"/>
      </com.powyin.scroll.widget.SwipeNest>
    
    
**自定义刷新UI**   

```
调用swipeRefresh.setSwipeControl(SwipeControl control) 或者 SwipeNest.setSwipeControl(SwipeControl control);

继承范例
public class SwipeControlStyle_Horizontal implements SwipeControl {
    private Context mContent;

    private View headView;
    private View overHead;
    private ProgressBar statusPre;

    private ImageView statusComplete;
    private TextView textInfo;

    private View footView;
    private TextView loadProgressBar;
    private TextView textLoad;

    public SwipeControlStyle_Horizontal(Context context) {
        this.mContent = context;
        LayoutInflater inflater = LayoutInflater.from(mContent);
        this.headView = inflater.inflate(R.layout.swipe_control_head, null);
        overHead = headView.findViewById(R.id.swipe_over_head);
        statusPre = (ProgressBar) headView.findViewById(R.id.swipe_image_info);

        statusComplete = (ImageView) headView.findViewById(R.id.swipe_ok);
        textInfo = (TextView) headView.findViewById(R.id.swipe_text_info);

        this.footView = inflater.inflate(R.layout.swipe_control_foot,null);
        loadProgressBar = (TextView) footView.findViewById(R.id.text_load_more_progress);
        textLoad = (TextView)footView.findViewById(R.id.text_load_more_over);
    }

    // 刷新头部
    @Override
    public View getSwipeHead() {
        return headView;
    }

    // SwipeRefresh(必须 getSwipeFoot() != null )  SwipeNest(可以 getSwipeFoot() == null 其不实现上拉加载)
    // 刷新尾部
    @Override
    public View getSwipeFoot() {
        return footView;
    }
    
    // 头部过度拉伸距离
    @Override
    public int getOverScrollHei() {
        return overHead.getHeight();
    }
    
    // 根据 SwipeStatus 状态值 做相应的 UI 调整即可
    // status 当前刷新的状态
    // visibleHei 下拉刷新UI 的可见高度；
    // wholeHei 下拉刷新UI 的总高度
    @Override
    public void onSwipeStatue(SwipeStatus status, int visibleHei, int wholeHei) {
        switch (status) {
            case SWIPE_HEAD_OVER:             // 提示 过度拉伸
                statusPre.setVisibility(View.VISIBLE);
                statusComplete.setVisibility(View.INVISIBLE);
                if(!textInfo.getText().toString().equals("松开刷新")){
                    textInfo.setText("松开刷新");
                }

                break;
            case SWIPE_HEAD_TOAST:           // 提示 下拉刷新
                statusPre.setVisibility(View.VISIBLE);
                statusComplete.setVisibility(View.INVISIBLE);

                if(!textInfo.getText().toString().equals("上拉刷新")){
                    textInfo.setText("上拉刷新");
                }
                break;
            case SWIPE_HEAD_LOADING:         // 提示 刷新中
                statusPre.setVisibility(View.VISIBLE);
                statusComplete.setVisibility(View.INVISIBLE);
                if(!textInfo.getText().toString().equals("正在拼命刷新中")){
                    textInfo.setText("正在拼命刷新中");
                }
                break;
            case SWIPE_HEAD_COMPLETE:        // 提示刷新完成
                statusPre.setVisibility(View.INVISIBLE);

                statusComplete.setVisibility(View.VISIBLE);
                if(!textInfo.getText().toString().equals("刷新成功")){
                    textInfo.setText("刷新成功");
                }
                break;

            case SWIPE_FOOT_LOADING:          // 上拉加载 进行中               (如果是给SwipeNest使用  忽略)
                loadProgressBar.setVisibility(View.VISIBLE);
                textLoad.setVisibility(View.GONE);
                break;
            case SWIPE_FOOT_COMPLETE:        // 上拉加载 已经拉取全部数据      (如果是给SwipeNest使用  忽略)
                loadProgressBar.setVisibility(View.GONE);
                textLoad.setVisibility(View.VISIBLE);
                break;
        }
    }

}

       

```
    
**SwipeRefresh 支持4种刷新模式**   

```
(BOTH = SwipeModel.SWIPE_BOTH) 同时支持下拉刷新与上拉加载  
(ONLY_REFRESH == SwipeModel.SWIPE_ONLY_REFRESH)) 只支持下拉刷新 
(ONLY_REFRESH == SwipeModel.SWIPE_ONLY_LOADINN) 只支持上拉加载 
(SWIPE_NONE == SwipeModel.SWIPE_NONE）都不支持
```
**note**  
```
  一：只有当包含的子View有足够内容进行独立滑动时 下拉加载才启动有效
  二：通过Xml app:fresh_model或者SwipeRefresh.setSwipeModel(SwipeControl.SwipeModel model) 定义SwipeRefresh刷新模式
  三：SwipeNest 不支持上拉加载; 请用其他方式实现  比如：
          (1) Adater的getView(getView(int postion) postion 为数据列表的最后一项时可以进行上拉加载操作）
          (2) 设置ViewTreeObserver.addOnGlobalLayoutListener 监听当ViewcanScrollVertically（-1） 与 Adater.getCount() 判断当前是否可以开始上拉加载；
          (3) 直接使用RecyleView();
  四：SwipeNest 目前只支持NestedScrollingChild继承类作为子View；
  
```

Add Gradle dependency:
```gradle
dependencies {
  compile 'com.github.powyin:scroll:1.2.1'
}
```




