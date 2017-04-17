### 创建和java以及res同级别的文件夹assets
### 在assets下建立一个fonts文件夹
### 将.ttf格式的字体格式文件放入fonts文件夹下

### 自定义View继承于TextView
> EditText加入字体与TextView相同，继承EditText
#
    /** 
     * 修改字体 
     * 
     */  
     public class MyTextView extends TextView  {  
  
        public MyTextView(Context context) {  
           super(context);  
        }  
  
        public MyTextView(Context context, AttributeSet attrs){  
           super(context, attrs);  
           changeTypeFace(context, attrs);  
        }  
  
    /** 
     * 改变字体类型 
     * @param context 
     * @param attrs 
     */  
    private void changeTypeFace(Context context, AttributeSet attrs){  
        if (attrs != null)  
        {  
            //TypedArray a = context.obtainStyledAttributes(attrs,  
            //R.styleable.TextView_Typefaces);  
            //tf = a.getInt(R.styleable.TextView_Typefaces_tf, tf);  
            Typeface mtf = Typeface.createFromAsset(context.getAssets(),  
                    "fonts/fileName.ttf");  
            super.setTypeface(mtf);  
        }  
    }  
}  