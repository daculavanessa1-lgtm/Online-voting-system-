
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Online Voting System</title>
<style>
* { box-sizing: border-box; }
body {
  margin: 0;
  font-family: Arial, sans-serif;
  background: #f0f4fa;
  color: #172554;
}
header {
  background: #123c78;
  color: white;
  text-align: center;
  padding: 25px 12px;
}
header h1 { margin: 0 0 8px; }
nav {
  background: #082b59;
  padding: 12px;
  text-align: center;
}
nav a {
  color: white;
  text-decoration: none;
  margin: 0 12px;
}
.container {
  max-width: 850px;
  margin: 25px auto;
  padding: 15px;
}
.card {
  background: white;
  padding: 22px;
  margin-bottom: 20px;
  border-radius: 12px;
  box-shadow: 0 3px 12px #0001;
}
.candidate {
  border: 1px solid #dbe4f0;
  border-radius: 10px;
  padding: 15px;
  margin: 12px 0;
}
button {
  background: #1763bd;
  color: white;
  border: none;
  border-radius: 7px;
  padding: 12px 20px;
  cursor: pointer;
}
button:hover { background: #0b438b; }
input {
  width: 100%;
  padding: 12px;
  margin: 8px 0 15px;
  border: 1px solid #ccd6e5;
  border-radius: 7px;
}
.result {
  background: #e8f1ff;
  padding: 12px;
  border-radius: 8px;
  margin: 10px 0;
}
.hidden { display: none; }
footer {
  text-align: center;
  padding: 20px;
  background: #123c78;
  color: white;
}
</style>
</head>
<body>

<header>
  <h1>ONLINE VOTING SYSTEM</h1>
  <p>Organization Election Portal</p>
</header>

<nav>
  <a href="#home">Home</a>
  <a href="#vote">Vote</a>
  <a href="#results">Results</a>
  <a href="#admin">Admin</a>
</nav>

<main class="container">

<section class="card" id="home">
  <h2>Welcome Voter!</h2>
  <p>Welcome to our organization's official online voting portal.</p>
  <p>Please select your preferred candidate and submit your vote.</p>
</section>

<section class="card" id="vote">
  <h2>Cast Your Vote</h2>
  <label for="voterId">Voter ID</label>
  <input id="voterId" placeholder="Enter your Voter ID">

  <h3>Select a Candidate</h3>
  <div id="candidates"></div>
  <button onclick="submitVote()">Submit Vote</button>
  <p id="message"></p>
</section>

<section class="card" id="results">
  <h2>Election Results</h2>
  <div id="resultsList"></div>
</section>

<section class="card" id="admin">
  <h2>Administrator Login</h2>
  <input id="adminPassword" type="password"
         placeholder="Enter admin password">
  <button onclick="adminLogin()">Login</button>
  <div id="adminPanel" class="hidden">
    <h3>Admin Dashboard</h3>
    <p>Total Votes: <strong id="totalVotes">0</strong></p>
    <div id="adminResults"></div>
    <button onclick="resetVotes()">Reset Election Data</button>
  </div>
</section>

</main>

<footer>
  <p>© 2026 Online Voting System | Organization Election</p>
</footer>

<script>
const candidates = [
  "Candidate 1",
  "Candidate 2",
  "Candidate 3"
];

let selectedCandidate = "";

function getVotes() {
  return JSON.parse(localStorage.getItem("votes") || "{}");
}

function getVoters() {
  return JSON.parse(localStorage.getItem("voters") || "[]");
}

function displayCandidates() {
  document.getElementById("candidates").innerHTML =
    candidates.map(name => `
      <div class="candidate">
        <label>
          <input type="radio" name="candidate"
            value="${name}" style="width:auto">
          <strong>${name}</strong>
        </label>
      </div>
    `).join("");
}

function submitVote() {
  const voterId = document.getElementById("voterId").value.trim();
  const choice = document.querySelector(
    'input[name="candidate"]:checked'
  );
  const message = document.getElementById("message");

  if (!voterId || !choice) {
    message.textContent = "Please enter your Voter ID and select a candidate.";
    return;
  }

  let voters = getVoters();

  if (voters.includes(voterId)) {
    message.textContent = "You have already voted!";
    return;
  }

  let votes = getVotes();
  votes[choice.value] = (votes[choice.value] || 0) + 1;

  voters.push(voterId);

  localStorage.setItem("votes", JSON.stringify(votes));
  localStorage.setItem("voters", JSON.stringify(voters));

  message.textContent = "Your vote has been submitted successfully!";
  document.getElementById("voterId").value = "";
  document.querySelectorAll('input[name="candidate"]')
    .forEach(input => input.checked = false);

  showResults();
}

function showResults() {
  const votes = getVotes();
  const total = Object.values(votes).reduce((a,b) => a+b, 0);

  document.getElementById("resultsList").innerHTML =
    candidates.map(name => `
      <div class="result">
        <strong>${name}</strong><br>
        Votes: ${votes[name] || 0}
        <div style="background:#d1dced;height:9px;border-radius:8px">
          <div style="background:#1763bd;height:9px;border-radius:8px;width:${
            total ? (votes[name] || 0) / total * 100 : 0
          }%"></div>
        </div>
      </div>
    `).join("");
}

function adminLogin() {
  const password = document.getElementById("adminPassword").value;

  if (password === "admin123") {
    document.getElementById("adminPanel").classList.remove("hidden");
    updateAdmin();
  } else {
    alert("Incorrect admin password!");
  }
}

function updateAdmin() {
  const votes = getVotes();
  const total = Object.values(votes).reduce((a,b) => a+b, 0);

  document.getElementById("totalVotes").textContent = total;

  document.getElementById("adminResults").innerHTML =
    candidates.map(name =>
      `<p>${name}: ${votes[name] || 0} votes</p>`
    ).join("");
}

function resetVotes() {
  if (confirm("Are you sure you want to reset all votes?")) {
    localStorage.removeItem("votes");
    localStorage.removeItem("voters");
    updateAdmin();
    showResults();
    alert("Election data reset.");
  }
}

displayCandidates();
showResults();
</script>
</body>
</html>
