# Android ViewPager使用详解 #
 这是谷歌官方给我们提供的一个兼容低版本安卓设备的软件包，里面包囊了只有在安卓3.0以上可以使用的api。而viewpager就是其中之一利用它，我们可以做很多事情，从最简单的导航，到页面菜单等等。那如何使用它呢，与LisstView类似，我们也需要一个适配器，他就是PagerAdapter。

 ViewPager的功能就是可以使视图滑动，就像Lanucher左右滑动那样。分三个步骤来使用它：

1. 在住布局文件里加入

		<android.support.v4.view.ViewPager //这个组件，注意这个组件是用来显示左右滑动的界面的，如果不加载xml布局文件，他是不会显示内容的。
        android:id="@+id/viewpager"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center" >

 2. 加载要显示的页卡

		
			LayoutInflater lf = getLayoutInflater().from(this);   
		    view1 = lf.inflate(R.layout.layout1, null);  
		    view2 = lf.inflate(R.layout.layout2, null);  
		    view3 = lf.inflate(R.layout.layout3, null);  
	  
	        viewList = new ArrayList<View>();// 将要分页显示的View装入数组中  
	        viewList.add(view1);  
	        viewList.add(view2);  
	        viewList.add(view3); 

 3. 在Activity里实例化ViewPager组件，并设置它的Adapter（就是PagerAdapter，方法与ListView一样的），在这里一般需要重写PagerAdapter。

			PagerAdapter pagerAdapter = new PagerAdapter() {  
  
            @Override  
            public boolean isViewFromObject(View arg0, Object arg1) {  
  
                return arg0 == arg1;  
            }  
  
            @Override  
            public int getCount() {  
  
                return viewList.size();  
            }  
  
            @Override  
            public void destroyItem(ViewGroup container, int position,  
                    Object object) {  
                container.removeView(viewList.get(position));  
  
            }  
  
            @Override  
            public int getItemPosition(Object object) {  
  
                return super.getItemPosition(object);  
            }  
  
            @Override  
            public CharSequence getPageTitle(int position) {  
  
                return titleList.get(position);  
            }  
  
            @Override  
            public Object instantiateItem(ViewGroup container, int position) {  
                container.addView(viewList.get(position));  
                weibo_button=(Button) findViewById(R.id.button1);  
                weibo_button.setOnClickListener(new OnClickListener() {  
                      
                    public void onClick(View v) {  
                        intent=new Intent(ViewPagerDemo.this,WeiBoActivity.class);  
                        startActivity(intent);  
                    }  
                });  
                return viewList.get(position);  
            }  
  
	        };  
	        viewPager.setAdapter(pagerAdapter); 

这是重写PagerAdapter的一个方法，我们还可以这样做：

	public class MyViewPagerAdapter extends PagerAdapter{  
        private List<View> mListViews;  
          
        public MyViewPagerAdapter(List<View> mListViews) {  
            this.mListViews = mListViews;//构造方法，参数是我们的页卡，这样比较方便。  
        }  
  
        @Override  
        public void destroyItem(ViewGroup container, int position, Object object)   {     
            container.removeView(mListViews.get(position));//删除页卡  
        }  
  
  
        @Override  
        public Object instantiateItem(ViewGroup container, int position) {  //这个方法用来实例化页卡         
             container.addView(mListViews.get(position), 0);//添加页卡  
             return mListViews.get(position);  
        }  
  
        @Override  
        public int getCount() {           
            return  mListViews.size();//返回页卡的数量  
        }  
          
        @Override  
        public boolean isViewFromObject(View arg0, Object arg1) {             
            return arg0==arg1;//官方提示这样写  
        }  
    }   


大同小异，有一定很重要，就是我们需要重写哪些方法。从上面的图片可以看到，ViewPager的适配器是PagerAdapter，它是基类提供适配器来填充页面ViewPager内部，你很可能想要使用一个更具体的实现,如FragmentPagerAdapter或FragmentStatePagerAdapter。在这里需要说明一下，其实ViewPager应该和Fragment一起使用，至少谷歌官方是这么想的，但是在3.0之下，我们没有必要这么做。下面要注意，当你实现一个PagerAdapter,你必须至少覆盖以下方法:

- instantiateItem(ViewGroup, int)
- destroyItem(ViewGroup, int, Object)
- getCount()
- isViewFromObject(View, Object)

