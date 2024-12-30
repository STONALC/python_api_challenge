In completing this challenge, I reviewed videos from the past class sessions. I also consulted microsoft co-pilot, youtube and other 
internet sources python_api_challenge.

The following codes were written and/or edited with the aid of microsoft co-pilot.

# Empty list for holding the latitude and longitude combinations
lat_lngs = []

# Empty list for holding the cities names
cities = []

# Range of latitudes and longitudes
lat_range = (-90, 90)
lng_range = (-180, 180)

# Create a set of random lat and lng combinations
lats = np.random.uniform(lat_range[0], lat_range[1], size=1500)
lngs = np.random.uniform(lng_range[0], lng_range[1], size=1500)
lat_lngs = zip(lats, lngs)

# Identify nearest city for each lat, lng combination
for lat_lng in lat_lngs:
    city = citipy.nearest_city(lat_lng[0], lat_lng[1]).city_name

    # If the city is unique, then add it to a our cities list
    if city not in cities:
        cities.append(city)

# Print the city count to confirm sufficient count
print(f"Number of cities in the list: {len(cities)}")

# Import necessary modules

import requests
import time
from citipy import citipy

# Define list of cities (or coordinates)
cities = ["Colorado", "Albany", "Saint-Pierre", "New Norfolk", "Nikolskoye", "Jamestown", "Lanzhou"]

# Set the API base URL
api_key = '00eb7b870d45fb21a6497434e70f1e73'  
base_url = "http://api.openweathermap.org/data/2.5/weather?"

# Define an empty list to fetch the weather data for each city
city_data = []

# Print to logger
print("Beginning Data Retrieval     ")
print("-----------------------------")

# Create counters
record_count = 1
set_count = 1

# Loop through all the cities in our list to fetch weather data
for i, city in enumerate(cities):

    # Group cities in sets of 50 for logging purposes
    if (i % 50 == 0 and i >= 50):
        set_count += 1
        record_count = 0

    # Create endpoint URL with each city
    city_url = f"{base_url}q={city}&appid={api_key}"

    # Log the url, record, and set numbers
    print("Processing Record %s of Set %s | %s" % (record_count, set_count, city))

    # Add 1 to the record count
    record_count += 1

    # Run an API request for each of the cities
    try:
        # Parse the JSON and retrieve data
        city_weather = response.json() 

        # Parse out latitude, longitude, max temp, humidity, cloudiness, wind speed, country, and date
        city_lat = city_weather['coord']['lat']
        city_lng = city_weather['coord']['lon']
        city_max_temp = city_weather['main']['temp_max']
        city_humidity = city_weather['main']['humidity']
        city_clouds = city_weather['clouds']['all']
        city_wind = city_weather['wind']['speed']
        city_country = city_weather['sys']['country']
        city_date = city_weather['dt']
        
        # Append the City information into city_data list
        city_data.append({"City": city,
                          "Lat": city_lat,
                          "Lng": city_lng,
                          "Max Temp": city_max_temp,
                          "Humidity": city_humidity,
                          "Cloudiness": city_clouds,
                          "Wind Speed": city_wind,
                          "Country": city_country,
                          "Date": city_date})

    # If an error is experienced, skip the city
    except:
        print("City not found. Skipping...")
        pass

    # pause to avoid rate limiting
    time.sleep(1)

# Indicate that Data Loading is complete
print("-----------------------------")
print("Data Retrieval Complete      ")
print("-----------------------------")

import pandas as pd
import requests

