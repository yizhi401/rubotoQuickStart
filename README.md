rubotoQuickStart
================

This is a program trying to make use of RubotoCore without installation.


1. Download the project and install in your phone (both through eclipse or command-line is ok).
2. if your phone have the Ruboto-core installed, the project should be working fine.  If your phone don't have Ruboto-core installed the app would ask you to download and install (just like the First Ruboto demo program behaves because it is the ruboto demo program).

Above is the general behavior that how Ruboto_Core works. 


But if you don't want to install the RubotoCore.apk while still make your ruboto app runs well, here is the solution:

1. Download the official ruboto-core.apk and rename it to RubotoCore.apk, and then put the apk file in the sdcard's root directory (or whatever directory you want, but in this example I made it the SDcard's root directory). 
2. Then restart the quick_start app (without ruboto-core installed), everything goes fine!.




This project is the demo project generated automatically using command

    ruboto gen app --package com.chenhao.example.quick_start

The only file that I modified is the org/ruboto/JRubyAdapter.java

    line 198...
    classLoader = new PathClassLoader(apkName,
    JRubyAdapter.class.getClassLoader());
    } catch (PackageManager.NameNotFoundException e2) {
                  System.out.println("JRuby not found in local APK:");
                  e1.printStackTrace();
                  System.out.println("JRuby not found in platform APK:");
                  e2.printStackTrace();
                  
    //!!!!!!What I modified begins here!!!!!!!!!!!!!!
    try {
      String libPath = Environment.getExternalStorageDirectory()+ "/RubotoCore.apk";
      File tmpDir = appContext.getDir("tempdex", 0);
      classLoader = new DexClassLoader(libPath,
      tmpDir.getAbsolutePath(),null,JRubyAdapter.class.getClassLoader());
      
      apkName = Environment.getExternalStorageDirectory()
                            + "/RubotoCore.apk";
                    // apkName="/data/app/org.ruboto.core-1.apk";
                    RUBOTO_CORE_VERSION_NAME = "0.6.0";
                    // classLoader = new PathClassLoader(apkName,
                    // JRubyAdapter.class.getClassLoader());
                } catch (Exception e3) {
                    android.util.Log.i("mInfo",
                            "JRuby not found in SDcard:");
                    e3.printStackTrace();
                    return false;
                }
            }
      try {
                scriptingContainerClass = Class.forName(
                        "org.jruby.embed.ScriptingContainer", true,
                        classLoader);
            } catch (ClassNotFoundException e) {
                // FIXME(uwe): ScriptingContainer not found in the platform
                // APK...
                e.printStackTrace();
                return false;
            }
        }
      //!!!!!!!!!!!!!!Modification ends here!!!!!!!!!!!!!!!!!!!!!!!!!
    try {
      // ////////////////////////////////
      //
      // Set jruby.home
      //
      ...........others are the same
  

Updates:

If you try to use the PathClassLoader instead of DexClassLoader, it will also worked. I tried this before but failed because the app can't load the classes.dex in the RubotoCore.apk.

                apkName = Environment.getExternalStorageDirectory()
                        + "/RubotoCore.apk";
                RUBOTO_CORE_VERSION_NAME = "0.6.0";
                classLoader = new PathClassLoader(apkName,JRubyAdapter.class.getClassLoader());

But when I followed this thread in StackOverFlow

http://stackoverflow.com/questions/11453614/how-can-i-load-a-jar-file-dynamically-in-an-android-application-4-0-3

I change the /data/dalvik-cache's access control to 777
    su chmod 777 /data/dalvik-cache

And then relunch, It works!  Anyway, you cann't change the user's /data/dalvik-cache into mod 777 right? So using DexClassLoader is ok.
