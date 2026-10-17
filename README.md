# GreenPulse Sensor Logger

A greenhouse sensor logging page built with plain HTML, CSS, and JavaScript (no frameworks, no libraries). CSI 3150, Assignment 2B.

## What it does

- Log a reading for a bay: bay name, pH, EC, air temperature, and humidity.
- Bad values are rejected right away (red border and a message in the error banner).
- Each reading gets a status: Optimal, Warning, or Critical Hazard.
- Readings are saved in the browser with `localStorage`, so they are still there after a refresh.
- "Resolve" removes one reading. "Purge Queue" removes all of them.

## Files

- `index.html` is the page structure.
- `styles.css` is the layout and colors.
- `app.js` is all the logic, wrapped in an IIFE.
- `README.md` is this file (my Assignment 2A answers are below).

## How to run

Open `index.html` in a browser. No install or server is needed.

## Status rules

- **Critical Hazard:** (pH under 5.0 or over 7.5) AND (Temp under 55°F or over 95°F) AND (EC under 0.2 or over 4.5) AND (Humidity under 30 or over 90). All four must be true.
- **Optimal:** pH 5.5 to 6.5, Temp 64.4 to 71.6°F, EC 1.0 to 2.0, Humidity 50 to 70. All four must be in range.
- **Warning:** anything else.

---

# Assignment 2A


## Section 4.1: User Journey Funnel

Starting State: Page loads. User sees the app name, a form, and the log list. Saved readings load from localStorage. If none, it says "No readings logged yet."

Action 1: User types the bay name, pH, EC, temp, and humidity. A red border shows if a number is out of range.

Action 2: User clicks "Record Reading." JS checks the values and works out the status. If every value is valid, the reading is added to the records array and the array is saved in localStorage. If a value is invalid, nothing is added or saved.

Action 3: A new card shows the bay name, time, four numbers, and a status badge. The count goes up.

Action 4: User clicks "Resolve" to delete one card, or "Purge Queue" to delete all cards.

Terminal State: The list shows the readings that are left, or the empty message. Refreshing loads them back from localStorage. After a successful submit, the form is cleared. After an invalid submit, it is not cleared.


## Section 4.2: Don Norman Usability & Constraint Audit

Gulf of Execution: Labels have units, like pH (0 to 14), EC (mS/cm), Air Temperature (°F), and Humidity (%). Each input has a `placeholder` with an example, like 6.2 for pH.

Gulf of Evaluation: JS gives each reading a status. Critical Hazard: (pH under 5.0 or over 7.5) AND (Temp under 55°F or over 95°F) AND (EC under 0.2 or over 4.5) AND (Humidity under 30 or over 90). All four must be true at the same time.

Optimal: pH 5.5 to 6.5, Temp 64.4 to 71.6°F, EC 1.0 to 2.0, Humidity 50 to 70. All four must be in range. Warning: anything that is not Critical Hazard or Optimal. Each status has its own color and a text badge.

System Constraint: Number inputs use `type="number"`, `min`, `max`, `step`, and `required`. The form uses `novalidate`, so JS checks every value on submit. If a value is invalid, the reading is not saved.

Visual Feedback State: An `input` listener checks the value while typing. Out of range adds the `input-error` class (red border). The class is removed when the value is fixed.

Error Recovery: "Resolve" removes one reading and "Purge Queue" removes all. JS then updates the list and localStorage.


## Section 4.3: Component, DOM Tree & Event Target Architecture

[STATIC] means the element is already in the HTML. [DYNAMIC] means JS creates it or changes it.

