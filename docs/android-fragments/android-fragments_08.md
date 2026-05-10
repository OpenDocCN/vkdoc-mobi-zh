# 第 1 章：Fragment 基础

```java
@Override
public void onSaveInstanceState(Bundle outState) {
    Log.v(MainActivity.TAG, "进入 TitlesFragment 的 onSaveInstanceState");
    super.onSaveInstanceState(outState);
    outState.putInt("curChoice", mCurCheckPosition);
}

@Override
public void onListItemClick(ListView l, View v, int pos, long id) {
    Log.v(MainActivity.TAG,
          "进入 TitlesFragment 的 onListItemClick。位置 = " + pos);
    myActivity.showDetails(pos);
    mCurCheckPosition = pos;
}

@Override
public void onDetach() {
    Log.v(MainActivity.TAG, "进入 TitlesFragment 的 onDetach");
    super.onDetach();
    myActivity = null;
}
```

与 `DetailsFragment` 不同，这个 fragment 在 `onCreateView()` 回调中不需要做任何操作。这是因为你继承了 `ListFragment` 类，该类本身已包含一个 `ListView`。`ListFragment` 的默认 `onCreateView()` 会为你创建这个 `ListView` 并返回它。直到 `onActivityCreated()` 时，你才开始执行真正的应用逻辑。此时，在你的应用中，可以确保 Activity 的视图层次结构以及这个 fragment 的视图层次结构都已被创建。这个 `ListView` 的资源 ID 是 `android.R.id.list1`，但如果需要获取它的引用，你始终可以调用 `getListView()`，在本例中你正是在 `onActivityCreated()` 里调用了它。由于 `ListFragment` 管理着 `ListView`，因此不要直接将适配器附加到 `ListView` 上。你必须改用 `ListFragment` 的 `setListAdapter()` 方法。此时 Activity 的视图层次结构已设置完毕，所以你可以安全地回到 Activity 中去调用 `showDetails()`。

在示例 Activity 的生命周期的这一阶段，你已经为列表视图添加了列表适配器，恢复了当前选中位置（如果因配置变更等操作进行了状态恢复），并且你已经请求 Activity（在 `showDetails()` 中）设置文本以对应选中的莎士比亚标题。

你的 `TitlesFragment` 类还在列表上设置了监听器，因此当用户点击另一个标题时，`onListItemClick()` 回调会被调用，你再次通过 `showDetails()` 方法切换文本以对应那个新标题。

[www.it-ebooks.info](http://www.it-ebooks.info/)

