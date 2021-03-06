# PassCodeLockSample
![](art/passcodelock.gif)

## Description
This is a sample program of *passcode lock* looks like iOS.  
The core idea of managing application lifecycle is based on [this slide](http://www.slideshare.net/heki1224/android-45736528) by Mr.[@heki1224](https://twitter.com/heki1224)
****
## Usage
***API 15~~***  
You need implement Application.ActivityLifecycleCallbacks to your Application class.  
In order to manage Activity stack appropriately, please start your Activity after **activityStack.add(activity.hashCode());**.
```java
public class MyApplication extends Application implements Application.ActivityLifecycleCallbacks {
    private HashSet<Integer> activityStack = new HashSet<>();

    @Override
    public void onCreate() {
        super.onCreate();
        registerActivityLifecycleCallbacks(this);
    }

    @Override
    public void onTerminate() {
        unregisterActivityLifecycleCallbacks(this);
        super.onTerminate();
    }
    
    @Override
    public void onActivityStarted(Activity activity) {
        boolean isForeground = activityStack.size() == 0;
        activityStack.add(activity.hashCode());
        if (isForeground) activity.startActivity(/*Activity for entering passcode*/);
    }

    @Override
    public void onActivityStopped(Activity activity) {
        activityStack.remove(activity.hashCode());
        boolean isBackground = activityStack.size() == 0;
    }
    ~~~~
}
```
  
***Activity for entering passcode***  
```java
    @Override
    protected void onPause() {
        super.onPause();
        finish(); //remove this Activity's stack
    }

    @Override
    public boolean onKeyDown(int keyCode, KeyEvent event) {
        if (keyCode == KeyEvent.KEYCODE_BACK) {
            moveTaskToBack(true); //Application will be background without regard for Activity stack
            return true;
        }
        return super.onKeyDown(keyCode, event);
    }
```
## License

```
Copyright 2016 Takukya Endo

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

   http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```
