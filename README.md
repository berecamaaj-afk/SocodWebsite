# SocodWebsite
Create 
let steps = 0;
let lastMagnitude = 0;

if ("Accelerometer" in window) {
  const accel = new Accelerometer({ frequency: 60 });
  accel.addEventListener("reading", () => {
    const magnitude = Math.sqrt(
      accel.x * accel.x +
      accel.y * accel.y +
      accel.z * accel.z
    );

    if (magnitude - lastMagnitude > 1.2) {
      steps++;
      document.getElementById("steps").innerText = steps;
    }
    lastMagnitude = magnitude;
  });
  accel.start();
} else {
  alert("Step sensor lama heli karo");
}
Send steps → backend
Copy code
Js
async function sendSteps() {
  await fetch("https://YOUR_BACKEND/api/steps/add", {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
      "Authorization": localStorage.getItem("token")
    },
    body: JSON.stringify({ steps })
  });
  steps = 0;
}
2️⃣ ADMIN DASHBOARD (Frontend) 👑
HTML + JS (fudud, degdeg)
admin.html
Copy code
Html
<h2>Withdraw Requests</h2>
<div id="list"></div>

<script>
async function loadWithdraws(){
  const res = await fetch("https://YOUR_BACKEND/api/admin/withdraws",{
    headers:{ "Authorization": localStorage.getItem("token") }
  });
  const data = await res.json();

  document.getElementById("list").innerHTML =
    data.map(w => `
      <div>
        ${w.amount}$ | ${w.method} | ${w.phoneNumber}
        <button onclick="approve('${w._id}')">Approve</button>
      </div>
    `).join("");
}

async function approve(id){
  await fetch("https://YOUR_BACKEND/api/admin/withdraw/approve/"+id,{
    method:"POST",
    headers:{ "Authorization": localStorage.getItem("token") }
  });
  loadWithdraws();
}
loadWithdraws();
</script>
3️⃣ ANTI-FRAUD (Backend – ku dar hal meel)
routes/steps.js (limit)
Copy code
Js
if (steps > 20000) {
  return res.json({ msg: "Daily limit exceeded" });
}
