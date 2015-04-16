# ListView中convertView和ViewHolder的工作原理 #
## LsitView和Adapter ##
### 工作原理： ###
 1.ListView针对List中每个item，要求adapter给我一个视图(getView)
 2.一个新的视图被返回并显示

如果我们有上亿个item要显示怎么办？为每个项目创建一个新视图？NO！这不可能~~~Android实际上为你缓存了视图

Android中有个叫做Recycler(反复循环器)的构件，下图是它的工作原理：
![](..\asset\image\back-top.png)

1. 如果你有10亿个项目(item)，其中只有可见的项目存在内存中，其他的在Recycler中
2. ListView先请求一个type1视图(getView)，然后请求其他可见的项目。conVertView在getView中时null的
3. 当item1滚出屏幕，并且一个新的项目从屏幕地段上来时，ListView再请求一个type1视图。convertView此时不是空值了，它的值是item1.你只需要设定新的数据返回convertView,不必重新创建一个视图。这样直接使用convertView从而减少了很不不必要view的创建

更快的方式是定义一个ViewHolder,将convertView的tag设置为ViewHolder，不为空是重新使用
 
ViewHolder只是将需要缓存的那些view封装好，convertView的setTag才是将这些缓存起来供下次调用
当你的listview里布局多样化的时候 viewholder的作用就有比较明显的体现了。 当然了，单一模式的布局一样有性能优化的作用 只是不直观。  假如你2种模式的布局 当发生回收的时候 你会用setTag分别记录是哪两种   这两种模式会被封装到viewholder中进行保存方便你下次使用。 VH就是个静态类 与缓存无关的

	public class MultipleItemsList extends ListActivity {
  
    private MyCustomAdapter mAdapter;
  
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        mAdapter = new MyCustomAdapter();
        for (int i = 0; i < 50; i++) {
            mAdapter.addItem("item " + i);
        }
        setListAdapter(mAdapter);
    }
  
    private class MyCustomAdapter extends BaseAdapter {
  
        private ArrayList mData = new ArrayList();
        private LayoutInflater mInflater;
  
        public MyCustomAdapter() {
            mInflater = (LayoutInflater)getSystemService(Context.LAYOUT_INFLATER_SERVICE);
        }
  
        public void addItem(final String item) {
            mData.add(item);
            notifyDataSetChanged();
        }
  
        @Override
        public int getCount() {
            return mData.size();
        }
  
        @Override
        public String getItem(int position) {
            return mData.get(position);
        }
  
        @Override
        public long getItemId(int position) {
            return position;
        }
  
        @Override
        public View getView(int position, View convertView, ViewGroup parent) {
            System.out.println("getView " + position + " " + convertView);
            ViewHolder holder = null;
            if (convertView == null) {
                convertView = mInflater.inflate(R.layout.item1, null);
                holder = new ViewHolder();
                holder.textView = (TextView)convertView.findViewById(R.id.text);
                convertView.setTag(holder);
            } else {
                holder = (ViewHolder)convertView.getTag();
            }
            holder.textView.setText(mData.get(position));
            return convertView;
        }
  
    }
  
    public static class ViewHolder {
        public TextView textView;
    }
}

android开发中Listview是一个很重要的组件，它以列表的形式根据数据的长自适应展示具体内容,用户可以自由的定义listview每一列的布局，但当listview有大量的数据需要加载的时候，会占据大量内存，影响性能，这时候就需要按需填充并重新使用view来减少对象的创建。</br>
ListView加载数据都是在public View getView(int position, View convertView, ViewGroup parent) {}方法中进行的(要自定义listview都需要重写listadapter:如BaseAdapter，SimpleAdapter,CursorAdapter的等的getvView方法),优化listview的加载速度就要让convertView匹配列表类型，并最大程度上的重新使用convertView。</br>
getview的加载方法一般有以下三种种方式：

1. 最慢的加载方式是每一次都重新定义一个View载入布局，再加载数据

	
		public View getView(int position, View convertView, ViewGroup parent) {
		 View item = mInflater.inflate(R.layout.list_item_icon_text, null);
		 ((TextView) item.findViewById(R.id.text)).setText(DATA[position]);
		 ((ImageView) item.findViewById(R.id.icon)).setImageBitmap(
		 (position & 1) == 1 ? mIcon1 : mIcon2);
		 return item;
		}


 
2. 正确的加载方式是当convertView不为空的时候直接重新使用convertView从而减少了很多不必要的View的创建，然后加载数据

	public View getView(int position, View convertView, ViewGroup parent) {
	 if (convertView == null) {
	 convertView = mInflater.inflate(R.layout.item, parent, false);
	 }
	 ((TextView) convertView.findViewById(R.id.text)).setText(DATA[position]);
	 ((ImageView) convertView.findViewById(R.id.icon)).setImageBitmap(
	 (position & 1) == 1 ? mIcon1 : mIcon2);
	 return convertView;
	 }

 
3. 最快的方式是定义一个ViewHolder，将convetView的tag设置为ViewHolder,不为空时重新使用即可
	static class ViewHolder {
	TextView text;
	ImageView icon;
	}
	 
	public View getView(int position, View convertView, ViewGroup parent) {
	 ViewHolder holder;
	 
	 if (convertView == null) {
	 convertView = mInflater.inflate(R.layout.list_item_icon_text,
	 parent, false);
	 holder = new ViewHolder();
	 holder.text = (TextView) convertView.findViewById(R.id.text);
	 holder.icon = (ImageView) convertView.findViewById(R.id.icon);
	 convertView.setTag(holder);
	} else {
	holder = (ViewHolder) convertView.getTag();
	}
	holder.text.setText(DATA[position]);
	holder.icon.setImageBitmap((position & 1) == 1 ? mIcon1 : mIcon2);
	return convertView;
	}