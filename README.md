# circulaar-queue-project
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Circular Queue Ticket Simulation 🎫</title>

<style>
  body {
    font-family: 'Poppins', sans-serif;
    background: linear-gradient(135deg, #2193b0, #6dd5ed);
    min-height: 100vh;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    margin: 0;
    color: #fff;
  }

  h1 {
    font-size: 28px;
    margin-bottom: 10px;
    text-align: center;
  }

  .container {
    background: rgba(255, 255, 255, 0.1);
    backdrop-filter: blur(10px);
    border-radius: 15px;
    padding: 25px 30px;
    width: 90%;
    max-width: 600px;
    text-align: center;
    box-shadow: 0 4px 25px rgba(0,0,0,0.2);
  }

  input, button {
    font-size: 16px;
    padding: 10px 15px;
    margin: 8px;
    border: none;
    border-radius: 8px;
  }

  input {
    width: 100px;
    text-align: center;
  }

  button {
    cursor: pointer;
    transition: 0.3s;
  }

  .btn-primary {
    background: #0078d7;
    color: white;
  }
  .btn-primary:hover { background: #005fa3; }

  .btn-success {
    background: #28a745;
    color: white;
  }
  .btn-success:hover { background: #218838; }

  .btn-danger {
    background: #dc3545;
    color: white;
  }
  .btn-danger:hover { background: #c82333; }

  .btn-reset {
    background: #ffc107;
    color: #333;
  }
  .btn-reset:hover { background: #e0a800; }

  .queue-box {
    display: flex;
    justify-content: center;
    margin-top: 20px;
    flex-wrap: wrap;
  }

  .queue-element {
    width: 60px;
    height: 60px;
    background: rgba(255,255,255,0.2);
    margin: 5px;
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    font-weight: bold;
    color: #fff;
    border: 2px solid #fff;
    transition: all 0.3s;
  }

  .queue-element.active {
    background: #0078d7;
  }

  .status {
    margin-top: 20px;
    font-size: 18px;
    color: #fff;
  }

  footer {
    margin-top: 30px;
    font-size: 14px;
    opacity: 0.8;
  }
</style>
</head>
<body>

<h1>🎫 Circular Queue Ticket Simulation</h1>

<div class="container">
  <div>
    <input type="number" id="queueSize" placeholder="Queue Size">
    <button class="btn-primary" onclick="createQueue()">Create Queue</button>
  </div>

  <div style="margin-top: 15px;">
    <button class="btn-success" onclick="enqueue()">Issue Ticket</button>
    <button class="btn-danger" onclick="dequeue()">Process Ticket</button>
    <button class="btn-reset" onclick="resetQueue()">Reset Queue</button>
  </div>

  <div class="queue-box" id="queueDisplay"></div>

  <div class="status" id="status">Queue not created yet.</div>
</div>

<script>
  let queue = [];
  let size = 0;
  let front = -1;
  let rear = -1;
  let ticketCounter = 1;

  // --- Load data when page opens ---
  window.onload = function () {
    if (localStorage.getItem("queueData")) {
      const saved = JSON.parse(localStorage.getItem("queueData"));
      queue = saved.queue;
      size = saved.size;
      front = saved.front;
      rear = saved.rear;
      ticketCounter = saved.ticketCounter;
      updateDisplay("♻️ Data restored after refresh");
    }
  };

  function saveState() {
    const data = { queue, size, front, rear, ticketCounter };
    localStorage.setItem("queueData", JSON.stringify(data));
  }

  function clearState() {
    localStorage.removeItem("queueData");
  }

  function createQueue() {
    size = parseInt(document.getElementById("queueSize").value);
    if (isNaN(size) || size <= 0) {
      alert("Please enter a valid queue size!");
      return;
    }
    queue = new Array(size).fill(null);
    front = -1;
    rear = -1;
    ticketCounter = 1;
    saveState();
    updateDisplay("Queue of size " + size + " created ✅");
  }

  function isFull() {
    return (rear + 1) % size === front;
  }

  function isEmpty() {
    return front === -1;
  }

  function enqueue() {
    if (size === 0) {
      alert("Please create a queue first!");
      return;
    }
    if (isFull()) {
      updateDisplay("⚠️ Queue is full!");
      return;
    }
    if (isEmpty()) front = 0;
    rear = (rear + 1) % size;
    const ticket = "T" + ticketCounter++;
    queue[rear] = ticket;
    saveState();
    updateDisplay("✅ Ticket " + ticket + " issued.");
  }

  function dequeue() {
    if (size === 0) {
      alert("Please create a queue first!");
      return;
    }
    if (isEmpty()) {
      updateDisplay("⚠️ No tickets to process!");
      return;
    }
    const ticket = queue[front];
    if (front === rear) {
      front = rear = -1;
    } else {
      front = (front + 1) % size;
    }
    saveState();
    updateDisplay("🎟️ Ticket " + ticket + " processed.");
  }

  function resetQueue() {
    if (confirm("Are you sure you want to reset the queue?")) {
      queue = [];
      size = 0;
      front = -1;
      rear = -1;
      ticketCounter = 1;
      clearState();
      updateDisplay("🔄 Queue has been reset.");
    }
  }

  function updateDisplay(message) {
    const display = document.getElementById("queueDisplay");
    display.innerHTML = "";
    for (let i = 0; i < size; i++) {
      const div = document.createElement("div");
      div.classList.add("queue-element");
      if (queue[i] !== null) {
        div.classList.add("active");
        div.innerText = queue[i];
      }
      display.appendChild(div);
    }
    document.getElementById("status").innerText = message;
  }
</script>

</body>
</html>
