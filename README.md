## Objective

Main objective of this blog post is to give you an idea about how to Share Image and Text on Twitter in Android Using Native Code.

Main objective of this blog post is to give you an idea about how to Share Image and Text on Twitter in Android Using Native Code.

## Step 1 : Share Text and image on Twitter in Android

If you want to share image, score or game link on twitter in android using the native code of java than you have to create an android application in eclipse or any other editor. After that, put the following code in .java file. Export jar file and put it in unity.

So, first create a jar file which contains the native code of sharing data on twitter and then export that file and put it in **Assets >> Plugins >> Android** folder in unity.

```java
packagecom.tag.fb_twitter;
 
importjava.util.ArrayList;
importjava.util.List;
importandroid.content.ComponentName;
importandroid.content.Context;
importandroid.content.Intent;
importandroid.content.pm.PackageManager;
importandroid.content.pm.ResolveInfo;
importandroid.net.Uri;
importandroid.os.Parcelable;
importandroid.util.Log;
importandroid.widget.Toast;
 
publicclassFb_Twitter {
private Context context;
privatestaticFb_Twitterinstance;
privatestaticFb_Twitterinstance()
{
if(instance == null)
{
instance = newFb_Twitter();
}
returninstance;
}
publicFb_Twitter()
{
this.instance = this;
}
publicvoidsetContext(Context context)
{
this.context = context;
}
privatebooleanappInstalledOrNot(String uri) {
PackageManager pm = context.getPackageManager();
booleanapp_installed = false;
try {
pm.getPackageInfo(uri, PackageManager.GET_ACTIVITIES);
app_installed = true;
}
catch (PackageManager.NameNotFoundException e) {
app_installed = false;
}
returnapp_installed;
}
 
publicvoidshowMessage(String msg)
{
Toast.makeText(context, msg, Toast.LENGTH_SHORT).show();
}
publicvoidShareTextOnTwitter(String msg)
{
List<Intent>targetShareIntents=newArrayList<Intent>();
if(appInstalledOrNot("com.twitter.android"))// Check android app is installed or not
{
PackageManagerpackageManager= context.getPackageManager();// Create instance of PackageManager
Intent sendIntent = newIntent(Intent.ACTION_SEND);
sendIntent.setType("text/plain");
//Get List of all activities
List<ResolveInfo>resolveInfoList = packageManager.queryIntentActivities(sendIntent, 0);
for (int j = 0; j <resolveInfoList.size(); j++)
{
ResolveInforesInfo = resolveInfoList.get(j);
String packageName = resInfo.activityInfo.packageName;
//Find twitter app from list
if(packageName.contains("com.twitter.android")){
Intent intent=newIntent();
intent.setComponent(newComponentName(packageName, resInfo.activityInfo.name));//Create Intent with twitter app package
intent.setAction(Intent.ACTION_SEND);
intent.setType("text/plain");
intent.putExtra(Intent.EXTRA_TEXT, msg);// Set message
intent.putExtra(Intent.EXTRA_SUBJECT, "Text Sharing");//set subject
intent.setPackage(packageName);
targetShareIntents.add(intent);
}
 
if(!targetShareIntents.isEmpty()){
Intent chooserIntent=Intent.createChooser(targetShareIntents.remove(0), "Choose app to share");
chooserIntent.putExtra(Intent.EXTRA_INITIAL_INTENTS, targetShareIntents.toArray(newParcelable[]{}));
context.startActivity(chooserIntent);
}else{
System.out.println("Do not Have Intent");
}
}
}
else
showMessage("App Not Installed");
}
publicvoidPostImageOnTwitter(String imageUri, String msg)
{
System.out.println("Cick on FB");
List<Intent>targetShareIntents=newArrayList<Intent>();
if(appInstalledOrNot("com.twitter.android"))
{
System.out.println("App installed");
PackageManagerpackageManager= context.getPackageManager();
Intent sendIntent = newIntent(Intent.ACTION_SEND);
sendIntent.setType("image/*");
List<ResolveInfo>resolveInfoList = packageManager.queryIntentActivities(sendIntent, 0);
for (int j = 0; j <resolveInfoList.size(); j++)
{
ResolveInforesInfo = resolveInfoList.get(j);
String packageName = resInfo.activityInfo.packageName;
Log.i("Package Name", packageName);
if(packageName.contains("com.twitter.android")){
Intent intent=newIntent();
intent.setComponent(newComponentName(packageName, resInfo.activityInfo.name));
intent.setAction(Intent.ACTION_SEND);
intent.setType("image/*");//Set MIME Type
intent.putExtra(Intent.EXTRA_TEXT, msg);
intent.putExtra(Intent.EXTRA_SUBJECT, "Post Image");
Uri screenshotUri = Uri.parse(imageUri);
intent.putExtra(Intent.EXTRA_STREAM, screenshotUri);// Pur Image to intent
 
intent.setPackage(packageName);
targetShareIntents.add(intent);
}
 
if(!targetShareIntents.isEmpty()){
System.out.println("Have Intent");
Intent chooserIntent=Intent.createChooser(targetShareIntents.remove(0), "Choose app to share");
chooserIntent.putExtra(Intent.EXTRA_INITIAL_INTENTS, targetShareIntents.toArray(newParcelable[]{}));
context.startActivity(chooserIntent);
}else{
System.out.println("Do not Have Intent");
}
}
}
else
showMessage("App Not Installed");
}
}
 
if(!targetShareIntents.isEmpty()){
System.out.println("Have Intent");
Intent chooserIntent=Intent.createChooser(targetShareIntents.remove(0), "Choose app to share");
chooserIntent.putExtra(Intent.EXTRA_INITIAL_INTENTS, targetShareIntents.toArray(newParcelable[]{}));
context.startActivity(chooserIntent);
}else{
System.out.println("Do not Have Intent");
}
}
}
else
showMessage("App Not Installed");
}
}
```

## Step 2 : Export jar file

Once you’ve completed your code, export the jar file. Put your jar file into **Assets >> Plugins >> Android** folder. Now, you have to access this java code in your script.

**So, here is the code for that:**

```csharp
AndroidJavaObjectfbTwitterSharing = null;
AndroidJavaObjectactivityContext = null;
 
if (fbTwitterSharing == null)
{
using (AndroidJavaClassactivityClass = newAndroidJavaClass("com.unity3d.player.UnityPlayer"))
{
activityContext = activityClass.GetStatic<AndroidJavaObject>("currentActivity");
}
 
using (AndroidJavaClasspluginClass = newAndroidJavaClass("com.tag.fb_twitter.Fb_Twitter"))
{
if (pluginClass != null)
{
fbTwitterSharing = pluginClass.CallStatic<AndroidJavaObject>("instance");
fbTwitterSharing.Call("setContext", activityContext);
 
//Text Sharing
fbTwitterSharing.Call("ShareTextOnTwitter", "Tweet Something");
 
//Image Sharing with text
fbTwitterSharing.Call("PostImageOnTwitter", ScreenshotHandler.savedImagePath, "Image Sharing");
}
}
}
```

If your project doesn’t contain android manifest file than put new one and add these permissions.

```java
<uses-permissionandroid:name="android.permission.INTERNET" />
<uses-permissionandroid:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permissionandroid:name="android.permission.ACCESS_WIFI_STATE" />
<uses-permissionandroid:name="android.permission.WRITE_EXTERNAL_STORAGE" />
<uses-permissionandroid:name="android.permission.READ_EXTERNAL_STORAGE">
```