从上面的例子中可以看到，我们最少也是实现了上面四个方法，当然如果你想让程序更健壮或是功能更全面，你可以重写其他的方法。下面看一下第一个完整的示例代码：

  主页面Activity：

	package com.example.viewpagerdemo;  
	  
	import java.util.ArrayList;  
	import java.util.List;  
	import android.os.Bundle;  
	import android.app.Activity;  
	import android.content.Context;  
	import android.content.Intent;  
	import android.support.v4.view.PagerAdapter;  
	import android.support.v4.view.PagerTabStrip;  
	import android.support.v4.view.PagerTitleStrip;  
	import android.support.v4.view.ViewPager;  
	import android.util.AttributeSet;  
	import android.view.LayoutInflater;  
	import android.view.Menu;  
	import android.view.View;  
	import android.view.View.OnClickListener;  
	import android.view.ViewGroup;  
	import android.widget.Button;  
  
	public class ViewPagerDemo extends Activity {  
  
    private View view1, view2, view3;//需要滑动的页卡  
    private ViewPager viewPager;//viewpager  
    private PagerTitleStrip pagerTitleStrip;//viewpager的标题  
    private PagerTabStrip pagerTabStrip;//一个viewpager的指示器，效果就是一个横的粗的下划线  
    private List<View> viewList;//把需要滑动的页卡添加到这个list中  
    private List<String> titleList;//viewpager的标题  
    private Button weibo_button;//button对象，一会用来进入第二个Viewpager的示例  
   private Intent intent;  
    @Override  
    public void onCreate(Bundle savedInstanceState) {  
        super.onCreate(savedInstanceState);  
        setContentView(R.layout.activity_view_pager_demo);  
        initView();  
    }  
      /*在这里需要说明一下，在上面的图片中我们看到了，PagerTabStrip，PagerTitleStrip，他们其实是viewpager的一个指示器，前者效果就是一个横的粗的下划线，后者用来显示各个页卡的标题，当然而这也可以共存。在使用他们的时候需要注意，看下面的布局文件，要在android.support.v4.view.ViewPager里面添加 
android.support.v4.view.PagerTabStrip以及android.support.v4.view.PagerTitleStrip。 
 
    private void initView() { 
        viewPager = (ViewPager) findViewById(R.id.viewpager); 
        //pagerTitleStrip = (PagerTitleStrip) findViewById(R.id.pagertitle); 
        pagerTabStrip=(PagerTabStrip) findViewById(R.id.pagertab); 
        pagerTabStrip.setTabIndicatorColor(getResources().getColor(R.color.gold));  
        pagerTabStrip.setDrawFullUnderline(false); 
        pagerTabStrip.setBackgroundColor(getResources().getColor(R.color.azure)); 
        pagerTabStrip.setTextSpacing(50); 
        /* 
        weibo_button=(Button) findViewById(R.id.button1); 
        weibo_button.setOnClickListener(new OnClickListener() { 
             
            public void onClick(View v) { 
                intent=new Intent(ViewPagerDemo.this,WeiBoActivity.class); 
                startActivity(intent); 
            } 
        }); 
        */  
          
        view1 = findViewById(R.layout.layout1);  
        view2 = findViewById(R.layout.layout2);  
        view3 = findViewById(R.layout.layout3);  
  
        LayoutInflater lf = getLayoutInflater().from(this);  
        view1 = lf.inflate(R.layout.layout1, null);  
        view2 = lf.inflate(R.layout.layout2, null);  
        view3 = lf.inflate(R.layout.layout3, null);  
  
        viewList = new ArrayList<View>();// 将要分页显示的View装入数组中  
        viewList.add(view1);  
        viewList.add(view2);  
        viewList.add(view3);  
  
        titleList = new ArrayList<String>();// 每个页面的Title数据  
        titleList.add("wp");  
        titleList.add("jy");  
        titleList.add("jh");  
  
        PagerAdapter pagerAdapter = new PagerAdapter() {  
  
            @Override  
            public boolean isViewFromObject(View arg0, Object arg1) {  
  
                return arg0 == arg1;  
            }  
  
            @Override  
            public int getCount() {  
  
                return viewList.size();  
            }  
  
            @Override  
            public void destroyItem(ViewGroup container, int position,  
                    Object object) {  
                container.removeView(viewList.get(position));  
  
            }  
  
            @Override  
            public int getItemPosition(Object object) {  
  
                return super.getItemPosition(object);  
            }  
  
            @Override  
            public CharSequence getPageTitle(int position) {  
  
                return titleList.get(position);//直接用适配器来完成标题的显示，所以从上面可以看到，我们没有使用PagerTitleStrip。当然你可以使用。  
  
            }  
  
            @Override  
            public Object instantiateItem(ViewGroup container, int position) {  
                container.addView(viewList.get(position));  
                weibo_button=(Button) findViewById(R.id.button1);//这个需要注意，我们是在重写adapter里面实例化button组件的，如果你在onCreate()方法里这样做会报错的。  
                weibo_button.setOnClickListener(new OnClickListener() {  
                      
                    public void onClick(View v) {  
                        intent=new Intent(ViewPagerDemo.this,WeiBoActivity.class);  
                        startActivity(intent);  
                    }  
                });  
                return viewList.get(position);  
            }  
  
        };  
        viewPager.setAdapter(pagerAdapter);  
    }  
  
    @Override  
    public boolean onCreateOptionsMenu(Menu menu) {  
        getMenuInflater().inflate(R.menu.activity_view_pager_demo, menu);  
        return true;  
    }  
  
}  