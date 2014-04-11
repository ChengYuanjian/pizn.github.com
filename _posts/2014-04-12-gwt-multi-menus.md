---
layout:         post
title:         使用原生GWT生成多级菜单
description: 使用GWT原生组件来生成多级菜单的具体实例，基于递归。
keywords: GWT,Java
category: GWT
---

###构造数据源

本实例数据源采用List<AppHierarchy>的形式，XML、Json也可，只需要根据数据结构修改解析方法`findChildren`即可。

*AppHierarchy.java*

<pre><code>
public class AppHierarchy implements java.io.Serializable {

	public String mDisplayName;//菜单名
	public String mIconFile;//图标路径
	public String mAppId;//父菜单id
	public String mParentAppId;//当前菜单id
	
	...setter,getter略过
	
}
</code></pre>


###根据菜单id查询所有子菜单集合

<pre><code>
private List<AppHierarchy> findChildren(String parentId, List<AppHierarchy> all) {
		List<AppHierarchy> result = new ArrayList<AppHierarchy>();
		for (AppHierarchy a : all) {
			if (a.getMparentappid().equals(parentId)) {
				result.add(a);
			}
		}
		return result;
}
</code></pre>

###根据数据源构造菜单

<pre><code>
public static final String			SEPARATOR	= "SEPARATOR";//分割线

private void createMenu(AppHierarchy parent, List<AppHierarchy> all, MenuItem parentItem) {
		String pid = "0";
		if (parent != null) {
			pid = parent.getMappid();
		}
		List<AppHierarchy> children = findChildren(pid, all);
		if (children.size() <= 0) {
			return;
		}
		MenuBar menu = new MenuBar(true);

		for (final AppHierarchy app : children) {
			if (app.getMdisplayname().equals(SEPARATOR)) {
				menu.addSeparator();
			} else {
				MenuItem item = new MenuItem(app.getMdisplayname(), createMenuCommand(parent, app));
				menu.addItem(item);
				createMenu(app, all, item);
			}

		}
		if (parentItem != null) {
			parentItem.setSubMenu(menu);
		}
	}
	
</code></pre>

###添加点击菜单触发事件处理

<pre><code>
private ScheduledCommand createMenuCommand(final AppHierarchy parentApp, final AppHierarchy app) {
		return new ScheduledCommand() {

			@Override
			public void execute() {
        //TODO
			}
		};
	}
</code></pre>

###调用方法生成菜单

<pre><code>
MenuBar mBar = new MenuBar(true);
mBar.setAutoOpen(true);
mBar.setAnimationEnabled(true);

MenuItem rootItem = new MenuItem("Root", null);
mBar.addItem(rootItem);
List<AppHierarchy> datas;//此处数据源可自由构造
createMenu(null, datas, rootItem);

</code></pre>
