# RxBonjour
Say "Hi!" to RxBonjour, a wrapper around Android's network service discovery functionalities with a support implementation for devices below Jelly Bean, going down all the way to API level 9.

## Download

`RxBonjour` is available on `jcenter()`, and provides compatibility for both RxJava 1.x and 2.x. Depending on your stack, choose **one** of the following:

```groovy
compile "de.mannodermaus:rxbonjour-driver-rxjava:______"
compile "de.mannodermaus:rxbonjour-driver-rxjava2:______"
```

## Discovery

Create a network service discovery request using `RxBonjour.newDiscovery(Context, String)` and subscribe to the returned `Observable`:

```java
RxBonjour.newDiscovery(this, "_http._tcp")
	.subscribe(bonjourEvent -> {
		BonjourService item = bonjourEvent.getService();
		switch (bonjourEvent.getType()) {
			case ADDED:
				// Called when a service was discovered
				break;

			case REMOVED:
				// Called when a service is no longer visible
				break;
		}
	}, error -> {
		// Service discovery failed, for instance
	});
```

## Registration

Create a service to broadcast using `RxBonjour.newBroadcast(Context, String)` and subscribe to the returned `Observable` of the broadcast object:

```java
BonjourBroadcast<?> broadcast = BonjourBroadcast.newBuilder("_http._tcp")
	.name("My Broadcast")
	.port(65335)
	.build();
	
RxBonjour.newBroadcast(this, broadcast)
	.subscribe(bonjourEvent -> {
		// Same as above
	});
```

## Implementations

RxBonjour comes with two implementations for network service discovery. By default, the support implementation is used because of the unreliable state of the `NsdManager` APIs and known bugs with that. If you **really** want to use `NsdManager` on devices running Jelly Bean and up though, you can specify this during the configuration phase:

```java
// If you're feeling real and ready to reboot your device once NsdManager breaks, pass in "true" as the last parameter to use it on supported devices
RxBonjour.newDiscovery(this, "_http._tcp", true)
		.subscribe(bonjourEvent -> {
			// ...
		}, error -> {
			// ...
		});
        
BonjourBroadcast.newBuilder("_http._tcp", true)
    .build();
```

### NsdManager implementation (v16)

On devices running Jelly Bean and up, Android's native Network Service Discovery API, centered around `NsdManager`, can be used.

### Support implementation (v9)

The support implementation utilizes the power of [jmDNS][jmdns] in conjunction with a `WifiManager` multicast lock as its service discovery backbone; because of this, including this library in your application's dependencies automatically adds the following permissions to your `AndroidManifest.xml`, in order to allow jmDNS to do its thing:

```xml
<uses-permission android:name="android.permission.INTERNET"/>
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE"/>
<uses-permission android:name="android.permission.CHANGE_WIFI_MULTICAST_STATE"/>
```

## Migration

If you've been using RxBonjour 0.x before, there are a couple of breaking changes that you need to take into account when upgrading to the latest iteration of the library. Refer to the [Migration to 1.x][migrate-1.x] guide to learn more.

## License

	Copyright 2016 Marcel Schnelle

	Licensed under the Apache License, Version 2.0 (the "License");
	you may not use this file except in compliance with the License.
	You may obtain a copy of the License at

	   http://www.apache.org/licenses/LICENSE-2.0

	Unless required by applicable law or agreed to in writing, software
	distributed under the License is distributed on an "AS IS" BASIS,
	WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
	See the License for the specific language governing permissions and
	limitations under the License.

	
 [jmdns]: https://github.com/openhab/jmdns
 [jit]: https://jitpack.io
	