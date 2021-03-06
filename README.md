Meet-The-Turtle
===============
![screenshot from 2015-01-13 02 09 46](https://cloud.githubusercontent.com/assets/6149085/5714462/6a706f0c-9ac9-11e4-9dc1-be9af4bcc0c4.png)

http://armellini13.github.io/Meet-The-Turtle/
# Tortuga #
Tortuga is an AngularJS web app music player that allows you to listen to your favorite songs, create your own playlists and get the best music recommendations. Benefiting from the LastFM, Spotify, SoundCloud, YouTube and The Eco Nest APIs, Tortuga believes in taking the best from each service.

Using angular's material module, Tortuga's UI is implemented following Google's Material Design guidelines. Due to angular-material still being a module in beta phase, Tortuga uses bootstrap to complement functionalities yet not found in this module. 

# Contributors #
- Agustín Armellini Fischer: armellini13@gmail.com
- Arnau Cebrián: arnaucda@gmail.com

# Libraries used #
Tortuga is an AngularJS app, benefiting from the use of third-party modules like `angular-material`, `angular-aria`, `angular-animate`, `hammerJS`, `bootstrap` and `angular-ui` for frontend design and other modules like `angular-resource`, `angular-touch` and `angular-route` for app functionalities.

# App structure #
Tortuga's workflow includes using tools like `yeoman`, `bower` and `grunt`. Being generated with yeoman, the app has the following structure which is pretty self-explanatory:

```
|-app
||-images
||-scripts
|||-controllers
|||-directives
|||-services
|||_app.js
||-styles
||-views
||-index.html
|-bower_components
|-node_modules
|-test
|-bower.json
|-Gruntfile.js
```
# App overview #
The app's configuration and URLs definitions are found inside the `app.js` file inside the scripts folder. 

Tortuga has three main views:
  - /starred: where popular tracks from the chosen service are shown.
  - /player: music player of the app.
  - /profile: where the user's music is found.

Each of these views is changed depending on the URL, which sets the value of the `ng-view` directive inside of the `index.html` file. 

## Services ##
All the information that the app manages is found through its services. Tortuga has two levels of services: those dependent on the application and those independent on the application.  

The app has to make several calls to different APIs to get information from different songs and update this information inside the playlists of the user. Each API has a service related which will only worry about making the request through angular's `$http` service and return the response data not caring about the apps format or needed information.

The main services which will care about that will be `track`, `formatTrack` and `playlist`.

### Track ###
`track` is a service that manages the requests to the selected services in order to get the top tracks from LastFM or query a song using Spotify's search engine. Its configuration lets us change the service for the search engine or the top tracks provider pretty easily. 

In order to format the responses of the requested songs, `track` gets injected the `formatTrack` service which adapts the response to the format defined by the app.

### FormatTrack ###
`formatTrack` defines a `Track` object which is used by all the controllers and services from the app when treating with the app's tracks. Each time we create a new API service we generate a function inside `formatTrack` which translates the response of that API to the one defined.
  
  ```javascript
  // Track object constructor for the App
    function Track (name, artist, album, image_small, image_large, playcount, id, playlist_id) {

      this.id = id || '';
      this.name = name;
      this.artist = artist;
      this.album = album || '';
      this.image_small = image_small || '';
      this.image_large = image_large || '';
      this.playcount = playcount || -1;
      this.link = undefined;
      this.playlist_id = playlist_id || '';
    }
  ```
### Playlist ###
`playlist` service takes care of the users playlists; loading the ones previously saved on the server and updating these when necessary. It is also the service that will contain all the information needed to play a track, add it to a list or delete it from another. The app uses indexes from the playlists and tracks array in order to execute any action on them.

For example if we click to add a track from one of our search results, the controller in charge of the search results will send the service the index of the list, the track clicked and the index of the destiny list as parameters of the funciton `playlist.addTrackToPlayList`. Then the `playlist` service will only copy the track from the search results list to the selected list.

## Views and controllers ##
### Popular tracks ###
The /starred URL provides us a list of the most popular songs from LastFM. The controller in charge of this view is the `StarredCtrl`. `StarredCtrl` will have the `track` service injected in order to request the top tracks of a particular provider. 

The controller will call `track.getTopTracks`, then inside this function track will call the selected API service and catch the response. Once recieved and before returning it back to the controller, `track` will call the correspondent `formatTrack` function in order to translate the response so the controller doesn't have to worry about the response format being able to just display the information using Tortuga's format.

![image](https://cloud.githubusercontent.com/assets/6149085/5714474/ad1e5a94-9ac9-11e4-8970-66ed044c63f4.png)

### Search bar ###
The search bar is located on the header inside `header.html` and controlled by `SearchCtrl`. Again follows the same flow as popular tracks to request data.

![screenshot from 2015-01-13 02 14 02](https://cloud.githubusercontent.com/assets/6149085/5714491/f84d0ee8-9ac9-11e4-8b6b-22a6612a9a34.png)

### Add Track ### 
`AddTrackCtrl` is a specific controller which inside a list of tracks allows you to add a track to a list using the `playlist` service.

![image](https://cloud.githubusercontent.com/assets/6149085/5714501/2afb4f30-9aca-11e4-8026-eea4c7d109f8.png)
![screenshot from 2015-01-13 02 16 38](https://cloud.githubusercontent.com/assets/6149085/5714527/cc6785d2-9aca-11e4-88b6-c31b199ad2fd.png)

### Profile ###
Profile follows the same pattern as starred but instead of displaying information from LastFM, displays the playlists from the user and allows you to create and modify new playlists.

![screenshot from 2015-01-13 02 22 14](https://cloud.githubusercontent.com/assets/6149085/5714552/15dbb102-9acb-11e4-8121-378542b980a0.png)
![screenshot from 2015-01-13 02 22 38](https://cloud.githubusercontent.com/assets/6149085/5714560/3108de50-9acb-11e4-8892-49c3e2a4f605.png)

## Music Player ##
Tortuga's player uses the `playerLogic` service and `PlayTrackCtrl` and `PlayerCtrl` controllers. `playerLogic` is one of the simplest services of all, the only information that has is the index of the playing song and the index of its playlist. Then the `PlayerCtrl` controller uses angular's `$watch` service to see when the song has changed and takes care of requesting the next song and playing it as well as letting the user change of song or pause the song.

![screenshot from 2015-01-13 02 24 34](https://cloud.githubusercontent.com/assets/6149085/5714596/a03e5ffc-9acb-11e4-969b-095e160b1e5c.png)
![screenshot from 2015-01-13 02 25 43](https://cloud.githubusercontent.com/assets/6149085/5714601/a9b9d7c8-9acb-11e4-8fc8-77fd63834190.png)

`PlayTrackCtrl` simply works as `AddTrackCtrl` although it modifies the indexes of the playerLogic service each time a song is clicked.

### YouTube directive ###
Tortuga has a YouTube directive which uses the YouTube javascript API to control the embedded videos. These are hidden in order to create a more streaming service look. Through angular's `$broadcast` service the `PlayerCtrl` controller is able to change the video (song) playing, play it and pause it. 

```
<youtube> ... </youtube>
```

## Recommendations ##
Tortuga also lets you get awesome recommendations from The Eco Nest API. Each time a song is played, the `recommendations` service is alerted and therefore makes a request to The Eco Nest asking for related artists to the 5 last played. These results can be seen inside the /profile view.

![screenshot from 2015-01-13 02 29 16](https://cloud.githubusercontent.com/assets/6149085/5714630/fe63bf46-9acb-11e4-8758-f62f71256f40.png)

### Set Up ###
To set up the app, clone the repository and execute `bower install` and `grunt serve`. The backend is still under development. Right now Tortuga uses a public API found in https://github.com/raulmoron/vmhipermedia, but our intention is to develop our own with Node.js and MongoDB.


