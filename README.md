[![Build Status](https://github.com/maximn/google-maps/actions/workflows/dotnet.yml/badge.svg)](https://github.com/maximn/google-maps/actions/workflows/dotnet.yml)
[![NuGet Status](https://img.shields.io/nuget/v/GoogleMapsApi.svg)](https://www.nuget.org/packages/GoogleMapsApi/)

# google-maps

Google Maps Web Services API wrapper for .NET

For Quickstart and more info read the wiki pages (https://github.com/maximn/google-maps/wiki)

The web page - http://maximn.github.com/google-maps

NuGet page - https://www.nuget.org/packages/GoogleMapsApi/

**Check out my blog at http://maxondev.com**

# Quickstart

This library wraps Google maps API.

You can easily query Google maps for Geocoding, Directions, Elevation, and Places.

NEW! Now you can easily show the results on a Static Google Map!

This Library is well documented and easy to use.

Code sample -

```C#
using GoogleMapsApi;
using GoogleMapsApi.Entities.Common;
using GoogleMapsApi.Entities.Directions.Request;
using GoogleMapsApi.Entities.Directions.Response;
using GoogleMapsApi.Entities.Geocoding.Request;
using GoogleMapsApi.Entities.Geocoding.Response;
using GoogleMapsApi.StaticMaps;
using GoogleMapsApi.StaticMaps.Entities;

var googleAPIKey = "google api key";
//Static class use (Directions) (Can be made from static/instance class)
var directionsRequest = new DirectionsRequest()
{
    Origin = "NYC, 5th and 39",
    Destination = "Philladephia, Chesnut and Wallnut",
    IsSSL = true,
    ApiKey = googleAPIKey,
};

var directions = await GoogleMaps.Directions.QueryAsync(directionsRequest);
Console.WriteLine(directions);

//Instance class use (Geocode)  (Can be made from static/instance class)
var geocodeRequest = new GeocodingRequest()
{
    Address = "NYC, 5th and 39",
    IsSSL = true,
    ApiKey = googleAPIKey,
};
var geocodingEngine = GoogleMaps.Geocode;
var geocode = await geocodingEngine.QueryAsync(geocodeRequest);
Console.WriteLine(geocode);

// Static maps API - get static map of with the path of the directions request
StaticMapsEngine staticMapGenerator = new StaticMapsEngine();

//Path from previos directions request
IEnumerable<Step> steps = directions.Routes.First().Legs.First().Steps;
// All start locations
IList<ILocationString> path = steps.Select(step => step.StartLocation).ToList<ILocationString>();
// also the end location of the last step
path.Add(steps.Last().EndLocation);

string url = staticMapGenerator.GenerateStaticMapURL(new StaticMapRequest(new Location(40.38742, -74.55366), 9, new ImageSize(800, 400))
{
    Pathes = new List<GoogleMapsApi.StaticMaps.Entities.Path>(){ new GoogleMapsApi.StaticMaps.Entities.Path()
    {
            Style = new PathStyle()
            {
                    Color = "red"
            },
            Locations = path
    }}
    ,
    IsSSL = true
    ,
    ApiKey = googleAPIKey
});
Console.WriteLine("Map with path: " + url);

//use EncodePath
var overviewEncodePath = directions.Routes.First().OverviewPath.GetRawPointsData();
string encodePathUrl = staticMapGenerator.GenerateStaticMapURL(new StaticMapRequest(new Location(40.38742, -74.55366), 9, new ImageSize(800, 400))
{
    Pathes = new List<GoogleMapsApi.StaticMaps.Entities.Path>(){ new GoogleMapsApi.StaticMaps.Entities.Path()
    {
            Style = new PathStyle()
            {
                    Color = "red"
            },
            EncodePolyline = overviewEncodePath
    }}
    ,
    IsSSL = true
    ,
    ApiKey = googleAPIKey
});
Console.WriteLine("Map with EncodePath: " + encodePathUrl);
```
