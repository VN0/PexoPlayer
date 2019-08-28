# PexoPlayer

[![AppCenter]()]()

### Specs
[![API](https://img.shields.io/badge/API-15%2B-orange.svg?style=flat)](https://android-arsenal.com/api?level=16)

An audio streaming library based on [ExoPlayer](https://exoplayer.dev) and [Audio App Architecture](https://developer.android.com/guide/topics/media-apps/audio-app/building-an-audio-app)  

## Features

  - Baked with ExoPlayer 
  - Easy media controls
  - Auto notification management

## Download

Use as Gradle dependency

```gradle
allprojects {
 repositories {
  maven {
   url 'https://jitpack.io'
  }
 }
}

dependencies {
 implementation 'com.github.prokash-sarkar:PexoPlayer:v1.0.0-java'
}
```

## Usage

**Required Permissions**

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.FOREGROUND_SERVICE" />

<service android:name="com.ps.pexoplayer.services.MediaService">
     <intent-filter>
          <action android:name="android.media.browse.MediaBrowserService" />
     </intent-filter>
</service>

<receiver android:name="androidx.media.session.MediaButtonReceiver">
      <intent-filter>
           <action android:name="android.intent.action.MEDIA_BUTTON" />
      </intent-filter>
</receiver>
```

**Initialize the** ```PexoPlayerManager```

```Java
public PexoPlayerManager pexoPlayerManager = new PexoPlayerManager(context);
```

In your activity, notify the ```PexoPlayerManager``` for ```onConfigurationChanged()```, ```onResume()``` and ```onDestroy()``` lifecycle state changes

```Java
// Call inside "onConfigurationChanged()"
pexoPlayerManager.setConfigurationChanged(newConfig);

// Call inside "onResume()"
pexoPlayerManager.subscribeCallBack();

// Call inside "onDestroy()"
pexoPlayerManager.unSubscribeCallBack();
```

**Initialize a Playlist**

PexoPlayer takes a custom object wrapped in ```PexoMediaMetadata```. You need to build the object with your own data e.g. contentId, artistName, playUrl, etc. You can also pass your main list to retrieve it later from the PexoPlayer instance.

```java

List<PexoMediaMetadata> mediaItemList = new ArrayList<PexoMediaMetadata>();

PexoMediaMetadata pexoMediaMetadata = new PexoMediaMetadata(
                        data.getContentID(),
                        data.getArtistname(),
                        data.getTitle(),
                        data.getPlayUrl(),
                        data.getLabelname(),
                        data.getReleaseDate(),
                        data.getImage())
                        
mMediaItems.add(pexoMediaMetadata);

// playlistId must be unique
// orignalSongList is optional, you can pass it for future reference
pexoPlayerManager.setupNewPlaylist(playlistId, orignalSongList, mediaItemList, position);
```

**Start playback**

```java
pexoPlayerManager.startPlayback();
```

**Toggle playback state**

> This will perform the opposite operation for current playback state. e.g. If playing it will stop, If stopped it will start playing
>

```java
pexoPlayerManager.onTogglePlayPause();
```

**Play** 

```java
pexoPlayerManager.onPlay();
```

**Pause**

```java
pexoPlayerManager.onPause();
```

**Skip to next**

```java
pexoPlayerManager.skipToNextTrack()
```

**Skip to previous**

```java
pexoPlayerManager.skipToPreviousTrack()
```

**Skip to a position**

```java
pexoPlayerManager.skipToTrack(position)
```

**Repeat a track**

```java
pexoPlayerManager.repeatTrack()
```

**Shuffle a track**

```java
pexoPlayerManager.shuffleTrack()
```

**Media position seeking**

> PexoPlayer comes with a built in ```SeekBar``` which can perform the media seeking by itself. The following widget will seek the media position in player automatically. Make sure to connect and disconnect the ```SeekBar``` in PexoPlayer's ```onPlayerMediaControllerConnected()``` and ```onControllerDisconnect()``` method

```xml
<com.ps.pexoplayer.widget.MediaSeekBar    
 android:id="@+id/seek_bar"    
 android:layout_width="match_parent"    
 android:layout_height="wrap_content"                                     
</com.ps.pexoplayer.widget.MediaSeekBar>
```

**Set the event listener callbacks**

```java
 pexoPlayerManager.setPexoEventListener(this);
```

 **This will override the following callbacks**

 ```java
    /*Media controller is connected*/
    @Override
    public void onPlayerMediaControllerConnected(MediaControllerCompat mediaController) {
        // Required if you want to use the Pexo MediaSeekBar
        seekBar.setMediaController(mediaController);
    }

    /*A track has been changed*/
    @Override
    public void onPlayerMetadataChanged(MediaMetadataCompat metadata) {
    }
    
    /*Playback state has changed e.g. play, pause, stop*/
    @Override
    public void onPlayerPlaybackStateChanged(PlaybackStateCompat state) {
    }

    /*Controller has been disconnected*/
    @Override
    public void onControllerDisconnect() {
        // Required if you want to use the Pexo MediaSeekBar
        seekBar.disconnectController();
    }
    
    /*Update the seekbar's secondary position here*/
    @Override
    public void updatePlayerBuffer(int progress) {
        seekBarMusic.setSecondaryProgress(progress);
    }

    /*Update the seekbar position here*/
    @Override
    public void updatePlayerSeekBar(int progress, int max) {
    }

    /*Shuffle status has changed*/
    @Override
    public void updateShuffle(int state) {
    }

    /*Repeat status has changed*/
    @Override
    public void updateRepeat(int state) {
    }

    /*You can reload a previously played playlist by passing it here
    If you can't supply the previous list, pass an empty one*/
    @Override
    public void prepareLastPlayedMedia() {
      final List<PexoMediaMetadata> mediaItems = new ArrayList<>();
      // Prepare the list item to resume the player
      pexoPlayerManager.onFinishedGettingPreviousSessionData(mediaItems);
    }
 ```

**Notification Management**

> PexoPlayer creates and handles the notification by itself after defining the proper media data object to the Playlist. It performs the play, pause and track change events from notification by itself. Callbacks are also provided by an interface. You only need to assign an activity for the pending intent
>

```java
pexoPlayerManager.setPendingIntentClass(MainActivity.class);
```

## Acknowledgement

This project is re-written based on the ground up of streaming audio example by [@mitchtabian](https://github.com/mitchtabian)

License
----

**Free Software, Ta-Da!**
