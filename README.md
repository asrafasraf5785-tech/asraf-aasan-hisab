<!DOCTYPE html>
<html lang="hi">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>ASRAF आसान हिसाब</title>

<style>
body {
  margin: 0;
  font-family: Arial, sans-serif;
  background: #f1f5f1;
  color: #222;
}
header {
  background: #166534;
  color: white;
  padding: 20px;
  text-align: center;
}
.card {
  background: white;
  margin: 15px;
  padding: 18px;
  border-radius: 15px;
  box-shadow: 0 2px 8px #ddd;
}
h2 {
  margin-top: 0;
}
.balance {
  font-size: 30px;
  font-weight: bold;
  color: #166534;
}
input, select, button {
  width: 100%;
  padding: 13px;
  margin-top: 10px;
  border-radius: 9px;
  border: 1px solid #ccc;
  box-sizing: border-box;
  font-size: 16px;
}
button {
  background: #166534;
  color: white;
  border: none;
  font-weight: bold;
}
.expense {
  background: #b91c1c;
}
.entry {
  padding: 12px 0;
  border-bottom: 1px solid #ddd;
}
.green {
  color: green;
}
.red {
  color: red;
}
</style>
</head>

<body>

<header>
  <h1>ASRAF आसान हिसाब</h1>
  <p>ASRAF GROUP</p>
</header>

<div class="card">
  <h2>कुल बैलेंस</h2>
  <div class="balance" id="balance">₹0</div>
</div>

<div class="card">
  <h3>आज का हिसाब</h3>
  <p>आमदनी: <b id="income">₹0</b></p>
  <p>खर्च: <b id="expense">₹0</b></p>
</div>

<div class="card">
  <h2>नया हिसाब जोड़ें</h2>

  <select id="type">
    <option value="income">पैसा आया</option>
    <option value="expense">पैसा गया</option>
  </select>

  <input id="amount" type="number"
    placeholder="रकम लिखें ₹">

  <input id="note" type="text"
    placeholder="किसलिए पैसा आया या गया?">

  <button onclick="addEntry()">
    हिसाब सेव करें
  </button>
</div>

<div class="card">
  <h2>हिसाब की सूची</h2>
  <div id="list"></div>
</div>

<script>
let records = JSON.parse(
  localStorage.getItem("asraf_records") || "[]"
);

function save() {
  localStorage.setItem(
    "asraf_records",
    JSON.stringify(records)
  );
  render();
}

function addEntry() {
  let type = document.getElementById("type").value;
  let amount = Number(
    document.getElementById("amount").value
  );
  let note = document.getElementById("note").value;

  if (amount <= 0) {
    alert("सही रकम लिखें");
    return;
  }

  records.push({
    type: type,
    amount: amount,
    note: note,
    date: new Date().toLocaleDateString("hi-IN")
  });

  save();

  document.getElementById("amount").value = "";
  document.getElementById("note").value = "";
}

function removeEntry(index) {
  if (confirm("क्या यह हिसाब हटाना है?")) {
    records.splice(index, 1);
    save();
  }
}

function render() {
  let income = 0;
  let expense = 0;
  let list = "";

  records.forEach((item, index) => {
    if (item.type === "income") {
      income += item.amount;
    } else {
      expense += item.amount;
    }

    let sign = item.type === "income" ? "+" : "-";
    let color = item.type === "income" ? "green" : "red";

    list += `
      <div class="entry">
        <b>${item.note || "बिना विवरण"}</b>
        <p class="${color}">
          ${sign} ₹${item.amount}
        </p>
        <small>${item.date}</small>
        <button class="expense"
          onclick="removeEntry(${index})">
          हटाएं
        </button>
      </div>
    `;
  });

  document.getElementById("income").innerText =
    "₹" + income;

  document.getElementById("expense").innerText =
    "₹" + expense;

  document.getElementById("balance").innerText =
    "₹" + (income - expense);

  document.getElementById("list").innerHTML =
    list || "अभी कोई हिसाब नहीं है।";
}

render();
</script>

</body>
</html>