```text
<body> [STATIC]
  <header class="portal-header"> (CSS: display flex, space-between, centered) [STATIC]
    <h1> (Text: "GreenPulse Sensor Logger") [STATIC]
    <span id="queue-count"> (Text: number of readings, JS updates it) [STATIC]
  <main class="portal-layout"> (CSS: display grid, one column, form on top and log below) [STATIC]
    <aside class="panel form-panel"> (CSS: display flex, column, gap) [STATIC]
      <h2> (Text: "Log Bay Reading") [STATIC]
      <p id="form-error-banner" class="alert alert-danger hidden" role="alert"> (Text: error message, JS shows or hides it) [STATIC]
      <form id="sensor-form" novalidate> (CSS: display grid, bay name, four numbers, then the button in one row; stacks on small screens) [STATIC] (Event: submit)
        <div class="form-group"> [STATIC]
          <label for="bay-id" class="form-label"> (Text: "Bay Name") [STATIC]
          <input id="bay-id" type="text" class="form-control" placeholder="Bay 1" required> [STATIC]
        <div class="form-row"> (CSS: display flex, row, gap) [STATIC]
          <div class="form-group"> [STATIC]
            <label for="ph-level" class="form-label"> (Text: "pH (0 to 14)") [STATIC]
            <input id="ph-level" type="number" class="form-control" min="0" max="14" step="0.1" placeholder="6.2" required> [STATIC] (Event: input)
          <div class="form-group"> [STATIC]
            <label for="ec-level" class="form-label"> (Text: "EC (mS/cm)") [STATIC]
            <input id="ec-level" type="number" class="form-control" min="0" max="10" step="0.1" placeholder="1.5" required> [STATIC] (Event: input)
          <div class="form-group"> [STATIC]
            <label for="air-temp" class="form-label"> (Text: "Air Temperature (°F)") [STATIC]
            <input id="air-temp" type="number" class="form-control" min="32" max="120" step="0.1" placeholder="68" required> [STATIC] (Event: input)
          <div class="form-group"> [STATIC]
            <label for="humidity" class="form-label"> (Text: "Humidity (%)") [STATIC]
            <input id="humidity" type="number" class="form-control" min="0" max="100" step="1" placeholder="60" required> [STATIC] (Event: input)
        <button id="btn-submit" type="submit" class="btn btn-primary btn-block"> (Text: "Record Reading") [STATIC]
      <ul class="status-key"> (CSS: display flex, wrap) [STATIC]
        <li> x3 (Text: what Optimal, Warning, and Critical Hazard mean, each with a colored dot) [STATIC]
    <section class="panel records-panel"> (CSS: display flex, column) [STATIC]
      <header class="records-header"> (CSS: display flex, space-between, centered) [STATIC]
        <h2> (Text: "Active Sensor Log") [STATIC]
        <button id="btn-purge" type="button" class="btn btn-outline-danger btn-sm"> (Text: "Purge Queue") [STATIC] (Event: click)
      <p id="empty-state"> (Text: "No readings logged yet.", JS shows or hides it) [STATIC]
      <ul id="records-list" class="records-grid" aria-live="polite"> (CSS: display grid, repeat(auto-fit, minmax(260px, 1fr))) [STATIC] (Event: click, one listener for all Resolve buttons)
        <li class="bay-card status-optimal / status-warning / status-critical" data-id="..."> [DYNAMIC]
          <h3> (Text: bay name) [DYNAMIC]
          <span class="status-badge"> (Text: Optimal / Warning / Critical Hazard) [DYNAMIC]
          <p class="timestamp"> (Text: time of the reading) [DYNAMIC]
          <dl class="readings-grid"> (CSS: display grid, 2 columns) [DYNAMIC]
          <button class="btn btn-sm btn-resolve" data-id="..."> (Text: "Resolve") [DYNAMIC] (Event: click, handled by the listener on #records-list)
  <footer class="portal-footer"> (CSS: display flex, centered) [STATIC]
    <p> (Text: copyright) [STATIC]
```

Initialization: On page load, JS loads saved readings from localStorage, turns the JSON text back into the `records` array, then draws the cards, the count, and the empty message. A `renderRecords()` function rebuilds the list using a DocumentFragment, and one click listener on `#records-list` handles every Resolve button. Files: index.html, styles.css, app.js.


## Section 4.4: Data Schema

This is a simplified example of how the data is saved and loaded. The real code is in `app.js`.

```js
const sensorReadingSchema = {
  id: "String",         // unique ID for each reading
  bayId: "String",      // name of the greenhouse bay
  pH: "Number",         // 0 to 14
  ec: "Number",         // mS/cm, 0 to 10
  airTemp: "Number",    // °F, 32 to 120
  humidity: "Number",   // %, 0 to 100
  status: "String",     // "Optimal", "Warning", or "Critical Hazard"
  timestamp: "String"   // time of the reading, like 10:30 PM
};

let records = [];

// Save
localStorage.setItem("greenpulse_records", JSON.stringify(records));

// Load on page open
const savedRecords = localStorage.getItem("greenpulse_records");
records = JSON.parse(savedRecords) || [];
```

The object at the top describes the fields of one reading. The save and load code under it shows how the data is stored.

`JSON.stringify()` saves the array as text. `JSON.parse()` turns it back into an array after a refresh. If nothing is saved, the array stays empty.


## GitHub Repository

https://github.com/Suhani6786/greenpulse-sensor-logger
