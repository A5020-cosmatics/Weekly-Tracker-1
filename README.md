<!DOCTYPE html><html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>My Weekly Progress Tracker</title>
  <style>
    body {
      font-family: 'Segoe UI', sans-serif;
      background: #f0f4f8;
      color: #333;
      margin: 0;
      padding: 20px;
    }
    h1, h2 {
      text-align: center;
      color: #2c3e50;
    }
    .day-section {
      background: white;
      margin: 20px 0;
      padding: 15px;
      border-radius: 10px;
      box-shadow: 0 2px 5px rgba(0,0,0,0.1);
    }
    label {
      display: block;
      margin: 10px 0;
    }
    button {
      background: #27ae60;
      color: white;
      border: none;
      padding: 10px 15px;
      border-radius: 5px;
      cursor: pointer;
    }
    button:disabled {
      background: #bdc3c7;
    }
    .motivation {
      font-style: italic;
      margin-top: 10px;
      color: #8e44ad;
    }
    .summary {
      background: #dff0d8;
      padding: 15px;
      margin-top: 20px;
      border-radius: 10px;
    }
    #chart-container {
      max-width: 600px;
      margin: 30px auto;
    }
  </style>
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
</head>
<body><h1>Weekly Progress Tracker</h1>
<p style="text-align: center">"Stay focused. Stay faithful. Your time will come, InshaAllah."</p><div id="tracker"></div><div class="summary" id="summary">
  <h2>Your Weekly Progress</h2>
  <p id="progress-text">Complete daily checklists to see your score!</p>
</div><div id="chart-container">
  <canvas id="progressChart"></canvas>
</div><script>
const week = ["Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday", "Sunday"];
const tasksByDay = {
  Monday: [
    "Attend university classes",
    "Review yesterday's study",
    "Watch Food Safety course (1 module)",
    "Write 3 bullet points from course",
    "Evening English word practice (10 mins)"
  ],
  Tuesday: [
    "Attend university classes",
    "Work on mini project or topic summary",
    "Update CV with any new course or experience",
    "Read 1 food industry article"
  ],
  Wednesday: [
    "Attend university classes",
    "Practice 2 IELTS reading/listening passages",
    "Write 3 new vocabulary words",
    "Practice speaking 5 mins aloud"
  ],
  Thursday: [
    "Attend university classes",
    "Continue online food science course",
    "Write 5 lines about what you learned",
    "Review class notes"
  ],
  Friday: [
    "Light study in morning",
    "Watch 1 motivational or Islamic video",
    "Write 1 thing you learned + 1 du'a",
    "Evening Quran reading or walk"
  ],
  Saturday: [
    "2 hrs deep revision (any subject)",
    "Watch 1 scholarship video (DAAD, Erasmus etc.)",
    "Write 5 ideas for your personal statement"
  ],
  Sunday: [
    "Review all notes from the week",
    "Write weekly reflection (what you learned, what improved)",
    "Call mentor or teacher",
    "Rest and spend time with family"
  ]
};

let totalTasks = 0;
let completedTasks = 0;
let dailyProgress = Array(7).fill(0);

function renderTracker() {
  const tracker = document.getElementById('tracker');
  week.forEach((day, dayIndex) => {
    const section = document.createElement('div');
    section.className = 'day-section';
    const heading = document.createElement('h2');
    heading.textContent = day;
    section.appendChild(heading);

    tasksByDay[day].forEach((task, index) => {
      totalTasks++;
      const label = document.createElement('label');
      const checkbox = document.createElement('input');
      checkbox.type = 'checkbox';
      checkbox.dataset.day = day;
      checkbox.dataset.index = index;
      checkbox.dataset.dayIndex = dayIndex;
      checkbox.addEventListener('change', updateProgress);
      label.appendChild(checkbox);
      label.appendChild(document.createTextNode(" " + task));
      section.appendChild(label);
    });

    const quote = document.createElement('p');
    quote.className = 'motivation';
    quote.textContent = getMotivationalQuote(day);
    section.appendChild(quote);

    tracker.appendChild(section);

    // Schedule notification reminder (simulated for each day at 2pm)
    const now = new Date();
    const reminderTime = new Date();
    reminderTime.setHours(14, 0, 0, 0);
    if ("Notification" in window && Notification.permission !== "denied") {
      Notification.requestPermission().then(permission => {
        if (permission === "granted") {
          const timeDiff = reminderTime.getTime() - now.getTime();
          if (timeDiff > 0) {
            setTimeout(() => {
              new Notification(`Reminder for ${day}`, {
                body: `Don't forget your daily tasks for ${day}. Bismillah!`
              });
            }, timeDiff);
          }
        }
      });
    }
  });
}

function updateProgress(e) {
  completedTasks = document.querySelectorAll('input[type=checkbox]:checked').length;
  const percent = Math.round((completedTasks / totalTasks) * 100);
  const progressText = document.getElementById('progress-text');
  const checkbox = e.target;
  const dayIndex = checkbox.dataset.dayIndex;
  if (checkbox.checked) dailyProgress[dayIndex]++;
  else dailyProgress[dayIndex]--;

  let message = "You are getting started. Keep going!";
  if (percent > 70) message = "Excellent! You're on track. MashaAllah!";
  else if (percent > 40) message = "Good progress. Stay consistent!";
  else if (percent > 0) message = "Decent start. Push a bit more!";

  progressText.innerHTML = `Progress: <strong>${percent}%</strong><br>${message}`;
  updateChart();
}

function getMotivationalQuote(day) {
  const quotes = {
    Monday: "A strong start leads to a strong week.",
    Tuesday: "What you do today builds your future.",
    Wednesday: "Discipline is more powerful than motivation.",
    Thursday: "Keep going. Every effort counts.",
    Friday: "Reset your soul. Recharge your focus.",
    Saturday: "Plan like a professional. Prepare like a warrior.",
    Sunday: "Reflect, rest, and rise again stronger."
  };
  return quotes[day];
}

let chart;
function updateChart() {
  if (!chart) {
    const ctx = document.getElementById('progressChart').getContext('2d');
    chart = new Chart(ctx, {
      type: 'bar',
      data: {
        labels: week,
        datasets: [{
          label: 'Tasks Completed',
          data: dailyProgress,
          backgroundColor: '#27ae60'
        }]
      },
      options: {
        scales: {
          y: { beginAtZero: true }
        }
      }
    });
  } else {
    chart.data.datasets[0].data = dailyProgress;
    chart.update();
  }
}

renderTracker();
</script></body>
</html>
