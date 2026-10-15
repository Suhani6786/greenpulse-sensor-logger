# GreenPulse Sensor Logger

This is my GreenPulse Sensor Logger project for the CSI 3150 assignment.

It is a simple greenhouse dashboard built with HTML, CSS, and JavaScript. Users can enter sensor readings, check their status, and save readings in the browser using `localStorage`.

## Files

- `index.html` – Contains the website structure and sensor form.
- `styles.css` – Handles the website design and layout.
- `app.js` – Handles input validation, sensor classification, event listeners, and saving readings.
- `README.md` – Contains project information and my Assignment 2A response.

## How to Run

1. Open `index.html` in a web browser.
2. Make sure `styles.css` and `app.js` are in the same folder.
3. No installation is needed.

## Website Sections

- **Header:** Shows the website title and reading count.
- **Sensor Form:** Allows users to enter the bay name, pH, EC, air temperature, and humidity.
- **Active Sensor Log:** Displays sensor readings in cards.
- **Footer:** Displays footer information.

## Features

- Validates sensor inputs.
- Shows error feedback while entering values.
- Classifies readings as Optimal, Warning, or Critical Hazard.
- Displays readings as cards.
- Uses the Resolve button to remove an individual reading.
- Uses the Purge Queue button to remove all readings.
- Saves readings in `localStorage`, so they remain after refreshing the page.
- Uses a responsive layout for smaller screens.

## Data Storage

The website saves sensor readings in the browser's `localStorage` using the key `greenpulse_records`.

`JSON.stringify()` converts the records array into text for storage, and `JSON.parse()` converts the saved text back into JavaScript data when the page loads.

---

# Assignment 2A

## Section 4.1: User Journey Funnel

**Starting State:** When the page loads, the user sees the website title, the sensor form, and the sensor log. Any saved readings are loaded from `localStorage`. If there are no readings, the page displays "No readings logged yet."

**Action 1:** The user enters the bay name, pH, EC, air temperature, and humidity. If a number is out of range, a red border provides visual feedback.

**Action 2:** The user clicks "Record Reading." JavaScript validates the inputs and determines the reading's status. If all values are valid, the reading is added to the records array and saved in `localStorage`. If any value is invalid, the reading is not added or saved.

**Action 3:** A new card appears with the bay name, timestamp, sensor values, and status badge. The reading count also updates.

**Action 4:** The user can click "Resolve" to remove one reading or "Purge Queue" to remove all readings.

**Terminal State:** The log displays the remaining readings or the empty message. After refreshing the page, saved readings are loaded again. After a successful submission, the form is cleared. If submission fails validation, the form is not cleared.

## Section 4.2: Don Norman Usability and Constraint Audit

**Gulf of Execution:** Each input has a label that explains what the user should enter, including units where needed. For example, pH uses a range of 0 to 14, EC is measured in mS/cm, air temperature is measured in °F, and humidity is measured in percent. Placeholders provide examples, such as `6.2` for pH.

**Gulf of Evaluation:** JavaScript classifies each reading based on the sensor values.

- **Critical Hazard:** pH is below 5.0 or above 7.5, temperature is below 55°F or above 95°F, EC is below 0.2 or above 4.5, and humidity is below 30 or above 90. All four conditions must be true at the same time.
- **Optimal:** pH is between 5.5 and 6.5, temperature is between 64.4°F and 71.6°F, EC is between 1.0 and 2.0, and humidity is between 50 and 70. All four values must be within their ranges.
- **Warning:** Any reading that does not meet the Critical Hazard or Optimal rules.

Each status has its own color and text badge to help users identify the result.

**System Constraint:** The number inputs use `type="number"`, `min`, `max`, `step`, and `required` attributes. The form uses `novalidate`, so JavaScript handles validation when the user submits the form. Invalid readings are not saved.

**Visual Feedback State:** An `input` event listener checks values as the user types. If a value is out of range, JavaScript adds the `input-error` class, which gives the input a red border. The class is removed when the value is corrected.

**Error Recovery:** The Resolve button removes one reading, while the Purge Queue button removes all readings. The page and `localStorage` are updated after these actions.

## Section 4.3: Component, DOM Tree, and Event Target Architecture

In the tree below, **[STATIC]** means the element is already in the HTML, while **[DYNAMIC]** means JavaScript creates or updates the element.

