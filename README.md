rubotoQuickStart
================

This is a problem program trying to make use of RubotoCore without installation


How to replicate my problem?

1. Download the project and install in your phone (both through eclipse or command-line is ok).
2. if your phone have the Ruboto-core installed, the project should be working fine.  If your phone don't have Ruboto-core installed the app would ask you to download and install (just like the First Ruboto demo program behaves because it is the ruboto demo program).
3. If you download the RubotoCore.apk in this repository, or rename the official ruboto-core.apk to RubotoCore.apk, and then put the apk file in the sdcard's root directory. And then restart the quick_start app (without ruboto-core installed), you will get the problem.


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
      tmpDir.getAbsolutePath(),null,JRubyAdapter.class.getClass().getClassLoader());
      
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
  
And this is the exception I got:

    02-19 11:53:50.719: E/AndroidRuntime(25373): FATAL EXCEPTION: ScriptLoader for  Java::ComChenhaoExampleQuick_startQuickStartActivity
    02-19 11:53:50.719: E/AndroidRuntime(25373): org.jruby.embed.EvalFailedException: (NameError) cannot load Java class com.chenhao.example.quick_start.R
    02-19 11:53:50.719: E/AndroidRuntime(25373): 	at org.jruby.embed.internal.EmbedEvalUnitImpl.run(EmbedEvalUnitImpl.java:133)
    02-19 11:53:50.719: E/AndroidRuntime(25373): 	at org.jruby.embed.ScriptingContainer.runUnit(ScriptingContainer.java:1264)
    02-19 11:53:50.719: E/AndroidRuntime(25373): 	at org.jruby.embed.ScriptingContainer.runScriptlet(ScriptingContainer.java:1257)
    02-19 11:53:50.719: E/AndroidRuntime(25373): 	at java.lang.reflect.Method.invokeNative(Native Method)
    02-19 11:53:50.719: E/AndroidRuntime(25373): 	at java.lang.reflect.Method.invoke(Method.java:511)
    02-19 11:53:50.719: E/AndroidRuntime(25373): 	at org.ruboto.JRubyAdapter.runScriptlet(JRubyAdapter.java:104)
    02-19 11:53:50.719: E/AndroidRuntime(25373): 	at org.ruboto.ScriptLoader$1.run(ScriptLoader.java:79)
    02-19 11:53:50.719: E/AndroidRuntime(25373): 	at java.lang.Thread.run(Thread.java:856)
    02-19 11:53:50.719: E/AndroidRuntime(25373): Caused by: org.jruby.exceptions.RaiseException: (NameError) cannot load Java class com.chenhao.example.quick_start.R
    02-19 11:53:50.719: E/AndroidRuntime(25373): 	at org.jruby.javasupport.JavaClass.for_name(org/jruby/javasupport/JavaClass.java:1242)
    02-19 11:53:50.719: E/AndroidRuntime(25373): 	at org.jruby.javasupport.JavaUtilities.get_proxy_class(org/jruby/javasupport/JavaUtilities.java:34)
    02-19 11:53:50.719: E/AndroidRuntime(25373): 	at RUBY.java_import(file:/storage/sdcard0/org.ruboto.core-1.apk!/jruby/java/core_ext/object.rb:26)
    02-19 11:53:50.719: E/AndroidRuntime(25373): 	at org.jruby.RubyArray.map(org/jruby/RubyArray.java:2417)
    02-19 11:53:50.719: E/AndroidRuntime(25373): 	at RUBY.java_import(file:/storage/sdcard0/org.ruboto.core-1.apk!/jruby/java/core_ext/object.rb:22)
    02-19 11:53:50.719: E/AndroidRuntime(25373): 	at RUBY.Ruboto(file:/storage/sdcard0/org.ruboto.core-1.apk!/ruboto/package.rb:4)
    02-19 11:53:50.719: E/AndroidRuntime(25373): 	at RUBY.(root)(file:/storage/sdcard0/org.ruboto.core-1.apk!/ruboto/package.rb:3)
    02-19 11:53:50.719: E/AndroidRuntime(25373): 	at org.jruby.RubyKernel.require(org/jruby/RubyKernel.java:1054)
    02-19 11:53:50.719: E/AndroidRuntime(25373): 	at RUBY.(root)(file:/storage/sdcard0/org.ruboto.core-1.apk!/ruboto/activity.rb:1)
    02-19 11:53:50.719: E/AndroidRuntime(25373): 	at org.jruby.RubyKernel.require(org/jruby/RubyKernel.java:1054)
    02-19 11:53:50.719: E/AndroidRuntime(25373): 	at RUBY.(root)(file:/storage/sdcard0/org.ruboto.core-1.apk!/ruboto/activity.rb:2)
    02-19 11:53:50.719: E/AndroidRuntime(25373): 	at org.jruby.RubyKernel.require(org/jruby/RubyKernel.java:1054)
    02-19 11:53:50.719: E/AndroidRuntime(25373): 	at RUBY.(root)(file:/storage/sdcard0/org.ruboto.core-1.apk!/ruboto/widget.rb:1)
  
Anyone who knows how to solve this would be welcomed! Thanks!!
