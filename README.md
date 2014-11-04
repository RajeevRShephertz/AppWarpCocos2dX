AppWarpCocos2dX
===============

### New API added in the SDK
```
void recoverConnection(int sessionId, std::string _userName)
        
int getSessionId()
```

If you want to recover session, when app was killed in between the session and opened again, then you need to call recoverConnection(sessionId, userName).

As the SDK does not save the sessionID and userName when app was killed, so you have to save the sessionId and user name in local database once in the **onConnectDone** callback.

For example:

```
void HelloWorld::onConnectDone(int res)
{
    if (res==AppWarp::ResultCode::success)
    {
        printf("\nonConnectDone .. SUCCESS..session=%d\n",AppWarp::AppWarpSessionID);
        //Getting session ID
        int sessionId = AppWarp::Client::getInstance()->getSessionId();
        
        //Saving Session ID in local database
        UserDefault::getInstance()->setStringForKey("userName", userName);
        UserDefault::getInstance()->setIntegerForKey("sessionId", sessionId);
    }
}
```

When app opened and you want to recover the previously closed session, then Fetch the session ID and user name from your local database and call recoverConnection using these params. For example:

```
void HelloWorld::recover()
{
    printf("\nHelloWorld::recover");
    AppWarp::Client *warpClientRef;
    AppWarp::Client::initialize(APPWARP_APP_KEY,APPWARP_SECRET_KEY);
    warpClientRef = AppWarp::Client::getInstance();
    warpClientRef->setRecoveryAllowance(60);
    warpClientRef->setConnectionRequestListener(this);
    warpClientRef->setNotificationListener(this);
    warpClientRef->setRoomRequestListener(this);
    warpClientRef->setZoneRequestListener(this);
    warpClientRef->setTurnBasedRoomRequestListener(this);
    
    int sessionId = UserDefault::getInstance()->getIntegerForKey("sessionId");
    string userName = UserDefault::getInstance()->getStringForKey("userName");
    AppWarp::Client::getInstance()->recoverConnection(sessionId,userName);
}
```
__Note:-__ The session can only be recovered if app is opened within the recoveryAllowance time set earlier. If you are calling recoverConnection and recoveryAllowance time is over then you will get __auth_error__ in onConnectDone callback.


