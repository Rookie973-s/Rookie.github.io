<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Watchlist</title>
  <style>
    body {
      font-family: sans-serif;
      background-color: #121212;
      color: #ffffff;
      max-width: 600px;
      margin: 30px auto;
      padding: 20px;
      border-radius: 15px;
      background: linear-gradient(145deg, #1e1e1e, #1c1c1c);
      box-shadow: 0 0 10px rgba(0,0,0,0.6);
    }

    input[type="text"] {
      padding: 10px;
      font-size: 16px;
      width: 70%;
      margin-bottom: 10px;
    }

    button {
      padding: 10px;
      font-size: 16px;
      cursor: pointer;
      background-color: #03dac6;
      border: none;
      color: #000;
      border-radius: 5px;
      margin-left: 5px;
    }

    ul {
      list-style-type: none;
      padding: 0;
    }

    li {
      margin: 10px 0;
      background-color: #1e1e1e;
      padding: 10px;
      border-radius: 8px;
      display: flex;
      align-items: center;
      justify-content: space-between;
    }

    li span {
      flex-grow: 1;
      margin-left: 10px;
    }

    h2 {
      text-align: center;
    }

    #welcomeText {
      font-style: italic;
      margin-bottom: 10px;
    }
    @import url('https://fonts.googleapis.com/css2?family=Crimson+Pro:wght@600&family=IM+Fell+English+SC&display=swap');

.lightning-container {
  position: fixed;
  bottom: 30px;
  left: 50%;
  transform: translateX(-50%);
  z-index: 9999;
}

.fantasy-banner {
  background: rgba(255, 248, 220, 0.07);
  padding: 15px 25px;
  border: 1px solid rgba(255, 248, 220, 0.25);
  border-radius: 12px;
  backdrop-filter: blur(6px);
  box-shadow: 0 0 30px rgba(255, 244, 214, 0.2);
  text-align: center;
  font-family: 'Crimson Pro', serif;
  opacity: 0;
  animation: fadeInBanner 3s ease-out 0.5s forwards;
}

.tagline {
  font-size: 14px;
  letter-spacing: 1px;
  color: #f0e6d2;
  opacity: 0.8;
  margin: 0;
}

.studio-name {
  font-family: 'IM Fell English SC', serif;
  font-size: 24px;
  letter-spacing: 2px;
  color: #ffe9b0;
  margin: 0;
  text-shadow: 1px 1px 4px rgba(255, 238, 180, 0.6), 0 0 10px rgba(255, 221, 120, 0.2);
  animation: glowText 4s ease-in-out infinite alternate;
}

/* Fade in after lightning */
@keyframes fadeInBanner {
  to {
    opacity: 1;
  }
}

/* Magical glow */
@keyframes glowText {
  0% {
    text-shadow: 0 0 5px rgba(255, 221, 150, 0.2);
  }
  100% {
    text-shadow: 0 0 15px rgba(255, 221, 120, 0.4), 0 0 25px rgba(255, 235, 150, 0.3);
  }
}

/* Lightning effect */
.lightning-flash {
  position: absolute;
  top: -200px;
  left: -200px;
  width: 1000px;
  height: 1000px;
  background: white;
  opacity: 0;
  animation: lightningStrike 1.2s ease-in-out 0.1s;
  pointer-events: none;
  border-radius: 50%;
  filter: blur(80px);
  z-index: -1;
}

@keyframes lightningStrike {
  0% { opacity: 0; }
  5% { opacity: 1; }
  10% { opacity: 0; }
  15% { opacity: 0.7; }
  20% { opacity: 0; }
  100% { opacity: 0; }
}

  </style>
</head>
<body>

  <h2></h2>

  <!-- Login area -->
  <input type="text" id="usernameInput" placeholder="Enter your name...">
  <button onclick="loginUser()">Login</button>
  <p id="welcomeText"></p>

  <!-- Series input area -->
  <input type="text" id="seriesInput" placeholder="Enter series name..." disabled>
  <button onclick="addSeries()">Add</button>

  <!-- List display -->
  <ul id="seriesList"></ul>
  <div class="lightning-container">
    <div class="lightning-flash"></div>
    <div class="fantasy-banner">
      <p class="tagline">Brought to you by</p>
      <h1 class="studio-name">RTSTUDIOS</h1>
    </div>
  </div>
  
  <script>
    // GLOBAL VARIABLES
    let currentUser = null;
    const seriesList = document.getElementById("seriesList");
    const inputBox = document.getElementById("seriesInput");

    // Function to log in user
    function loginUser() {
      const username = document.getElementById("usernameInput").value.trim();
      if (username !== "") {
        currentUser = username;
        document.getElementById("welcomeText").textContent = `Welcome, ${username}!`;
        inputBox.disabled = false;
        loadList();
      }
    }

    // Function to add a new series
    function addSeries() {
      const seriesName = inputBox.value.trim();
      if (seriesName !== "") {
        const listItem = document.createElement("li");

        const checkbox = document.createElement("input");
        checkbox.type = "checkbox";
        checkbox.addEventListener("change", () => {
          listItem.style.textDecoration = checkbox.checked ? "line-through" : "none";
          saveList();
        });

        const span = document.createElement("span");
        span.textContent = seriesName;

        const deleteBtn = document.createElement("button");
        deleteBtn.textContent = "Delete";
        deleteBtn.addEventListener("click", () => {
          listItem.remove();
          saveList();
        });

        listItem.appendChild(checkbox);
        listItem.appendChild(span);
        listItem.appendChild(deleteBtn);
        seriesList.appendChild(listItem);

        inputBox.value = "";
        saveList();
      }
    }

    // Save user's watchlist to localStorage
    function saveList() {
      if (!currentUser) return;

      const allItems = [];
      const listElements = seriesList.querySelectorAll("li");

      listElements.forEach((li) => {
        const checkbox = li.querySelector("input[type='checkbox']");
        const seriesName = li.querySelector("span").textContent.trim();
        allItems.push({
          name: seriesName,
          watched: checkbox.checked
        });
      });

      localStorage.setItem(`watchlist_${currentUser}`, JSON.stringify(allItems));
    }

    // Load watchlist for current user
    function loadList() {
      seriesList.innerHTML = "";

      if (!currentUser) return;

      const savedList = localStorage.getItem(`watchlist_${currentUser}`);
      if (savedList) {
        const items = JSON.parse(savedList);

        items.forEach((item) => {
          const listItem = document.createElement("li");

          const checkbox = document.createElement("input");
          checkbox.type = "checkbox";
          checkbox.checked = item.watched;
          checkbox.addEventListener("change", () => {
            listItem.style.textDecoration = checkbox.checked ? "line-through" : "none";
            saveList();
          });

          const span = document.createElement("span");
          span.textContent = item.name;

          const deleteBtn = document.createElement("button");
          deleteBtn.textContent = "Delete";
          deleteBtn.addEventListener("click", () => {
            listItem.remove();
            saveList();
          });

          listItem.appendChild(checkbox);
          listItem.appendChild(span);
          listItem.appendChild(deleteBtn);
          listItem.style.textDecoration = item.watched ? "line-through" : "none";

          seriesList.appendChild(listItem);
        });
      }
    }
  </script>
</body>
</html>
