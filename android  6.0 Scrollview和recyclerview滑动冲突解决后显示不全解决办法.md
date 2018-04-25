android  6.0 Scrollview和recyclerview滑动冲突解决后显示不全解决办法

解决方法，在使用到recycleview的布局外用一个RelativeLayout，关键属性`android:descendantFocusability="blocksDescendants"`

<RelativeLayout
android:layout_width="match_parent"                android:layout_height="wrap_content"                android:descendantFocusability="blocksDescendants">                <android.support.v7.widget.RecyclerView                    android:background="@color/white"                    android:id="@+id/recyclerView_single"                    android:layout_width="match_parent"                    android:layout_height="match_parent" />           
</RelativeLayout>