# Define the city weather data
city_weather_data = [
    {'City': 'Colorado', 'Lat': -22.8375, 'Lng': -47.0722, 'Max Temp': 20.36, 'Humidity': 86, 'Cloudiness': 96, 'Wind Speed': 4.69, 'Country': 'BR', 'Date': 1666107965},
    {'City': 'Albany', 'Lat': 42.6001, 'Lng': -73.9772, 'Max Temp': 10.36, 'Humidity': 74, 'Cloudiness': 29, 'Wind Speed': 2.1, 'Country': 'US', 'Date': 1666108231},
    {'City': 'Saint-Pierre', 'Lat': -21.3393, 'Lng': 55.4781, 'Max Temp': 22.82, 'Humidity': 68, 'Cloudiness': 0, 'Wind Speed': 5.14, 'Country': 'RE', 'Date': 1666108236},
    {'City': 'New Norfolk', 'Lat': -42.7826, 'Lng': 147.0587, 'Max Temp': 11.72, 'Humidity': 58, 'Cloudiness': 12, 'Wind Speed': 1.34, 'Country': 'AU', 'Date': 1666108230},
    {'City': 'Nikolskoye', 'Lat': 59.7035, 'Lng': 30.7861, 'Max Temp': 8.94, 'Humidity': 90, 'Cloudiness': 100, 'Wind Speed': 3.22, 'Country': 'RU', 'Date': 1666108237},
    {'City': 'Jamestown', 'Lat': 42.097, 'Lng': -79.2353, 'Max Temp': 5.77, 'Humidity': 77, 'Cloudiness': 100, 'Wind Speed': 9.77, 'Country': 'US', 'Date': 1666107934},
    {'City': 'Lanzhou', 'Lat': 36.0564, 'Lng': 103.7928, 'Max Temp': 14.53, 'Humidity': 48, 'Cloudiness': 59, 'Wind Speed': 1.2, 'Country': 'CN', 'Date': 1666108230}
]

# Convert the list of dictionaries to a DataFrame
city_data_df = pd.DataFrame(city_weather_data)

# Add an empty column for "Hotel Name"
hotel_df = city_data_df[['City', 'Country', 'Lat', 'Lng', 'Humidity']].copy()
hotel_df['Hotel Name'] = ''

# Set parameters to search for a hotel
radius = 10000
params = {
    'categories': 'accommodation.hotel',
    'filter': '',
    'limit': 1,
    'apiKey': "00eb7b870d45fb21a6497434e70f1e73"
}

# Define the API endpoint
base_url = "https://api.geoapify.com/v2/places"

# Function to get hotel name using Geoapify API
def get_hotel_name(lat, lng):
    filter_str = f"circle:{lng},{lat},{radius}"
    bias_str = f"proximity:{lng},{lat}"
    params["filter"] = filter_str
    params["bias"] = bias_str

    # Make an API request using the params dictionary
    response = requests.get(base_url, params=params)
    
    # Convert the API response to JSON format
    name_address = response.json()
    
    if response.status_code == 200:
        if name_address['features']:
            hotel_name = name_address['features'][0]['properties']['name']
            return hotel_name 
    return "No hotel found"

# Print a message to follow up the hotel search
print("Starting hotel search")

# Iterate through the hotel_df DataFrame
for index, row in hotel_df.iterrows():
    # Get latitude, longitude from the DataFrame
    lat = row['Lat']
    lng = row['Lng']
    
    # Get hotel name using the Geoapify API
    hotel_name = get_hotel_name(lat, lng)
    
    # Grab the first hotel from the results and store the name in the hotel_df DataFrame
    try:
        hotel_df.loc[index, "Hotel Name"] = hotel_name
    except (KeyError, IndexError):
        # Handle cases where the hotel information is not available
        hotel_df.loc[index, "Hotel Name"] = "No hotel found"

    # Log the search results
    print(f"{hotel_df.loc[index, 'City']} - nearest hotel: {hotel_df.loc[index, 'Hotel Name']}")

# Display sample data
print(hotel_df)

and

%%capture --no-display
import folium
# Configure the map plot

# Initialize a map centered around the average location
average_lat = hotel_df['Lat'].mean()
average_lng = hotel_df['Lng'].mean()
map_plot = folium.Map(location=[average_lat, average_lng], zoom_start=2)

# Add points to the map
for index, row in hotel_df.iterrows():
    folium.CircleMarker(
        location=[row['Lat'], row['Lng']],
        radius=row['Humidity'] / 10,  # Scale the radius by humidity
        popup=f"City: {row['City']}<br>Hotel: {row['Hotel Name']}",
        color='blue',
        fill=True,
        fill_color='blue'
    ).add_to(map_plot)

# Save the map to an HTML file
map_plot.save("city_hotel_map.html")

# Display the map 
from IPython.display import IFrame
IFrame('city_hotel_map.html', width=700, height=500)
