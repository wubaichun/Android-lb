 

## 时间戳的实现

 



 

#### 第一步：

#### Notelist中增加时间戳

 

首先是notelist_item.xml中增加时间戳的布局，text2为增加的时间戳部分

 

```
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_height="match_parent"
    android:layout_width="match_parent"
    android:orientation="vertical">
    <TextView xmlns:android="http://schemas.android.com/apk/res/android"
        android:id="@android:id/text1"
        android:layout_width="match_parent"
        android:layout_height="?android:attr/listPreferredItemHeight"
        android:gravity="center_vertical"
        android:paddingLeft="5dip"
        android:singleLine="true"
        android:textAppearance="?android:attr/textAppearanceLarge" />
    <TextView
        android:id="@android:id/text2"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:paddingLeft="5dip"/>
</LinearLayout>
```

 

在PROJECTION中增加修改的时间的显示

 

```
 private static final String[] PROJECTION = new String[]{
            NotePad.Notes._ID, // 0
            NotePad.Notes.COLUMN_NAME_TITLE, // 1
            NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE,//2修改的时间
    };
```

 

在datacolumn和viewID中增加时间戳部分需要的参数

 

```
  String[] dataColumns = {NotePad.Notes.COLUMN_NAME_TITLE, NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE};
  int[] viewIDs = {android.R.id.text1, android.R.id.text2};
```

 

#### 第二步：

####  在NotePadProvider中的Insert函数中增加时间戳部分

 

首先是时间戳的格式转化

 

```
 SimpleDateFormat dateformat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
 String dateStr = dateformat.format(System.currentTimeMillis());
```

 

将原生代码中创建的时间与修改的时间中写入的时间替换为格式化后的时间

 

```
  if (values.containsKey(NotePad.Notes.COLUMN_NAME_CREATE_DATE) == false) {
            values.put(NotePad.Notes.COLUMN_NAME_CREATE_DATE,dateStr);
  }//写入创建时间
  
  if (values.containsKey(NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE) == false) {
            values.put(NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE,dateStr);
  }//写入修改时间
```

 

#### 第三步：

####  在NoteEditor中的updateNote函数中对写入的修改时间进行格式转化

 

```
 SimpleDateFormat dateformat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
 String dateStr = dateformat.format(System.currentTimeMillis());//时间日期的转换
 values.put(NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE, dateStr);
```

 

### 实现结果

 

![](./photo\1.png)

 

## 搜索框的实现

 

#### 实现过程

 

#### 第一步：

#### 新增布局文件searchview.xml，设置一个搜索的布局文件，由searchview，textview，listview组件组成，为搜索后所表现的样子

 

```
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">
    <SearchView
        android:id="@+id/search2"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:queryHint="请输入搜索内容"
        >
    </SearchView>

    <ListView
        android:paddingLeft="10dp"
        android:paddingRight="10dp"
        android:id="@android:id/list"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1"
        android:drawSelectorOnTop="false" />
    <TextView
        android:paddingLeft="10dp"
        android:paddingRight="10dp"
        android:id="@android:id/empty"
        android:gravity="center"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1"
        android:text="无搜索结果" />
</LinearLayout>
```

 

#### 第二步：

####  修改NotesList一开始加载的布局文件 

```
  setContentView(R.layout.searchview);
```

 

#### 第三步：设置searchview监听器

 

 

```
private void SearchView(final SimpleCursorAdapter adapter) {
        SearchView searchView = findViewById(R.id.search2);
        searchView.setSubmitButtonEnabled(true);
        searchView.setOnQueryTextListener(new SearchView.OnQueryTextListener() {
            @Override
            public boolean onQueryTextSubmit(String query) {
                return false;
            }

            @Override
            public boolean onQueryTextChange(String s) {
                Cursor newCursor;

                if (!s.equals("")) {
                    String selection = NotePad.Notes.COLUMN_NAME_TITLE + " GLOB '*" + s + "*'";
                    newCursor = getContentResolver().query(
                            getIntent().getData(),
                            PROJECTION,
                            selection,
                            null,
                            NotePad.Notes.DEFAULT_SORT_ORDER
                    );
                } else {
                    newCursor = getContentResolver().query(
                            getIntent().getData(),
                            PROJECTION,
                            null,
                            null,
                            NotePad.Notes.DEFAULT_SORT_ORDER
                    );
                }
                adapter.swapCursor(newCursor); // 视图将同步更新！
                return true;
            }
        });
    }
```



### 实现结果

 

![](./photo\2.png)

![](./photo\3.png)