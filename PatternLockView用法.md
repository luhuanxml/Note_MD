# PatternLockView用法

## Gradle

```
dependencies {
   // other dependencies here
    compile 'com.andrognito.patternlockview:patternlockview:1.0.0'    
   // Optional, for RxJava2 adapter    
    compile 'com.andrognito.patternlockview:patternlockview-reactive:1.0.0'
}
```

## Usage

### Step 1

###### XML layout 文件中引用

```
<com.andrognito.patternlockview.PatternLockView
        android:id="@+id/pattern_lock_view"
        android:layout_width="280dp"
        android:layout_height="280dp"/>
```

## Step 2

###### 设置PatternLockView属性

- 在XML中快速简单的设置属性

```
 app:dotCount="3"                          
 // Change the no.of dots in a row (or column)  设置行和列的数目
 
  app:dotNormalSize="12dp"                
  // Change the size of the dots in normal state  设置每个点没有选中时的大小
  
  app:dotSelectedSize="24dp"           
  // Change the size of the dots in selected state  设置每个点被选中时的大小
  
  app:pathWidth="4dp"                         
  // Change the width of the path  滑动的线条路径效果的宽度
  
  app:aspectRatioEnabled="true"    
  // Set if the view should respect custom aspect ratio  能否自定义视图的权限
  
  app:aspectRatio="square"        
  // Set between "square", "width_bias", "height_bias"
  
  app:normalStateColor="@color/white" 
  // Set the color of the pattern view in normal state  没有选中普通的时候显示的颜色
  
  app:correctStateColor="@color/primary"    
  //不能当作正确的时候的颜色提前设置，要当作滑动结束后，通过判断是否正确来设置他的颜色
  // Set the color of the pattern view in correct state  选中滑动正确的时候显示的颜色
  
  //  app:wrongStateColor="@color/pomegranate" 
  // 经测试此方法无效 
  // Set the color of the pattern view in error state    选中滑动错误的时候显示的颜色
  
  app:dotAnimationDuration="200"                          
  // Change the duration of the animating dots  改变每个点选中到离开点复原动画的持续时间
  
  app:pathEndAnimationDuration="100"                      
  // Change the duration of the path end animaiton  改变滑动线条靠近下一个点的动画时间
```

- Java方法设置属性
```
mPatternLockView.setViewMode(PatternLockView.PatternViewMode.CORRECT);       
// Set the current viee more 

mPatternLockView.setInStealthMode(true);                                     
// Set the pattern in stealth mode (pattern drawing is hidden)

mPatternLockView.setTactileFeedbackEnabled(true);                            
// Enables vibration feedback when the pattern is drawn

mPatternLockView.setInputEnabled(false);                                    
// Disables any input from the pattern lock view completely

mPatternLockView.setDotCount(3);
mPatternLockView.setDotNormalSize((int) ResourceUtils.getDimensionInPx(this, R.dimen.pattern_lock_dot_size));
mPatternLockView.setDotSelectedSize((int) ResourceUtils.getDimensionInPx(this, R.dimen.pattern_lock_dot_selected_size));
mPatternLockView.setPathWidth((int) ResourceUtils.getDimensionInPx(this, R.dimen.pattern_lock_path_width));
mPatternLockView.setAspectRatioEnabled(true);
mPatternLockView.setAspectRatio(PatternLockView.AspectRatio.ASPECT_RATIO_HEIGHT_BIAS); 
mPatternLockView.setNormalStateColor(ResourceUtils.getColor(this, R.color.white));
mPatternLockView.setCorrectStateColor(ResourceUtils.getColor(this, R.color.primary));
mPatternLockView.setWrongStateColor(ResourceUtils.getColor(this, R.color.pomegranate));
mPatternLockView.setDotAnimationDuration(150);
mPatternLockView.setPathEndAnimationDuration(100);
mPatternLockView.clearPattern(); //滑动结束后清空
```

## Step 3

###### 设置监听

- 普通方法
```
mPatternLockView = (PatternLockView) findViewById(R.id.pattern_lock_view);
mPatternLockView.addPatternLockListener(mPatternLockViewListener);
private PatternLockViewListener mPatternLockViewListener = new PatternLockViewListener() {
        @Override
        public void onStarted() {
            Log.d(getClass().getName(), "Pattern drawing started");
        }

        @Override
        public void onProgress(List<PatternLockView.Dot> progressPattern) {
            Log.d(getClass().getName(), "Pattern progress: " +
                    PatternLockUtils.patternToString(mPatternLockView, progressPattern));
        }

        @Override
        public void onComplete(List<PatternLockView.Dot> pattern) {
            Log.d(getClass().getName(), "Pattern complete: " +
                    PatternLockUtils.patternToString(mPatternLockView, pattern));
        }

        @Override
        public void onCleared() {
            Log.d(getClass().getName(), "Pattern has been cleared");
        }
    };
```

###### 如果要放弃解锁，或者不需要监听
```
removePatternLockListener(mPatternLockViewListener);
```
- 支持Rxjava2
```
RxPatternLockView.patternChanges(mPatternLockView)
       .subscribe(new Consumer<PatternLockCompoundEvent>() {
             @Override
             public void accept(PatternLockCompoundEvent event) throws Exception {
                if (event.getEventType() == PatternLockCompoundEvent.EventType.PATTERN_STARTED) {
                     Log.d(getClass().getName(), "Pattern drawing started");
                } else if (event.getEventType() == PatternLockCompoundEvent.EventType.PATTERN_PROGRESS) {
                     Log.d(getClass().getName(), "Pattern progress: " +
               PatternLockUtils.patternToString(mPatternLockView, event.getPattern()));
               } else if (event.getEventType() == PatternLockCompoundEvent.EventType.PATTERN_COMPLETE) {
                    Log.d(getClass().getName(), "Pattern complete: " +
               PatternLockUtils.patternToString(mPatternLockView, event.getPattern()));
               } else if (event.getEventType() == PatternLockCompoundEvent.EventType.PATTERN_CLEARED) {
               Log.d(getClass().getName(), "Pattern has been cleared");
               }
            }
      });
```