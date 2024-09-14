# Trucker GIS

## Project Overview

The project involved designing a Geographic Information System (GIS) fully implemented in C++ using OpenStreetMap data. Recognizing that 77.7% of all Canadian goods are transported by truck, this project focuses on addressing the needs of truck drivers—a group often underserved by conventional GIS solutions. Trucker GIS is tailored specifically for truck drivers, providing features that prioritize their requirements and improve their navigational efficiency.

## Key Features

- **General GIS Features**: Navigate and explore interactive maps of major cities including Toronto, New York City, Tokyo, and Tehran. The system uses the A* pathfinding algorithm with various heuristics to obtain optimal directions, lookup intersections, and provide navigational instructions.
  
- **Weather Information**: Trucker GIS integrates a 2-second weather API call from OpenWeatherMap to display weather-related information. Users can view current temperatures and detailed weather conditions to prevent weather-related cargo damage.

- **Categorical Filters**: Filters for finding nearby restaurants, gas stations, and hotels. The filter UI is designed to be non-intrusive yet easily accessible, showing relevant details such as names, phone numbers, star ratings for hotels, and opening hours for restaurants.

- **Live Traffic Information**: Displays live traffic data on the selected route, helping truck drivers make better-informed decisions for timely deliveries.

## Demos & Examples

### Weather Features Demo

This demo showcases the weather icon located at the bottom of the map. Clicking the icon toggles a point weather tooltip that displays the live temperature at the user's cursor location. For detailed weather information, users can press the "Weather Information" button to open a pop-up with specific details such as current, minimum, maximum temperatures, and weather conditions.

*Video demonstrating the Weather Features will be provided here.*

### Categorical Filters Demo

In this demo, users select the “Food” filter button to focus on nearby restaurants. Clicking the filter button places pins on the map indicating different restaurants. Users can click on a restaurant name to center the view on it and get more information. The demo also shows how users can switch between options and clear filters by clicking on an intersection.

*Video demonstrating the Categorical Filters will be provided here.*

### Live Traffic Information

The live traffic information feature highlights the optimal route based on the A* algorithm, marking the starting point with a green flag and the destination with a checkered flag. Traffic conditions are color-coded (blue for not busy, yellow for little busy, and orange for very busy), and users receive descriptive navigational instructions along with statistics such as estimated distance and time of arrival.

*Picture illustrating Live Traffic Information will be provided here.*

## Technical Design Details

### Weather Information System

**Explanation**:
The weather information system processes real-time weather data as follows:
1. **API Call**: Trucker GIS queries the OpenWeatherMap database to retrieve weather data in JSON format.
2. **JSON Parsing**: The JSON data is parsed using a custom Boost JSON Parser developed for Trucker GIS. This parsing converts the JSON into a format suitable for further processing.
3. **Data Handling**: The parsed data, represented as a `ptree` object, is passed to a handler service. This service interprets the data and facilitates interaction between the backend and the frontend.
4. **Event Controllers**: The backend connects to the frontend through two event controllers, `draw_main_canvas` and `ezgl_event_handler`. These controllers manage user interactions with the weather feature:
   - **`toggleWeatherToolTipBtnCbk`**: Triggered when a user clicks the weather icon, displaying the temperature tooltip.
   - **`getWeatherDetailCbk`**: Activated when the user clicks the “Weather Information” button to show detailed weather information.

*Diagram illustrating the Weather Information System workflow will be provided here.*

### Categorical Filters

**Explanation**:
The categorical filters are designed to efficiently display points of interest (POIs) based on user-selected categories:
1. **POI Retrieval**: When the GIS loads the selected map, it queries the OpenStreetMap database to gather IDs for restaurants, gas stations, and hotels. These IDs are filtered to ensure they are valid and then stored in an `unordered_map`.
2. **Filter Activation**: When a user selects a filter, a callback function connects the frontend component to the backend. The system checks if the zoom level is appropriate, adjusting it if necessary to ensure localized results.
3. **Windowing Algorithm**: A windowing algorithm searches the `unordered_map` for POIs within the user's viewing window based on latitude and longitude coordinates. The use of `unordered_map` and constant-time complexity ensures rapid performance of the filter.

