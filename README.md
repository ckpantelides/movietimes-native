#### Installation

> npm install // install dependencies

> tns run // compile for development

Movie Times app v2
===================

The backend code used to handle queries for cinemas/movies is [here](https://github.com/ckpantelides/list-api)

The backend code used to request the movies' posters - is [here](https://github.com/ckpantelides/movietime-server)

#### A cinema and film listing app built with NativeScript-Vue

The app finds your location using the android geolocation API. It then finds your local cinemas using the [TheList API](https://api.list.co.uk/). You can also search for cinemas by place name - I send the place name to the google location API, and then send the returned coordinates to TheList API. This is all done on the [backend](https://github.com/ckpantelides/list-api). TheList only allows 1000 requests per month on its fee-free platform.

[TheList API](https://api.list.co.uk/) is also used to get the the film listings for each cinema for 7 days. TheList doesn't return the film performances by day - instead it returns each film as a separate object with the week's performances of that particular film as a nested property of the film object. I therefore filter performances by date on the [backend](https://github.com/ckpantelides/list-api) and send back an array of films by date i.e. [0] contains today's films, [1] contains tomorrow's films.  The calls between the TheList API and the backend, and then from the backend to this frontend app, are all managed through axios.

When the movie listings are displayed, the frontend connects via socket.io to a separate [backend](https://github.com/ckpantelides/movietime-server). I send an array of the film names to this backend, which searches [The Movie DB](https://www.themoviedb.org/) for the films' posters, and sends these back to the frontend. (The fact that there are two backends is an artifact of [version 1](https://github.com/ckpantelides/native-movies) of this app, which relied on a different API for the cinema/film queries).

By clicking on an individual film listing, it shows you the movie's description. 

The results of the cinema search are cached using the [application settings](https://docs.nativescript.org/ns-framework-modules/application-settings) module.

#### Code structure

The Cinema component is intially displayed on startup. It get's your location, makes the request to TheList and displays the results. Once the user has selected a cinema from the results, the cinema's id is passed to the MovieTimes component, which has seven child components - one for each day of the week. The MovieTimes component requests the movie times for the week, and once the results are returned, displays the "today" child component. There's a separate component for cinema searches by place name.

As the structure is straightforward, routing is handled manually, and data and events are passed from component to component (rather than using an event bus or Vuex).

#### Notes on development

This is an android port of a [vue web app](https://github.com/ckpantelides/movietimes) I developed. Porting it wasn't too difficult, the component structure is generally the same, although the app has seven days of movie listings instead of three for the website. The app also requests all of the week's film performances in one go, whereas the website requests the performances one day at a time.

NativeScript doesn't use divs, so I used mostly StackLayout and ListView to structure the code.

Another difference is that the "view" doesn't update automatically (for example when the movie posters are received from the backend). To get around this, I gave my list of movies a reference called listView (ref="listView"). I used a watcher to look for changes in the poster images' data, which would fire the following code when the images were received: this.$refs.listView.nativeView.refresh(). NB code that uses "refs" can't be used in functions that fire when the component is loaded or mounted.

The last major difference is that NativeScript doesn't support rotateY. In the web version of the app, when a movie listing is tapped, it flips over to show the movie's description. Without rotateY the flip animation isn't possible.

#### Building the release app

I had to add the following to the node field in webpack.config.js: node: { net: 'empty', tls: 'empty', dns: 'empty' }. 
Google Play warned me that the app only had 32bit code, so I had to add the following to the defaultConfig{} in app.gradle: 
ndk {
  abiFilters.clear()
  abiFilters.addAll(['armeabi-v7a','arm64-v8a'])
}

I built an Android App Bundle using the NativeScript CLI:
```tns build android --release --key-store-path <path-to-your-keystore> --key-store-password <your-key-store-password> --key-store-alias <your-alias-name> --key-store-alias-password <your-alias-password> --aab --copy-to <aab-location>.aab```

The NativeScript ID in the package.json has to be changed from the default first. Although I use Google Play to handle the private key for the app, I still had to use a .p12 file to complete the build above

![img1] ![img2]

[img1]: https://github.com/ckpantelides/native-movies/blob/images/movie-app1.jpg
[img2]: https://github.com/ckpantelides/native-movies/blob/images/movie-app2.jpg

This app (version 2) replaces [version 1](https://github.com/ckpantelides/native-movies) of the app, which relied on a different API and an older version of NativeScript-vue (which couldn't be updated).
