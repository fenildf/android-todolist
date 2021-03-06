## 介绍

一个创建、编辑、删除和搜索 Todo 的示例。

Todo页，

![img](https://raw.githubusercontent.com/lzwjava/plan/master/android-todo-360.png)

应用内搜索页，

![img](https://raw.githubusercontent.com/lzwjava/plan/master/todo360.png)


## 演示功能

* 数据增删改查, AVObject 与 AVQuery 的使用
* AVObject 的子类化 
* 统计功能, 统计相应的事件，从而分析用户喜好
* 统计的自定义事件功能
* 应用内搜索, 便捷地找到所需要的信息

## 如何运行

* 用 Android Gradle 打开本项目，运行即可
* 导入 Eclipse 可能会提示某些类没有找到，请到 [SDK 下载页](https://leancloud.cn/docs/sdk_down.html)下载 SDK。需要基础、推送、统计、应用内搜索四个模块。

## 替换 App 信息

Demo 使用的是公共的 app id 和 app key，您可以在`com.avos.demo.ToDoListApplication`修改成您自己的应用 id 和 key。

不过首先需要在您的数据管理平台手工创建一个 Class，名称设定为`Todo`即可。

## 阅读代码 

核心的用到 AVOS Cloud 服务都在[AVService.java](https://github.com/avoscloud/Android-SDK-demos/blob/master/AVOSCloud-Todo/src/com/avos/demo/AVService.java) 里，

```java
public class AVService {
  public static void AVInit(Context ctx) {
    // 初始化应用 Id 和 应用 Key，您可以在应用设置菜单里找到这些信息
    AVOSCloud.initialize(ctx, "70l90kzm53nplnu013ala0j8wipr594d36m5zuz94ukvmh5s",
        "lamrsuheyiaqcx4o7m3jaz4awaeukerit1mucnjwk7ibokfv");
    // 启用崩溃错误报告
    AVAnalytics.enableCrashReport(ctx, true);
    // 注册子类
    AVObject.registerSubclass(Todo.class);
  }

  public static void fetchTodoById(String objectId,GetCallback<AVObject> getCallback) {
    Todo todo = new Todo();
    todo.setObjectId(objectId);
    // 通过Fetch获取content内容
    todo.fetchInBackground(getCallback);
  }

  public static void createOrUpdateTodo(String objectId, String content, SaveCallback saveCallback) {
    final Todo todo = new Todo();
    if (!TextUtils.isEmpty(objectId)) {
      // 如果存在objectId，保存会变成更新操作。
      todo.setObjectId(objectId);
    }
    todo.setContent(content);
    // 异步保存
    todo.saveInBackground(saveCallback);
  }

  public static List<Todo> findTodos() {
    // 查询当前Todo列表
    AVQuery<Todo> query = AVQuery.getQuery(Todo.class);
    // 按照更新时间降序排序
    query.orderByDescending("updatedAt");
    // 最大返回1000条
    query.limit(1000);
    try {
      return query.find();
    } catch (AVException exception) {
      Log.e("tag", "Query todos failed.", exception);
      return Collections.emptyList();
    }
  }

  public static void searchQuery(String inputSearch) {
    AVSearchQuery searchQuery = new AVSearchQuery(inputSearch);
    searchQuery.search();
  }
}
```
