(function () {
  "use strict";

  const STORAGE_KEY = "greenpulse_records";

  const sensorForm = document.getElementById("sensor-form");
  const errorBanner = document.getElementById("form-error-banner");
  const recordsList = document.getElementById("records-list");
  const queueCount = document.getElementById("queue-count");
  const emptyState = document.getElementById("empty-state");
  const btnPurge = document.getElementById("btn-purge");

  const inputBay = document.getElementById("bay-id");
  const inputPh = document.getElementById("ph-level");
  const inputEc = document.getElementById("ec-level");
  const inputTemp = document.getElementById("air-temp");
  const inputHumidity = document.getElementById("humidity");

  const allInputs = [inputBay, inputPh, inputEc, inputTemp, inputHumidity];

  const messages = {};

  let records = loadRecords();

  renderRecords();

  function loadRecords() {
    try {
      const stored = localStorage.getItem(STORAGE_KEY);
      return stored ? JSON.parse(stored) : [];
    } catch (error) {
      console.error("Could not read the saved readings", error);
      return [];
    }
  }

  function saveRecords() {
    try {
      localStorage.setItem(STORAGE_KEY, JSON.stringify(records));
      return true;
    } catch (error) {
      console.error("Could not save the readings", error);
      return false;
    }
  }

  function evaluateStatus(pH, ec, airTemp, humidity) {
    const phCritical = pH < 5.0 || pH > 7.5;
    const tempCritical = airTemp < 55 || airTemp > 95;
    const ecCritical = ec < 0.2 || ec > 4.5;
    const humidityCritical = humidity < 30 || humidity > 90;

    if (phCritical && tempCritical && ecCritical && humidityCritical) {
      return "Critical Hazard";
    }

    const phOptimal = pH >= 5.5 && pH <= 6.5;
    const tempOptimal = airTemp >= 64.4 && airTemp <= 71.6;
    const ecOptimal = ec >= 1.0 && ec <= 2.0;
    const humidityOptimal = humidity >= 50 && humidity <= 70;

    if (phOptimal && tempOptimal && ecOptimal && humidityOptimal) {
      return "Optimal";
    }

    return "Warning";
  }

  function checkBay() {
    if (inputBay.value.trim() === "") {
      return "Bay Name is required.";
    }
    return "";
  }

  function checkNumber(input, label, min, max) {
    const value = parseFloat(input.value);

    if (isNaN(value)) {
      return label + " is required and must be a number.";
    }

    if (value < min || value > max) {
      return label + " must be between " + min + " and " + max + ".";
    }
    return "";
  }

  function getMessage(input) {
    if (input === inputBay) {
      return checkBay();
    }
    if (input === inputPh) {
      return checkNumber(inputPh, "pH", 0, 14);
    }
    if (input === inputEc) {
      return checkNumber(inputEc, "EC", 0, 10);
    }
    if (input === inputTemp) {
      return checkNumber(inputTemp, "Air Temperature", 32, 120);
    }
    return checkNumber(inputHumidity, "Humidity", 0, 100);
  }

  function validateField(input) {
    messages[input.id] = getMessage(input);
    if (messages[input.id] === "") {
      input.classList.remove("input-error");
    } else {
      input.classList.add("input-error");
    }
  }

  function collectErrors() {
    const errors = [];
    allInputs.forEach(function (input) {
      if (messages[input.id]) {
        errors.push(messages[input.id]);
      }
    });
    return errors;
  }

  function validateInput() {
    allInputs.forEach(function (input) {
      validateField(input);
    });
    return collectErrors();
  }

  function displayError(message) {
    errorBanner.textContent = message;
    errorBanner.classList.remove("hidden");
  }

  function hideError() {
    errorBanner.textContent = "";
    errorBanner.classList.add("hidden");
  }

  function updateBanner() {
    const errors = collectErrors();
    if (errors.length > 0) {
      displayError(errors.join("\n"));
    } else {
      hideError();
    }
  }

  function renderRecords() {
    queueCount.textContent = records.length + " active";

    if (records.length === 0) {
      emptyState.classList.remove("hidden");
      recordsList.innerHTML = "";
      return;
    }

    emptyState.classList.add("hidden");

    const fragment = document.createDocumentFragment();

    records.forEach(function (rec) {
      const li = document.createElement("li");

      let statusClass = "status-warning";
      if (rec.status === "Optimal") {
        statusClass = "status-optimal";
      }
      if (rec.status === "Critical Hazard") {
        statusClass = "status-critical";
      }
      li.className = "bay-card " + statusClass;

      li.innerHTML = `
        <h3>${escapeHtml(rec.bayId)}</h3>
        <span class="status-badge">${escapeHtml(rec.status)}</span>
        <p class="timestamp">Logged at ${rec.timestamp}</p>
        <dl class="readings-grid">
          <div class="reading"><dt>pH</dt><dd>${escapeHtml(rec.pH)}</dd></div>
          <div class="reading"><dt>EC</dt><dd>${escapeHtml(rec.ec)} mS/cm</dd></div>
          <div class="reading"><dt>Air Temp</dt><dd>${escapeHtml(rec.airTemp)}°F</dd></div>
          <div class="reading"><dt>Humidity</dt><dd>${escapeHtml(rec.humidity)}%</dd></div>
        </dl>
        <button type="button" class="btn btn-sm btn-resolve" data-id="${rec.id}">Resolve</button>`;

      fragment.appendChild(li);
    });

    recordsList.innerHTML = "";
    recordsList.appendChild(fragment);
  }

  function escapeHtml(value) {
    return String(value)
      .replace(/&/g, "&amp;")
      .replace(/</g, "&lt;")
      .replace(/>/g, "&gt;");
  }

  allInputs.forEach(function (input) {
    input.addEventListener("input", function () {
      validateField(input);
      updateBanner();
    });
  });

  sensorForm.addEventListener("submit", function (event) {
    event.preventDefault();

    const validationErrors = validateInput();

    if (validationErrors.length > 0) {
      displayError(validationErrors.join("\n"));
      return;
    }

    hideError();

    const pH = parseFloat(inputPh.value);
    const ec = parseFloat(inputEc.value);
    const airTemp = parseFloat(inputTemp.value);
    const humidity = parseFloat(inputHumidity.value);

    const formData = {
      id: crypto.randomUUID(),
      bayId: inputBay.value.trim(),
      pH: pH,
      ec: ec,
      airTemp: airTemp,
      humidity: humidity,
      status: evaluateStatus(pH, ec, airTemp, humidity),
      timestamp: new Date().toLocaleTimeString([], {
        hour: "2-digit",
        minute: "2-digit",
      }),
    };

    records.unshift(formData);

    if (!saveRecords()) {
      records.shift();
      displayError("Could not save the reading. Browser storage may be full or blocked.");
      return;
    }

    renderRecords();

    sensorForm.reset();
    allInputs.forEach(function (input) {
      messages[input.id] = "";
      input.classList.remove("input-error");
    });

    inputBay.focus();
  });

  recordsList.addEventListener("click", function (event) {
    const target = event.target;
    if (target.classList.contains("btn-resolve")) {
      const recordId = target.dataset.id;
      records = records.filter(function (record) {
        return record.id !== recordId;
      });
      saveRecords();
      renderRecords();
    }
  });

  btnPurge.addEventListener("click", function () {
    if (records.length === 0) {
      return;
    }
    if (confirm("Are you sure you want to delete all readings?")) {
      records = [];
      saveRecords();
      renderRecords();
    }
  });
})();
