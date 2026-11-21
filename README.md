# Daily-Checklist
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Daily Habit Checklist</title>

  <style>
    * {
      box-sizing: border-box;
    }

    body {
      margin: 0;
      padding: 30px 16px;
      min-height: 100vh;
      font-family: Arial, sans-serif;
      color: #24302d;
      background: #f2f6f1;
    }

    .app {
      max-width: 760px;
      margin: 0 auto;
    }

    header {
      margin-bottom: 24px;
      text-align: center;
    }

    header h1 {
      margin-bottom: 8px;
      color: #27745b;
    }

    header p {
      margin-top: 0;
      color: #68756f;
    }

    .panel {
      margin-bottom: 18px;
      padding: 20px;
      border: 1px solid #e0e9e2;
      border-radius: 14px;
      background: white;
      box-shadow: 0 5px 18px #24302d0d;
    }

    .add-form {
      display: flex;
      gap: 10px;
    }

    input[type="text"] {
      flex: 1;
      min-width: 0;
      padding: 12px;
      border: 1px solid #ccd8cf;
      border-radius: 8px;
      font-size: 16px;
    }

    button,
    .file-button {
      padding: 11px 14px;
      border: 0;
      border-radius: 8px;
      font-size: 14px;
      cursor: pointer;
    }

    .primary-button {
      color: white;
      background: #27745b;
    }

    .primary-button:hover {
      background: #1e604a;
    }

    .secondary-button,
    .file-button {
      color: #27745b;
      background: #e8f3ec;
    }

    .secondary-button:hover,
    .file-button:hover {
      background: #dcece1;
    }

    .section-heading {
      display: flex;
      justify-content: space-between;
      align-items: center;
      gap: 12px;
    }

    .section-heading h2 {
      margin: 0;
      font-size: 20px;
    }

    #todayDate {
      color: #68756f;
      font-size: 14px;
    }

    #habitList {
      display: grid;
      gap: 10px;
      margin-top: 16px;
    }

    .habit-row {
      display: flex;
      align-items: center;
      gap: 12px;
      padding: 13px;
      border: 1px solid #e4ece5;
      border-radius: 10px;
    }

    .habit-row input {
      width: 19px;
      height: 19px;
      accent-color: #27745b;
      cursor: pointer;
    }

    .habit-name {
      flex: 1;
      overflow-wrap: anywhere;
    }

    .habit-row.completed .habit-name {
      color: #84918a;
      text-decoration: line-through;
    }

    .delete-button {
      padding: 7px 10px;
      color: #a34848;
      background: #fff0ef;
    }

    .delete-button:hover {
      background: #ffe2e0;
    }

    .empty-message {
      padding: 18px 10px;
      color: #78857e;
      text-align: center;
    }

    .progress-track {
      height: 12px;
      margin: 14px 0 8px;
      overflow: hidden;
      border-radius: 20px;
      background: #e8eee9;
    }

    #progressBar {
      width: 0%;
      height: 100%;
      border-radius: 20px;
      background: #54a77d;
      transition: width 0.25s ease;
    }

    #progressText {
      margin: 0;
      color: #68756f;
      font-size: 14px;
    }

    .week-grid {
      display: grid;
      grid-template-columns: repeat(7, 1fr);
      gap: 8px;
      margin-top: 16px;
    }

    .day {
      padding: 10px 4px;
      border-radius: 9px;
      background: #f2f6f1;
      text-align: center;
    }

    .day-name {
      display: block;
      margin-bottom: 7px;
      color: #718078;
      font-size: 12px;
    }

    .day-score {
      font-weight: bold;
      color: #27745b;
    }

    .file-actions {
      display: flex;
      flex-wrap: wrap;
      gap: 10px;
    }

    #importFile {
      display: none;
    }

    #message {
      min-height: 20px;
      margin: 10px 0 0;
      color: #27745b;
      font-size: 14px;
    }

    @media (max-width: 500px) {
      body {
        padding: 20px 12px;
      }

      .add-form {
        flex-direction: column;
      }

      .section-heading {
        align-items: flex-start;
        flex-direction: column;
      }

      .week-grid {
        gap: 4px;
      }

      .day {
        padding: 9px 2px;
      }
    }
  </style>