```text
<body> [STATIC]
  <header class="portal-header"> [STATIC]
    <h1> GreenPulse Sensor Logger [STATIC]
    <span id="queue-count"> Reading count [STATIC]

  <main class="portal-layout"> [STATIC]
    <aside class="panel form-panel"> [STATIC]
      <h2> Log Bay Reading [STATIC]
      <p id="form-error-banner"> Error message [STATIC]
      <form id="sensor-form" novalidate> [STATIC]
        <div class="form-group"> [STATIC]
          <label for="bay-id"> Bay Name [STATIC]
          <input id="bay-id" type="text" required> [STATIC]

        <div class="form-row"> [STATIC]
          <div class="form-group"> [STATIC]
            <label for="ph-level"> pH [STATIC]
            <input id="ph-level" type="number"
                   min="0" max="14" step="0.1"
                   required> [STATIC]

          <div class="form-group"> [STATIC]
            <label for="ec-level"> EC (mS/cm) [STATIC]
            <input id="ec-level" type="number"
                   min="0" max="10" step="0.1"
                   required> [STATIC]

          <div class="form-group"> [STATIC]
            <label for="air-temp"> Air Temperature (°F) [STATIC]
            <input id="air-temp" type="number"
                   min="32" max="120" step="0.1"
                   required> [STATIC]

          <div class="form-group"> [STATIC]
            <label for="humidity"> Humidity (%) [STATIC]
            <input id="humidity" type="number"
                   min="0" max="100" step="1"
                   required> [STATIC]

        <button id="btn-submit" type="submit">
          Record Reading [STATIC]

      <ul class="status-key"> Status colors [STATIC]

    <section class="panel records-panel"> [STATIC]
      <header class="records-header"> [STATIC]
        <h2> Active Sensor Log [STATIC]
        <button id="btn-purge" type="button">
          Purge Queue [STATIC]

      <p id="empty-state"> No readings logged yet [STATIC]

      <ul id="records-list" class="records-grid"> [STATIC]
        <li class="bay-card"> [DYNAMIC]
          <h3> Bay name [DYNAMIC]
          <span class="status-badge"> Status [DYNAMIC]
          <p class="timestamp"> Date and time [DYNAMIC]
          <dl class="readings-grid"> Sensor values [DYNAMIC]
          <button class="btn-resolve" data-id="...">
            Resolve [DYNAMIC]

  <footer class="portal-footer"> [STATIC]
    <p> Copyright information [STATIC]
```

**Initialization and Events:** When the page loads, JavaScript retrieves saved readings from `localStorage` and converts the JSON text back into an array. The `renderRecords()` function creates and displays the reading cards, updates the count, and handles the empty-state message. It uses a `DocumentFragment` to build the list before adding it to the page.

The `#records-list` element has one click listener that handles Resolve buttons for all reading cards. The form uses a submit listener, the sensor inputs use input listeners, and the Purge Queue button uses a click listener.

The main project files are `index.html`, `styles.css`, and `app.js`.

## Section 4.4: Data Schema

The following example shows the fields used to represent one sensor reading. It is a simplified illustration of the data structure and storage process.

```js
const sensorReadingSchema = {
  id: "String",         // Unique ID for each reading
  bayId: "String",      // Greenhouse bay name
  pH: "Number",         // pH value, 0 to 14
  ec: "Number",         // EC value, 0 to 10 mS/cm
  airTemp: "Number",    // Air temperature, 32 to 120°F
  humidity: "Number",   // Humidity, 0 to 100%
  status: "String",     // Optimal, Warning, or Critical Hazard
  timestamp: "String"   // Date and time in ISO format
};

let records = [];

// Save the records
localStorage.setItem("greenpulse_records", JSON.stringify(records));

// Load the saved records
const savedRecords = localStorage.getItem("greenpulse_records");
records = JSON.parse(savedRecords) || [];
```

The object describes the fields stored for each sensor reading. The save and load examples show how JavaScript stores the records array in the browser and retrieves it later.

`JSON.stringify()` converts the array into text so it can be saved. `JSON.parse()` converts the saved text back into JavaScript data when the page loads.

In the actual project, the loading process uses `try...catch` to handle errors if the saved data cannot be parsed correctly.

---

## GitHub Repository

Repository URL: https://github.com/Suhani6786/greenpulse-sensor-logger
