# SharedPreferences #
<div id="article_content" class="article_content">
<strong><span style="color:#ff0000">SharedPreferences简介</span></strong>
<p>在Android开发过程中，有时候我们需要保存一些简单的软件配置等简单数据的信息，而如果我们直接用数据库存储的话又不太方便，在这里我们就可以用到SharedPreferences，SharedPreferences保存的数据主要是类&#20284;于配置信息&#26684;式的数据，因此保存的数据主要是简单类型的键&#20540;对(key-value),它保存的是一个XML文件。</p>
<p><strong>SharedPreferences常用的属性和方法</strong></p>
<table border="1" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td valign="top">
<p><strong>方法名称</strong></p>
</td>
<td valign="top">
<p><strong>描述</strong></p>
</td>
</tr>
<tr>
<td valign="top">
<p><strong>public abstract boolean contains (String key)</strong></p>
</td>
<td valign="top">
<p>判断SharedPreferences是否包含特定key的数据</p>
</td>
</tr>
<tr>
<td valign="top">
<p><strong>public abstract SharedPreferences.Editor edit ()</strong></p>
</td>
<td valign="top">
<p>返回一个Edit对象用于操作SharedPreferences</p>
</td>
</tr>
<tr>
<td valign="top">
<p><strong>public abstract Map&lt;String, ?&gt; getAll ()</strong></p>
</td>
<td valign="top">
<p>获取SharedPreferences数据里全部的key-value对</p>
</td>
</tr>
<tr>
<td valign="top">
<p><strong>getXXX(String key,XXX defvlaue)</strong></p>
</td>
<td valign="top">
<p>获取SharedPreferences数据指定key所对应的value，如果该key不存在，返回默认&#20540;defValue。其中XXX可以是boolean、float、int、long、String等基本类型的&#20540;</p>
</td>
</tr>
</tbody>
</table>
<p>由于SharedPreference是一个接口，而且在这个接口里并没有提供写入数据和读取数据的能力。但是在其内部有一个Editor内部的接口，Edit这个接口有一系列的方法用于操作SharedPreference。</p>
<p><strong>Editor接口的常用方法</strong></p>
<table border="1" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td valign="top">
<p><strong>方法名称</strong></p>
</td>
<td valign="top">
<p><strong>描述</strong></p>
</td>
</tr>
<tr>
<td valign="top">
<p><strong>public abstract SharedPreferences.Editor clear ()</strong></p>
</td>
<td valign="top">
<p>清空SharedPreferences里所有的数据</p>
</td>
</tr>
<tr>
<td valign="top">
<p><strong>public abstract boolean commit ()</strong></p>
</td>
<td valign="top">
<p>当Editor编辑完成后，调用该方法可以提交修改，而且必须要调用这个数据才修改</p>
</td>
</tr>
<tr>
<td valign="top">
<p><strong>public abstract SharedPreferences.Editor putXXX (String key, boolean XXX)</strong></p>
</td>
<td valign="top">
<p>向SharedPreferences存入指定的key对应的数据，其中XXX可以是boolean、float、int、long、String等基本类型的&#20540;</p>
</td>
</tr>
<tr>
<td valign="top">
<p><strong>public abstract SharedPreferences.Editor remove (String key)</strong></p>
</td>
<td valign="top">
<p>删除SharedPreferences里指定key对应的数据项</p>
</td>
</tr>
</tbody>
</table>
<p>&nbsp;</p>
<p>SharedPreferences是一个接口，程序是无法创建SharedPreferences实例的，可以通过<strong>Context.getSharedPreferences(String name.int mode)</strong>来得到一个SharedPreferences实例</p>
<p>name：是指文件名称，不需要加后缀.xml，系统会自动为我们添加上。一般这个文件存储在<strong>/data/data/&lt;package name&gt;/shared_prefs</strong>下(这个面试常问到)</p>
<p>mode：是指定读写方式，其&#20540;有三种，分别为：</p>
<p><strong>Context.MODE_PRIVATE：</strong>指定该SharedPreferences数据只能被本应用程序读、写</p>
<p><strong>Context.MODE_WORLD_READABLE：</strong>指定该SharedPreferences数据能被其他应用程序读，但不能写</p>
<p><strong>Context.MODE_WORLD_WRITEABLE：</strong>指定该SharedPreferences数据能被其他应用程序读写。</p>
<p><span style="color:#ff0000"><strong>实例：用SharedPreferences进行简单的操作</strong></span></p>
<pre class="java" name="code">package com.jiahui.preferences;

import android.app.Activity;
import android.content.SharedPreferences;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;

public class SharedPreferencesDemoActivity extends Activity {

	private SharedPreferences sharedPreferences;

