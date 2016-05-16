# AndroidDataStore
SelectMemberActivity.java中：initInnerFrameManager


SmartHarewareActivity中使用：
mParams.putParcelableArrayList(FriendChooser.RESULT_BUDDIES_SELECTED, mFriendList);将mFriendList序列化
在DeviceAuthorityChooser中：
mFriendList = mParams.getParcelableArrayList(FriendChooser.RESULT_BUDDIES_SELECTED);反序列化
现在要把mFriendList存储到数据库中

###################################目前的做法######################################
 protected void startDeviceDeleteFriendChooserActivity() {
        Intent intent = new Intent();
        if (mFriendList != null) {
            mParams.putParcelableArrayList(FriendChooser.RESULT_BUDDIES_SELECTED, mFriendList);
        }
        intent.putExtra(Constants.KEY_PARAMS, mParams);
        intent.putExtra(Constants.KEY_ACTION, Constants.ACTION_STORY);
        SmartDevicePluginLoader.getInstance().launchPluginActivityForResult(this, app, app.getAccount(), intent, "com.tencent.device.activities.DeviceDeleteFriendChooserActivity",
                REQUEST_CODE_DELETE_FRIEND_CHOOSER, null, SmartDevicePluginProxyActivity.class);
        SmartDeviceReport.report(mApp, mDeviceInfo.din, SmartDeviceReport.ActionName.Usr_Share_AddDel, 2, 0, mProductId);
    }

    
    
    建立DeviceFriendDBHelper,其中建立数据表deviceFriendList。
    
    建立DeviceFriendDBProvider，用来操作数据表。
    
    SmartHardwareActivity中，现在getDeviceUsersList()获取friendlist之后，将friendlist存放成数据库。insertFriendToDatabase
  
    修改DeviceAuthorityChooser,添加ContentResolver。
    
    增加好友--startDeviceShareFriendChooser-startActivityForResult(...,REQUEST_CODE_SHARE_FRIEND_CHOOSER)-->onActivityResult
    -PostCgiProxy(...,mSendBindListObserver)-->修改mFriendList和insertFriendToSharedPreferences
    删除好友--startDeviceDeleteFriendChooserActivity-REQUEST_CODE_DELETE_FRIEND_CHOOSER--> mFriendList = unDeleteFriends
    
    首次进入--OnDeviceOperationRst//设备绑定，解除绑定操作回调-进入DeviceOp_FetchOnlineStatus-->进入DeviceResultHandler广播
    好友接受绑定--OnDeviceOperationRst--DeviceOp_SomebodyJoin
    好友解除绑定--OnDeviceOperationRst--DeviceOp_SomebodyQuit
    
