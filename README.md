
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Game Dev Training Platform</title>
  <style>
    body { font-family: Arial, sans-serif; margin: 0; background: #f4f4f9; }
    header { background: #333; color: #fff; padding: 15px; text-align: center; }
    nav { background: #444; padding: 10px; text-align: center; }
    nav a { color: #fff; margin: 0 10px; text-decoration: none; }
    .container { padding: 20px; }
    .section { padding: 20px; margin: 20px 0; background: #fff; border-radius: 10px; }
    h2 { color: #333; }
    .hidden { display: none; }
    input, textarea, button {
      display: block; margin: 10px 0; padding: 8px; width: 100%;
      border-radius: 5px; border: 1px solid #ccc;
    }
    button { background: #333; color: #fff; cursor: pointer; }
    button:hover { background: #555; }
    pre { background: #eee; padding: 10px; border-radius: 5px; }
    .lesson, .quiz { border: 1px solid #ccc; padding: 10px; margin: 10px 0; border-radius: 8px; }
    .scoreboard { background: #eee; padding: 10px; margin-top: 20px; border-radius: 8px; }
  </style>
</head>
<body>
  <header>
    <h1>🎮 Game Development Training</h1>
    <p>Learn & Teach Game Development with Fun</p>
  </header>

  <nav>
    <a href="#" onclick="showSection('login')">Login</a>
    <a href="#" onclick="showSection('lessons')">Lessons</a>
    <a href="#" onclick="showSection('quiz')">Quiz</a>
    <a href="#" onclick="showSection('admin')">Admin Panel</a>
    <a href="#" onclick="showSection('leaderboard')">Leaderboard</a>
  </nav>

  <div class="container">

    <!-- Login Section -->
    <div id="login" class="section">
      <h2>Login</h2>
      <input type="text" id="username" placeholder="Username">
      <input type="password" id="password" placeholder="Password">
      <button onclick="login()">Login</button>
      <p><strong>Demo Accounts:</strong></p>
      <p>👨‍🏫 Teacher → user: <code>admin</code>, pass: <code>1234</code></p>
      <p>👩‍🎓 Student → user: <code>student</code>, pass: <code>1234</code></p>
    </div>

    <!-- Lessons Section -->
    <div id="lessons" class="section hidden">
      <h2>📚 Lessons</h2>
      <div id="lessonList"></div>
    </div>

    <!-- Quiz Section -->
    <div id="quiz" class="section hidden">
      <h2>🎯 Quick Quiz</h2>
      <div class="quiz">
        <p><b>Q1:</b> Which language is mainly used in Unity?</p>
        <button onclick="checkAnswer(this,'C#')">C#</button>
        <button onclick="checkAnswer(this,'C++')">C++</button>
        <button onclick="checkAnswer(this,'Python')">Python</button>
      </div>
      <div class="quiz">
        <p><b>Q2:</b> Godot Engine 4 supports which renderer?</p>
        <button onclick="checkAnswer(this,'Vulkan')">Vulkan</button>
        <button onclick="checkAnswer(this,'DirectX')">DirectX</button>
        <button onclick="checkAnswer(this,'Metal')">Metal</button>
      </div>
      <p id="quizResult"></p>
    </div>

    <!-- Admin Panel -->
    <div id="admin" class="section hidden">
      <h2>⚙️ Admin Panel</h2>
      <input type="text" id="lessonTitle" placeholder="Lesson Title">
      <textarea id="lessonContent" placeholder="Lesson Content (HTML allowed)"></textarea>
      <button onclick="addLesson()">Add Lesson</button>
      <h3>Existing Lessons</h3>
      <div id="adminLessonList"></div>
    </div>

    <!-- Leaderboard -->
    <div id="leaderboard" class="section hidden">
      <h2>🏆 Leaderboard</h2>
      <div id="scoreboard" class="scoreboard"></div>
    </div>

  </div>

  <script>
    let currentUser = null;
    let score = 0;

    // preload demo lessons
    if (!localStorage.getItem("lessons")) {
      const demo = [
        {title: "C# Basics", content: "<p>C# is Unity's main scripting language.</p>"},
        {title: "C++ in Games", content: "<p>C++ is used in Unreal Engine and AAA titles.</p>"},
        {title: "Unity Engine", content: "<p>Unity is cross-platform, supporting 2D, 3D, VR, AR.</p>"},
        {title: "Godot 4", content: "<p>Godot 4 is open-source with Vulkan renderer.</p>"},
        {title: "MLBB Case Study", content: "<p>Mobile Legends is a MOBA made with Unity.</p>"}
      ];
      localStorage.setItem("lessons", JSON.stringify(demo));
    }

    if (!localStorage.getItem("scores")) {
      localStorage.setItem("scores", JSON.stringify({}));
    }

    function showSection(id) {
      document.querySelectorAll(".section").forEach(sec => sec.classList.add("hidden"));
      document.getElementById(id).classList.remove("hidden");
      if (id === "lessons") renderLessons();
      if (id === "admin") renderAdminLessons();
      if (id === "leaderboard") renderLeaderboard();
    }

    function login() {
      const user = document.getElementById("username").value;
      const pass = document.getElementById("password").value;
      if ((user === "admin" && pass === "1234") || (user === "student" && pass === "1234")) {
        currentUser = user;
        alert("Welcome " + user);
        showSection("lessons");
      } else {
        alert("Invalid credentials!");
      }
    }

    function renderLessons() {
      const lessons = JSON.parse(localStorage.getItem("lessons")) || [];
      const list = document.getElementById("lessonList");
      list.innerHTML = "";
      lessons.forEach((lesson, i) => {
        list.innerHTML += `<div class="lesson"><h3>${lesson.title}</h3><div>${lesson.content}</div></div>`;
      });
    }

    function addLesson() {
      if (currentUser !== "admin") {
        alert("Only admin can add lessons!");
        return;
      }
      const title = document.getElementById("lessonTitle").value;
      const content = document.getElementById("lessonContent").value;
      const lessons = JSON.parse(localStorage.getItem("lessons")) || [];
      lessons.push({title, content});
      localStorage.setItem("lessons", JSON.stringify(lessons));
      document.getElementById("lessonTitle").value = "";
      document.getElementById("lessonContent").value = "";
      renderAdminLessons();
      renderLessons();
    }

    function renderAdminLessons() {
      const lessons = JSON.parse(localStorage.getItem("lessons")) || [];
      const list = document.getElementById("adminLessonList");
      list.innerHTML = "";
      lessons.forEach((lesson, i) => {
        list.innerHTML += `<div class="lesson"><h3>${lesson.title}</h3>
          <button onclick="deleteLesson(${i})">Delete</button></div>`;
      });
    }

    function deleteLesson(i) {
      const lessons = JSON.parse(localStorage.getItem("lessons")) || [];
      lessons.splice(i, 1);
      localStorage.setItem("lessons", JSON.stringify(lessons));
      renderAdminLessons();
      renderLessons();
    }

    function checkAnswer(btn, answer) {
      let correct = (answer === "C#" || answer === "Vulkan");
      if (correct) {
        score += 10;
        document.getElementById("quizResult").innerHTML =
          "✅ Correct! You earned 10 points. Total: " + score;
      } else {
        document.getElementById("quizResult").innerHTML =
          "❌ Wrong! Try again. Total: " + score;
      }
      saveScore();
    }

    function saveScore() {
      let scores = JSON.parse(localStorage.getItem("scores"));
      if (!scores[currentUser]) scores[currentUser] = 0;
      scores[currentUser] = score;
      localStorage.setItem("scores", JSON.stringify(scores));
    }

    function renderLeaderboard() {
      let scores = JSON.parse(localStorage.getItem("scores"));
      let list = Object.entries(scores).sort((a,b)=>b[1]-a[1]);
      let html = "<ol>";
      list.forEach(([user, sc]) => {
        html += `<li>${user}: ${sc} points</li>`;
      });
      html += "</ol>";
      document.getElementById("scoreboard").innerHTML = html;
    }

    // default section
    showSection("login");
  </script>
</body>
</html>
