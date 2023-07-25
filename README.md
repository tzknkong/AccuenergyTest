# AccuenergyTest

This project is a web application built using Vue.js that allows users to acquire their current location, search for locations by name, and display the searched locations on a map. Additionally, it features a table with pagination to show all searched places, along with the ability to select and delete multiple records.

Installation
Clone this repository to your local machine.
Make sure you have Node.js and npm installed.
Navigate to the project directory and run the following command to install dependencies:
bash

npm install
After the installation is complete, run the development server:
bash

npm run serve
Open your web browser and go to http://localhost:8080 to access the app.

Features
Get Current Location: Click the "Get Current Location" button to acquire your current location from your browser.

Search for Locations: Use the search module to input the name of a location and press the "Enter" key or click the "Search" button to trigger the search.

Map Display: The searched locations will be displayed on a map, and a marker will be added to each searched location every time the location changes.

Table with Pagination: The table displays a maximum of 10 records on each page, and you can navigate through the pages using the pagination controls.

Multiple Selection: Each row in the table has a checkbox at the beginning to allow users to select multiple records simultaneously.

Delete Records: Use the "Delete" button on the top to remove all selected records from the table and their corresponding markers on the map.

Time Zone and Local Time: The app displays the time zone and local time of the latest searched location.