	private SharedPreferences.Editor editor;

	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);

		sharedPreferences = this.getSharedPreferences(&quot;test&quot;,MODE_WORLD_READABLE);
		editor = sharedPreferences.edit();

		final EditText edtName = (EditText) this.findViewById(R.id.edtName);
		final EditText edtAge = (EditText) this.findViewById(R.id.edtAge);

		Button btnRead = (Button) this.findViewById(R.id.btnRead);
		Button btnWrite = (Button) this.findViewById(R.id.btnWrite);

		btnRead.setOnClickListener(new View.OnClickListener() {

			@Override
			public void onClick(View v) {
				// 如果没有这个key就以默认值
				String name = sharedPreferences.getString(&quot;name&quot;, null);
				int age = sharedPreferences.getInt(&quot;age&quot;, 0);
				edtAge.setText(String.valueOf(age));
				edtName.setText(name);
			}
		});

		btnWrite.setOnClickListener(new View.OnClickListener() {

			@Override
			public void onClick(View v) {
				String name = edtName.getText().toString();
				int age = Integer.parseInt(edtAge.getText().toString());
				editor.putInt(&quot;age&quot;, age);
				editor.putString(&quot;name&quot;, name);

				// 一定要提交
				editor.commit();

			}
		});

	}
}</pre>
<p><br>
<strong>实现效果：</strong></p>
<p>&nbsp;<img src="http://hi.csdn.net/attachment/201111/15/0_1321318852vw5x.gif" alt=""></p>
<p>用File Explorer可以查看在<strong>data/data/com.jiahui.preferences/shared_prefs/</strong>下多了一个XML文件</p>
<p>&nbsp;<img src="http://hi.csdn.net/attachment/201111/15/0_1321318860up4Y.gif" alt=""></p>
<p>将XML文件导出我们可以看到在这个XML文件里存储了以下内容：</p>
<pre class="html" name="code">&lt;?xml version='1.0' encoding='utf-8' standalone='yes' ?&gt;
&lt;map&gt;
&lt;int name=&quot;age&quot; value=&quot;20&quot; /&gt;
&lt;string name=&quot;name&quot;&gt;jiahui&lt;/string&gt;
&lt;/map&gt;

</pre>
<p><strong><span style="color:#ff0000">读写其他应用SharedPreferences</span></strong></p>
<p>在有时的开发过程中，我们可能要访问其他的应用程序的SharedPreferences，要实现这样的功能，我们必须要保证要访问的那个应用程序的SharedPreferences的访问权限要是MODE_WORLD_READABLE，这表明该SharedPreferences可以被其他应用程序读取，如果指定的MODE_WORLD_WRITEABLE表明该SharedPreferences可以被其他应用程序读取写入其实现思路如下</p>
<p>1、&nbsp; 获得其他应用程序对应的Context</p>
<p>2、&nbsp; 通过这个Context得到这个应用程序的SharedPreferences，然后再利用这个SharedPreferences读取内容</p>
<p>以上一例的SharedPreference为例。实现代码：</p>
<pre class="java" name="code">package com.jiahui.preferences2;

import android.app.Activity;
import android.content.Context;
import android.content.SharedPreferences;
import android.content.pm.PackageManager.NameNotFoundException;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;

public class SharedPreferencesDemo02Activity extends Activity {

	private Button btnRead;
	private EditText edtResult;
	Context otherContext;

	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);

		try {
			// 获取其他应用程序的Context
			otherContext = createPackageContext(&quot;com.jiahui.preferences&quot;,
					CONTEXT_IGNORE_SECURITY);
		} catch (NameNotFoundException e) {
			e.printStackTrace();
		}

		edtResult = (EditText) this.findViewById(R.id.edtResult);

		btnRead = (Button) this.findViewById(R.id.btnRead);
		btnRead.setOnClickListener(new View.OnClickListener() {

			@Override
			public void onClick(View v) {
				// 获取其他应用程序的SharedPreferences
				SharedPreferences preferences = otherContext
						.getSharedPreferences(&quot;test&quot;,
								Context.MODE_WORLD_READABLE);

				String name = preferences.getString(&quot;name&quot;, null);
				int age = preferences.getInt(&quot;age&quot;, 0);
				edtResult.setText(&quot;访问的结果为:姓名：&quot; + name + &quot;年龄:&quot; + age);

			}
		});

	}
}</pre>
<p><br>
&nbsp;<strong>实现效果：</strong></p>
<p><img src="http://hi.csdn.net/attachment/201111/15/0_1321318923jU9h.gif" alt=""></p>
<p><strong><span style="color:#ff0000">SharedPreferences的注意事项：</span></strong></p>
<p>编辑完SharedPreferences一定要记得调用Editor的commit()方法，否则不会将数据写入到文件里的。</p>
<p><strong><span style="color:#ff0000">回顾总结：</span></strong></p>
<p>1、&nbsp; 如何得到SharedPreferences</p>
<p>SharedPreferences preferences=getPreferences(“test”,MODE_PRIVATE);</p>
<p>2、&nbsp; 如何编辑SharedPreferences</p>
<p>得到Editor对象实例</p>
<p>SharedPreferences.Editor editor=preferences.editor();</p>
<p>3、&nbsp; SharedPreferences的存储位置</p>
<p><strong><span style="color:#ff0000">/data/data/&lt;package name&gt;/shared_prefs</span></strong></p>
<p>&nbsp;</p>
<p>如需转载引用请注明出处：<a href="http://blog.csdn.net/jiahui524">http://blog.csdn.net/jiahui524</a></p>

</div>

duce Job History Server  |HADOOP_JOB_HISTORYSERVER_OPTS      |