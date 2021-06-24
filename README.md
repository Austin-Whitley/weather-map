# Weather-map
##Features:

- Double click anywhere on the map to move the marker
- Drag and drop the marker
- Search for a location using the search field in the top left
- Ability to toggle between 4 and 8-day forecast
- expandable weather cards for those interested in daily wind speeds and sunset/sunrise // moonrise/moonset
- 10 different map backgrounds to choose from


##How it works:

On start, I instantiate a new instance of weather map through a mapbox call like so:
```
var map = new mapboxgl.Map({
container: 'map', // container ID
style: 'mapbox://styles/mapbox/streets-v11', // style URL
center: [-97.109, 32.738], // starting position [lng, lat]
zoom: 10 // starting zoom
});
```
I went ahead and centered on my location, however this can be changed to the users computer location
by using a Geocoding API by google (may implement at a later date)

The users marker gets added similarly, create a new instance of a marker object and pass it some location:
~~~
var marker = new mapboxgl.Marker({
    draggable: true,
    color: "red",
})
    .setLngLat([-97.109, 32.738])
    .addTo(map);
~~~

Now that I have a starting point, I can use some built in methods of the mapbox to create an array that will store the current longitude and latitude of our marker:
~~~
var coordinates = [marker.getLngLat().lat, marker.getLngLat().lng];
~~~

Then I just have to update the coordinates variable anytime an event takes place.
For example, when the user is finished dragging the marker to the desired location we can do this:
~~~
marker.on("dragend", function () {
    coordinates = [marker.getLngLat().lat, marker.getLngLat().lng]
    console.log(coordinates)
    write();
});
~~~

Now you might say "That's all fun and games, but you still haven't told me how you're retrieving data". 
And to that I say "Here's how the magic happens!"

You may have noticed the write() function call in the above code... that's the magic sauce.

First things first, we need to make an ajax request to the API. In order to do this we need to pass a couple of things: Longitude, Latitude, and a 'Key'
(it's a good thing I made that variable earlier :D) Here's how that looks:
~~~
 $.ajax("https://api.openweathermap.org/data/2.5/onecall?units=imperial&lat=" + coordinates[0] + "&lon=" + coordinates[1] + "&exclude=minutely,hourly&appid=" + WEATHERMAP_ACCESS_TOKEN)
        .done(function (response)
~~~
The request that was just made to the mapBox API will return a response which I used to pull out relative weather data for any location.
I then turn that response into a variable called 'today' that will be looped through to update today's weather as well as up to a week of weather data from the current day.
~~~
var today = response.daily[i];
~~~
Then I tack together the API calls into one giant line of HTML
for example:
~~~
html += "<p>Real Feel: " + Math.round(today.feels_like.day) + "°</p>";
~~~
Then append the string of html code to the page: 
~~~
$("#insertInfo").append(html);
~~~
and BOOM, you have 8 days of constantly updateed weather information anywhere on earth (excluding some areas such as North Korea)
at the click of a button.

I hope this was a little insightful, but mainly just fun for those who read the README
please look through the full code(most everything happens in the weather-map.js file) if you're interested.
#
Sincerely,

Austin Whitley.