</head>

<body>
  <main class="app">
    <header>
      <h1>🌱 Daily Habit Checklist</h1>
      <p>Small steps, repeated daily.</p>
    </header>

    <section class="panel">
      <form class="add-form" id="habitForm">
        <input
          type="text"
          id="habitInput"
          placeholder="Add a habit, like Read for 20 minutes"
          maxlength="60"
          required
        >
        <button class="primary-button" type="submit">Add Habit</button>
      </form>
    </section>

    <section class="panel">
      <div class="section-heading">
        <h2>Today's habits</h2>
        <span id="todayDate"></span>
      </div>
      <div id="habitList"></div>
    </section>

    <section class="panel">
      <h2>Today's progress</h2>
      <div class="progress-track">
        <div id="progressBar"></div>
      </div>
      <p id="progressText">0 of 0 habits completed</p>
    </section>

    <section class="panel">
      <h2>This week</h2>
      <div class="week-grid" id="weekGrid"></div>
    </section>

    <section class="panel">
      <h2>Save or load your habits</h2>
      <div class="file-actions">
        <button class="secondary-button" id="exportButton" type="button">
          Export habits
        </button>

        <label class="file-button" for="importFile">Import habits</label>
        <input type="file" id="importFile" accept=".json,application/json">
      </div>
      <p id="message"></p>
    </section>
  </main>

  <script>
    const STORAGE_KEY = "dailyHabitChecklist";

    const habitForm = document.getElementById("habitForm");
    const habitInput = document.getElementById("habitInput");
    const habitList = document.getElementById("habitList");
    const progressBar = document.getElementById("progressBar");
    const progressText = document.getElementById("progressText");
    const todayDate = document.getElementById("todayDate");
    const weekGrid = document.getElementById("weekGrid");
    const exportButton = document.getElementById("exportButton");
    const importFile = document.getElementById("importFile");
    const message = document.getElementById("message");

    let habits = loadHabits();

    function loadHabits() {
      const savedHabits = localStorage.getItem(STORAGE_KEY);

      if (savedHabits) {
        try {
          return JSON.parse(savedHabits);
        } catch (error) {
          return [];
        }
      }

      return [];
    }

    function saveHabits() {
      localStorage.setItem(STORAGE_KEY, JSON.stringify(habits));
    }

    function getTodayKey() {
      const date = new Date();
      const year = date.getFullYear();
      const month = String(date.getMonth() + 1).padStart(2, "0");
      const day = String(date.getDate()).padStart(2, "0");

      return `${year}-${month}-${day}`;
    }

    function showDate() {
      todayDate.textContent = new Date().toLocaleDateString(undefined, {
        weekday: "long",
        month: "short",
        day: "numeric"
      });
    }

    function renderHabits() {
      habitList.innerHTML = "";

      if (habits.length === 0) {
        habitList.innerHTML =
          '<p class="empty-message">No habits yet. Add one above to get started!</p>';
        updateProgress();
        renderWeek();
        return;
      }

      const today = getTodayKey();

      habits.forEach(function (habit) {
        const row = document.createElement("div");
        row.className = "habit-row";

        const checkbox = document.createElement("input");
        checkbox.type = "checkbox";
        checkbox.checked = habit.completedDates.includes(today);
        checkbox.setAttribute("aria-label", "Mark " + habit.name + " complete");

        if (checkbox.checked) {
          row.classList.add("completed");
        }

        checkbox.addEventListener("change", function () {
          if (checkbox.checked) {
            if (!habit.completedDates.includes(today)) {
              habit.completedDates.push(today);
            }
          } else {
            habit.completedDates = habit.completedDates.filter(function (date) {
              return date !== today;
            });
          }

          saveHabits();
          renderHabits();
        });

        const name = document.createElement("span");
        name.className = "habit-name";
        name.textContent = habit.name;

        const deleteButton = document.createElement("button");
        deleteButton.className = "delete-button";
        deleteButton.textContent = "Delete";
        deleteButton.type = "button";

        deleteButton.addEventListener("click", function () {
          habits = habits.filter(function (item) {
            return item.id !== habit.id;
          });

          saveHabits();
          renderHabits();
        });

        row.appendChild(checkbox);
        row.appendChild(name);
        row.appendChild(deleteButton);
        habitList.appendChild(row);
      });

      updateProgress();
      renderWeek();
    }

    function updateProgress() {
      const today = getTodayKey();
      const completedCount = habits.filter(function (habit) {
        return habit.completedDates.includes(today);
      }).length;

      const percentage = habits.length === 0
        ? 0
        : Math.round((completedCount / habits.length) * 100);

      progressBar.style.width = percentage + "%";
      progressText.textContent =
        completedCount + " of " + habits.length +
        " habits completed (" + percentage + "%)";
    }

    function renderWeek() {
      weekGrid.innerHTML = "";

      for (let daysAgo = 6; daysAgo >= 0; daysAgo--) {
        const date = new Date();
        date.setDate(date.getDate() - daysAgo);

        const dateKey = [
          date.getFullYear(),
          String(date.getMonth() + 1).padStart(2, "0"),
          String(date.getDate()).padStart(2, "0")
        ].join("-");

        const completedCount = habits.filter(function (habit) {
          return habit.completedDates.includes(dateKey);
        }).length;

        const day = document.createElement("div");
        day.className = "day";

        const dayName = document.createElement("span");
        dayName.className = "day-name";
        dayName.textContent = date.toLocaleDateString(undefined, {
          weekday: "short"
        });

        const score = document.createElement("span");
        score.className = "day-score";
        score.textContent = completedCount + "/" + habits.length;

        day.appendChild(dayName);
        day.appendChild(score);
        weekGrid.appendChild(day);
      }
    }

    habitForm.addEventListener("submit", function (event) {
      event.preventDefault();

      const name = habitInput.value.trim();

      if (name === "") {
        return;
      }

      habits.push({
        id: Date.now(),
        name: name,
        completedDates: []
      });

      saveHabits();
      habitInput.value = "";
      habitInput.focus();
      message.textContent = "Habit added!";
      renderHabits();
    });

    exportButton.addEventListener("click", function () {
      const fileContents = JSON.stringify(habits, null, 2);
      const file = new Blob([fileContents], { type: "application/json" });
      const downloadLink = document.createElement("a");

      downloadLink.href = URL.createObjectURL(file);
      downloadLink.download = "my-habits.json";
      downloadLink.click();

      URL.revokeObjectURL(downloadLink.href);
      message.textContent = "Your habits have been saved to a JSON file.";
    });

    importFile.addEventListener("change", function () {
      const file = importFile.files[0];

      if (!file) {
        return;
      }

      const reader = new FileReader();

      reader.addEventListener("load", function () {
        try {
          const importedHabits = JSON.parse(reader.result);

          const isValid = Array.isArray(importedHabits) &&
            importedHabits.every(function (habit) {
              return habit &&
                typeof habit.name === "string" &&
                Array.isArray(habit.completedDates);
            });

          if (!isValid) {
            throw new Error("Invalid habit file");
          }

          habits = importedHabits;
          saveHabits();
          renderHabits();
          message.textContent = "Habits imported successfully.";
        } catch (error) {
          message.textContent = "That file could not be read. Choose a valid habits JSON file.";
        }

        importFile.value = "";
      });

      reader.readAsText(file);
    });

    showDate();
    renderHabits();
  </script>
</body>
</html>
