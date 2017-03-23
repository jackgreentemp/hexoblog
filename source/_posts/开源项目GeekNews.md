---
title: 开源项目GeekNews
date: 2017-02-27 21:27:46
tags: Android
---
# 项目地址
https://github.com/codeestX/GeekNews

# 值得借鉴的地方
1. 项目结构（MVP+Dagger2）
2. Retrofit对多个baseUrl的管理
3. realm的使用
4. 侧边栏导航、tab导航的使用
5. 单Activity+多Fragment的管理（基于me.yokeyword:fragmentation）
6. 页面跳转共享资源的使用（列表->详情共享新闻图片）
7. ==搜索框的使用，搜索时间的发布订阅处理逻辑==

# 解读
1. RxBus事件总线

DailyPresenter的构造方法中有一个registerEvent方法
```java
@Inject
public DailyPresenter(RetrofitHelper mRetrofitHelper,RealmHelper mRealHelper) {
    this.mRetrofitHelper = mRetrofitHelper;
    this.mRealmHelper = mRealHelper;
    registerEvent();
}
```
registerEvent()是订阅RxBus事件的

```java
private void registerEvent() {
    Subscription rxSubscription = RxBus.getDefault().toObservable(CalendarDay.class)
            .subscribeOn(Schedulers.io())
            .map(new Func1<CalendarDay, String>() {
            ...
```
在DailyFragment中，发布RxBus事件

```
swipeRefresh.setOnRefreshListener(new SwipeRefreshLayout.OnRefreshListener() {
    @Override
    public void onRefresh() {
        if(currentDate.equals(DateUtil.getTomorrowDate())) {
            mPresenter.getDailyData();
        } else {
            int year = Integer.valueOf(currentDate.substring(0,4));
            int month = Integer.valueOf(currentDate.substring(4,6));
            int day = Integer.valueOf(currentDate.substring(6,8));
            CalendarDay date = CalendarDay.from(year, month - 1, day);
            RxBus.getDefault().post(date);
        }
    }
});
```
2. DailyAdapter

有三种样式：图片滚动栏（类似于web的banner）、日期、新闻item

```
@Override
public int getItemViewType(int position) {
    if(!isBefore) {
        if(position == 0) {
            return ITEM_TYPE.ITEM_TOP.ordinal();
        } else if(position == 1) {
            return ITEM_TYPE.ITEM_DATE.ordinal();
        } else {
            return ITEM_TYPE.ITEM_CONTENT.ordinal();
        }
    } else {
        if(position == 0) {
            return ITEM_TYPE.ITEM_DATE.ordinal();
        } else {
            return ITEM_TYPE.ITEM_CONTENT.ordinal();
        }
    }
}
```
其中positi==0的就是图片滚动栏，由一个ViewPager实现，该ViewPager的Adapter是由另外一个Adapter持有的

```
@Override
public RecyclerView.ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
    if(viewType == ITEM_TYPE.ITEM_TOP.ordinal()) {
        mAdapter = new TopPagerAdapter(mContext,mTopList);
        return new TopViewHolder(inflater.inflate(R.layout.item_top, parent, false));
    } else if(viewType == ITEM_TYPE.ITEM_DATE.ordinal()) {
        return new DateViewHolder(inflater.inflate(R.layout.item_date, parent, false));
    }
    return new ContentViewHolder(inflater.inflate(R.layout.item_daily, parent, false));
}
```
3. compose的使用

可以少写切换线程的代码

```
@Override
public void getDailyData() {
    Subscription rxSubscription = mRetrofitHelper.fetchDailyListInfo()
            .compose(RxUtil.<DailyListBean>rxSchedulerHelper())
            ...
}
```
其中的compose主要是在io线程处理数据，ui线程处理结果

```
/**
 * 统一线程处理
 * @param <T>
 * @return
 */
public static <T> Observable.Transformer<T, T> rxSchedulerHelper() {    //compose简化线程
    return new Observable.Transformer<T, T>() {
        @Override
        public Observable<T> call(Observable<T> observable) {
            return observable.subscribeOn(Schedulers.io())
                    .observeOn(AndroidSchedulers.mainThread());
        }
    };
}
```
4. 新闻的item点击进入detail时会共享新闻图片

xml如下

```xml
<com.codeest.geeknews.widget.SquareImageView
    android:id="@+id/iv_daily_item_image"
    android:transitionName="shareView"
    android:src="@mipmap/ic_launcher"
    android:layout_width="@dimen/list_image_size"
    android:layout_height="@dimen/list_image_size" />
    ...
```

java如下

```java
mAdapter.setOnItemClickListener(new DailyAdapter.OnItemClickListener() {
    @Override
    public void onItemClick(int position,View shareView) {
        mPresenter.insertReadToDB(mList.get(position).getId());
        mAdapter.setReadState(position,true);
        if(mAdapter.getIsBefore()) {
            mAdapter.notifyItemChanged(position + 1);
        } else {
            mAdapter.notifyItemChanged(position + 2);
        }
        Intent intent = new Intent();
        intent.setClass(mContext, ZhihuDetailActivity.class);
        intent.putExtra("id",mList.get(position).getId());
        ActivityOptions options = ActivityOptions.makeSceneTransitionAnimation(mActivity, shareView, "shareView");
        mContext.startActivity(intent,options.toBundle());
    }
});
```
detail中做如下处理

xml如下

```xml
<android.support.design.widget.AppBarLayout
    android:id="@+id/app_bar"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:transitionName="shareView"
    android:fitsSystemWindows="true">
    ...
```


java如下

```java
(getWindow().getSharedElementEnterTransition()).addListener(new Transition.TransitionListener() {
    @Override
    public void onTransitionStart(Transition transition) {
    }
    @Override
    public void onTransitionEnd(Transition transition) {
        /**
         * 测试发现部分手机(如红米note2)上加载图片会变形,没有达到centerCrop效果
         * 查阅资料发现Glide配合SharedElementTransition是有坑的,需要在Transition动画结束后再加载图片
         * https://github.com/TWiStErRob/glide-support/blob/master/src/glide3/java/com/bumptech/glide/supportapp/github/_847_shared_transition/DetailFragment.java
         */
        isTransitionEnd = true;
        if (imgUrl != null) {
            isImageShow = true;
            ImageLoader.load(mContext, imgUrl, detailBarImage);
        }
    }
    @Override
    public void onTransitionCancel(Transition transition) {
    }
    @Override
    public void onTransitionPause(Transition transition) {
    }
    @Override
    public void onTransitionResume(Transition transition) {
    }
});
```