*Diagram depicting the design and functionality of the Categorical Filters will be provided here.*

### Live Traffic Information

**Explanation**:
The live traffic information feature provides real-time traffic updates on the map:
1. **Traffic API Call**: When the user requests the fastest path, Trucker GIS makes an API call to TomTom Traffic to obtain traffic data in JSON format.
2. **Data Parsing**: The JSON data is parsed to extract relevant traffic information.
3. **Traffic Interpretation**: A handler service interprets the traffic data, assigning colors to different traffic conditions (blue for not busy, yellow for little busy, and orange for very busy).
4. **Map Update**: The event controller uses the interpreted data to highlight the optimal path on the map with color-coded traffic information. The handler service is responsible for drawing the path and its color segments on the map.

*Diagram showing the implementation of Live Traffic Information will be provided here.*

### Pathfinding Algorithm

**Explanation**:
The pathfinding algorithm enhances efficiency using the Linear Range of Distances (LRD) heuristic:
1. **LRD Heuristic**: This heuristic improves upon traditional A* by incorporating a linear range calculation. The algorithm computes the straight-line distance from each intersection to the optimal path (dashed red line).
2. **Distance Calculation**: The straight-line distance is divided by the maximum speed limit to estimate travel time from the intersection to the dashed line.
3. **Path Selection**: Paths are evaluated based on their intersections' proximity to the dashed line and turn penalties. The algorithm selects the path with the best overall efficiency, highlighted in blue.

*Diagram explaining the Linear Range of Distances (LRD) heuristic used in the Pathfinding Algorithm will be provided here.*

## Design Principles

Trucker GIS emphasizes usability and responsiveness:

- **Weather Tooltip Responsiveness**: The weather tooltip feature displays temperature readings in 0.15 seconds, including map render time. This nearly instant feedback enhances usability by providing consistent positional feedback and informational tooltips [Mwangi et al., ResearchGate](https://www.researchgate.net/publication/331199201_A_Review_Of_Web-Based_GIS_Usability_Elements).

- **Categorical Filter Performance**: Experiments show Trucker GIS' filter responsiveness is 7.25 times faster than Google Maps, with an average response time of 0.2 seconds compared to Google Maps’ 1.45 seconds. This performance is attributed to the design choices made for backend implementation [ISO 9241-11](https://doi.org/10.1080/01449290410001669914).

- **Route Traffic Information**: The route traffic information feature has an approximate response time of 0.3 seconds, well below the industry standard of 2 seconds for tolerable waiting time [ISO standards](https://online-viewer-techstreet-com.myaccess.library.utoronto.ca/virtualviewer/launchViewer.jsp).

## Future Plans

1. **Pit Stops Integration**:
   - **Gap**: Current GIS solutions do not simplify the route planning process for including pit stops.
   - **Plan**: Implement preset pit stops that drivers can toggle to plan routes with detours. This will involve using a multi-Dijkstra algorithm to handle multiple destinations.
   - **Implementation**: Develop a feature to automatically adjust routes based on desired pit stops, providing seamless route planning for long trips.

2. **Customization for Truck Specifications**:
   - **Gap**: Existing GIS systems do not customize routes for large diesel/gas trucks with specific travel restrictions.
   - **Plan**: Implement a sidebar tool for selecting truck models to provide custom routes, speed calculations, and fuel consumption estimates.
   - **Implementation**: Add a selection tool to account for vehicle specifications, enhancing route planning and arrival time estimates based on truck-specific parameters.

## Glossary

- **Heuristic (in A* Algorithm)**: A function used to estimate the cost from a current state to the goal state, guiding the search towards the most promising paths.
  
- **Linear Range of Distances (LRD)**: A heuristic in pathfinding that calculates the straight-line distance from an intersection to the optimal path and uses this information to improve path selection efficiency.

## Contact

For more information, contact Koosha Omidian at [Koosha.omidian@mail.utoronto.ca](mailto:Koosha.omidian@mail.utoronto.ca). 

Note: This project was developed for a university course, and due to academic integrity policies, the source code cannot be shared outside the course.

## Acknowledgements

Special thanks to my group members for their contributions to this project.
