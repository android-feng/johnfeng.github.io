---
layout: post 
title: "Android RelativeLayout's Bug -- for devices' API lower than 17"
date: 2015-10-12 18:19:04 +0800 
comments: true 
share: true
categories: android bug relativeLayout 17 layout
---

##Problem

This afternoon, When I am implementing a simple ListView's item view, I found a really wired problem. First, the layout is shown below:

{% codeblock lang:xml item_list_notification %}

<?xml version="1.0" encoding="utf-8"?>

<RelativeLayout android:id="@+id/notification_layout"
	xmlns:android="http://schemas.android.com/apk/res/android"
	android:layout_width="match_parent"
	android:layout_height="match_parent"
	android:background="?android:attr/selectableItemBackground"
	android:clickable="true"
	android:descendantFocusability="blocksDescendants"
	android:gravity="center_vertical"
	android:paddingBottom="15dp"
	android:paddingLeft="14dp"
	android:paddingRight="14dp"
	android:paddingTop="15dp"
	>

	<LinearLayout
	android:id="@+id/notification_content"
	android:layout_width="match_parent"
	android:layout_height="match_parent"
	android:orientation="vertical"
	>

		<TextView
		android:id="@+id/notification_text"
		style="@style/Text.Frodo.P4"
		android:layout_width="wrap_content"
		android:layout_height="wrap_content"
		android:singleLine="false"
		android:textColor="@color/text_gray"
		/>

		<TextView
		android:id="@+id/notification_time"
		style="@style/Text.Frodo.P4"
		android:layout_width="wrap_content"
		android:layout_height="wrap_content"
		android:singleLine="true"
		android:textColor="@color/text_gray_light"
		/>

	</LinearLayout>

</RelativeLayout>

{% endcodeblock %}

<!-- more -->

The problem is : when runs on devices with API over 18, the LinearLayout shown in right places. But when runs on below 17, all padding properties of the LinearLayout seem do not work at all, with all subviews just shown at left-top side without any space to bounds.

##Fix

After long time searching , I found a piece of words on [developer.android.com](http://developer.android.com/reference/android/widget/RelativeLayout.html) , which says:

> Note: In platform version 17 and lower, RelativeLayout was affected by a measurement bug that could cause child views to be measured with incorrect MeasureSpec values. (See MeasureSpec.makeMeasureSpec for more details.) This was triggered when a RelativeLayout container was placed in a scrolling container, such as a ScrollView or HorizontalScrollView. If a custom view not equipped to properly measure with the MeasureSpec mode UNSPECIFIED was placed in a RelativeLayout, this would silently work anyway as RelativeLayout would pass a very large AT_MOST MeasureSpec instead.

<br>

####And something more is:
[View.makeMeasureSpec](http://developer.android.com/reference/android/view/View.MeasureSpec.html#makeMeasureSpec(int, int))

It seems that this bug only affects RelativeLayout's child views, when RelativeLayout is inside of a scrolling container , like a listview I am using. Thus , the result is quite simple , <strong>all measuring properties of RelativeLayout's child views should best move to RelativeLayout itself.</strong>  Like my list item , it should be like:

{% codeblock lang:xml fixed : item_list_notification %}
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout android:id="@+id/notification_layout"
	xmlns:android="http://schemas.android.com/apk/res/android"
	android:layout_width="match_parent"
	android:layout_height="match_parent"
	android:background="?android:attr/selectableItemBackground"
	android:clickable="true"
	android:descendantFocusability="blocksDescendants"
	android:gravity="center_vertical"
	android:paddingBottom="15dp"
	android:paddingLeft="14dp"
	android:paddingRight="14dp"
	android:paddingTop="15dp"
	>

		<LinearLayout
		android:id="@+id/notification_content"
		android:layout_width="match_parent"
		android:layout_height="match_parent"
		android:orientation="vertical"
		>

			<TextView
			android:id="@+id/notification_text"
			style="@style/Text.Frodo.P4"
			android:layout_width="wrap_content"
			android:layout_height="wrap_content"
			android:singleLine="false"
			android:textColor="@color/text_gray"
			/>

			<TextView
			android:id="@+id/notification_time"
			style="@style/Text.Frodo.P4"
			android:layout_width="wrap_content"
			android:layout_height="wrap_content"
			android:singleLine="true"
			android:textColor="@color/text_gray_light"
			/>
		</LinearLayout>
</RelativeLayout>
{% endcodeblock %}

all paddings have been moved into RelativeLayout from LinearLayout.

P.S. If your apps' lowest support API levels are above 18, you don't have to worry about this problem.

Peace!
