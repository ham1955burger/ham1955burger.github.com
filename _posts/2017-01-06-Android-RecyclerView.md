---
layout: post
title:  "Android RecyclerView 사용하기"
date:   2017-01-06 12:24:00 +09
categories: Android
---

iOS TableView와 같은 화면을 Android에도 구현해보자.

용어가 좀 다른데, Android에서 TableView는 엑셀과 같이 표를 만들어 주는 component이고 iOS TableView와 같은 component는 ListView다.

ListView는 Customize에 한계가 있던 반면, RecyclerView는 확장성과 유연성을 겸비한 녀석이라고. 그래서인지 ListView의 기본 기능들을 RecyclerView에선 직접 구현해야되는 경우가 많다고.[^1]


아래와 같이 기본데이터 3개가 있고, ADD ITEM 버튼을 누르면 추가, 클릭 할 경우 Toast, 롱클릭 할 경우 삭제 해보자.

![RecyclerViewTest](/assets/images/android_recyclerView/recyclerView_test.png)

---

>#### Layout

* #### RecyclerView

  RecyclerView를 사용하기 위해선 gradle dependencies에 추가를 해줘야 한다.

  * app > build.gradle

        dependencies {
            ...
            compile 'com.android.support:recyclerview-v7:24.2.1'
        }

  또는

  * Android Studio Layout Palette > RecyclerView 클릭

    ![set_dependencies_recyclerView](/assets/images/android_recyclerView/set_dependencies_recyclerView.png)

    gradle에 25.0.0으로 자동 추가된다. 버전 안맞으면 바꿔줄 것.

  적당한 위치에 배치.

* #### Item

  iOS TableView안에 Cell이 있듯이 Android에는 Item이 있다.

  Item Layout도 적당히 만들어줄 것.

  ![main_list_item](/assets/images/android_recyclerView/main_list_item.png)

---

>#### Model Class 만들기

Item의 data가 담겨있는 ModelClass를 만들어보자.

{% highlight java %}
    public class MainListItem {
        String title;
        String sub;

        public MainListItem(String title, String sub) {
            this.title = title;
            this.sub = sub;
        }

        public String getTitle() {
            return title;
        }

        public void setTitle(String title) {
            this.title = title;
        }

        public String getSub() {
            return sub;
        }

        public void setSub(String sub) {
            this.sub = sub;
        }
    }
{% endhighlight %}

**Tip!** setter, getter는 `command + N`단축키로 쉽게 만들 수 있다. ~~만능 단축키~~

---

>#### Adapter와 ViewHolder

* #### Adapter

Android에서 RecyclerView에 item을 보여주기 위해선 Adapter를 이용해야 한다.
iOS에 UITableViewDelegate, UITableViewDataSource라고 보면 된다.

* #### ViewHolder

ViewHolder는 Item Layout을 source code로 bind하고, 그 안의 component들을 bind하여 정의할때 쓰인다. iOS에 UITableViewCell과 같다.

{% highlight java %}

    // Adapter를 상속 받는 MainListAdapter Class
    public class MainListAdapter extends RecyclerView.Adapter<MainListAdapter.ViewHolder> {
        // model class MainListItem형을 갖는 ArrayList 정의
        private ArrayList<MainListItem> mainListItems = new ArrayList<MainListItem>();
        private Context context;

        // Constructor
        public MainListAdapter(ArrayList<MainListItem> mainListItems, Context context) {
            this.mainListItems = mainListItems;
            this.context = context;
        }

        @Override
        public MainListAdapter.ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
            // ViewHolder 생성
            View view = LayoutInflater.from(parent.getContext()).inflate(R.layout.main_list_item, parent, false);
            MainListAdapter.ViewHolder holder = new ViewHolder(view);
            return holder;
        }

        @Override
        public void onBindViewHolder(MainListAdapter.ViewHolder holder, int position) {
            // same iOS cellForRowAt
            holder.title.setText(mainListItems.get(position).getTitle());
            holder.sub.setText(mainListItems.get(position).getSub());

            // Item click
            holder.itemView.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View view) {
                    Toast.makeText(context, position + "번째 item click", Toast.LENGTH_SHORT).show();
                }
            });

            // Item long click
            holder.itemView.setOnLongClickListener(new View.OnLongClickListener() {
                @Override
                public boolean onLongClick(View view) {
                    mainListItems.remove(position);
                    notifyItemRemoved(position);
                    notifyItemRangeChanged(position, getItemCount());
                    return true;
                }
            });
        }

        @Override
        public int getItemCount() {
            // same iOS numberOfRowsInSection
            return mainListItems.size();
        }

        // Item 추가
        public void addItem(String title, String sub) {
            MainListItem item = new MainListItem(title, sub);
            mainListItems.add(item);
            notifyItemInserted(getItemCount());
        }

        // ViewHolder Class
        public class ViewHolder extends RecyclerView.ViewHolder {
            @BindView(R.id.itemTitleTextView) TextView title;
            @BindView(R.id.itemSubTextView) TextView sub;

            public ViewHolder(View itemView) {
                super(itemView);
                ButterKnife.bind(this, itemView);

            }
        }
    }

{% endhighlight %}

**Tip!** 상속받을 때 필수 추상 메소드는 빨간 줄에 커서를 가져다 대고 `alt + enter` > implements method로 쉽게 구현 가능
![shortcut_abstract_method](/assets/images/android_recyclerView/shortcut_abstract_method.png)

---

>#### Activity

RecyclerView를 사용하는 Activity에선, 만들어둔 Adapter형으로 객체를 생성해 RecyclerView에 붙여주면 된다.

{% highlight java %}
    public class MainActivity extends Activity {
        @BindView(R.id.recyclerView) RecyclerView recyclerView;
        private MainListAdapter adapter;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
            ButterKnife.bind(this);

            // adapter 정의
            adapter = new MainListAdapter(new ArrayList<MainListItem>(), this);

            recyclerView.setHasFixedSize(true);
            recyclerView.setLayoutManager(new LinearLayoutManager(this));

            // 기본 데이터 set
            adapter.addItem("테스트1", "테스트 1입니다.");
            adapter.addItem("테스트2", "테스트 2입니다.");
            adapter.addItem("테스트3", "테스트 3입니다.");

            // RecyclerView adapter set
            recyclerView.setAdapter(adapter);
        }

        @OnClick(R.id.addItemButton) void clickedAddItem() {
            adapter.addItem("테스트" + (adapter.getItemCount() + 1),
                    "테스트 " + (adapter.getItemCount() + 1) + "입니다.");
        }
    }
{% endhighlight %}

---

처음에 할때는 뭐 이렇게 많이 만들어야 돼? 했는데 따지고 보면 iOS랑 별 다를게 없었다는.

추후 필요할때 LayoutManager, ItemDecoration, ItemAnimator도 써보고 정리하는걸로.~~언제가 될진..~~

---

>#### 참고

[^1]: [Android ListView vs RecyclerView](http://www.truiton.com/2015/03/android-recyclerview-vs-listview-comparison/)
