<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>Business Entry Tracker</title>
  <style>
    body { font-family: Arial, sans-serif; margin: 20px; background: #f8f9fa; }
    input { margin: 5px; padding: 8px; width: 250px; }
    button { margin-top: 10px; padding: 8px 14px; cursor: pointer; }
    table { border-collapse: collapse; margin-top: 20px; width: 100%; background: #fff; }
    th, td { border: 1px solid #ccc; padding: 6px 10px; text-align: center; font-size: 14px; }
    h2, h3 { color: #333; }
  </style>
</head>
<body>

<!-- 🔐 Login Section -->
<div id="loginSection">
  <h2>🔒 Login</h2>
  <input type="text" id="username" placeholder="User Name" /><br>
  <input type="password" id="password" placeholder="Password" /><br>
  <button onclick="checkLogin()">Login</button>
  <p id="loginMessage" style="color: red;"></p>
</div>

<!-- 📊 App Section -->
<div id="appSection" style="display:none;">
  <h2>📊 Business Entry Tracker</h2>
  <form id="entryForm">
    <input placeholder="Amount Transferred" id="transfer" /><br>
    <input placeholder="Paid by (Transfer)" id="transferBy" /><br>
    <input placeholder="Shipping Fees" id="shipping" /><br>
    <input placeholder="Paid by (Shipping)" id="shippingBy" /><br>
    <input placeholder="Clearance Fees" id="clearance" /><br>
    <input placeholder="Paid by (Clearance)" id="clearanceBy" /><br>
    <input placeholder="Receivable" id="receivable" /><br>
    <input placeholder="Stock" id="stock" /><br>
    <input placeholder="To be Collected" id="collected" /><br>
    <button type="button" onclick="saveEntry()">Save Entry</button>
    <button type="button" onclick="downloadCSV()">Download Excel</button>
  </form>

  <h3>📂 Saved Entries</h3>
  <table id="entryTable">
    <thead>
      <tr>
        <th>Date</th><th>Transfer</th><th>Paid By</th>
        <th>Shipping</th><th>Paid By</th>
        <th>Clearance</th><th>Paid By</th>
        <th>Receivable</th><th>Stock</th><th>To Collect</th>
        <th>Delete</th>
      </tr>
    </thead>
    <tbody></tbody>
  </table>
</div>

<script>
  let entries = JSON.parse(localStorage.getItem("entries") || "[]");

  function checkLogin() {
    const user = document.getElementById("username").value;
    const pass = document.getElementById("password").value;
    if (user === "e3med" && pass === "e3med2025+") {
      document.getElementById("loginSection").style.display = "none";
      document.getElementById("appSection").style.display = "block";
      renderTable();
    } else {
      document.getElementById("loginMessage").textContent = "Incorrect username or password.";
    }
  }

  function saveEntry() {
    const data = {
      transfer: parseFloat(transfer.value) || 0,
      transferBy: transferBy.value,
      shipping: parseFloat(shipping.value) || 0,
      shippingBy: shippingBy.value,
      clearance: parseFloat(clearance.value) || 0,
      clearanceBy: clearanceBy.value,
      receivable: parseFloat(receivable.value) || 0,
      stock: parseFloat(stock.value) || 0,
      collected: parseFloat(collected.value) || 0,
      date: new Date().toLocaleDateString() // Changed to store only date without time
    };
    entries.push(data);
    localStorage.setItem("entries", JSON.stringify(entries));
    alert("✅ Entry saved!");
    renderTable();
    document.getElementById("entryForm").reset();
  }

  function renderTable() {
    const tbody = document.querySelector("#entryTable tbody");
    tbody.innerHTML = "";
    entries.forEach((e, index) => {
      const row = document.createElement("tr");
      row.innerHTML = `
        <td>${e.date}</td>
        <td>${e.transfer}</td><td>${e.transferBy}</td>
        <td>${e.shipping}</td><td>${e.shippingBy}</td>
        <td>${e.clearance}</td><td>${e.clearanceBy}</td>
        <td>${e.receivable}</td><td>${e.stock}</td>
        <td>${e.collected}</td>
        <td><button onclick="deleteEntry(${index})">🗑️</button></td>
      `;
      tbody.appendChild(row);
    });
  }

  function deleteEntry(index) {
    if (confirm("Are you sure you want to delete this entry?")) {
      entries.splice(index, 1);
      localStorage.setItem("entries", JSON.stringify(entries));
      renderTable();
    }
  }

  function downloadCSV() {
    let csv = "Date,Transfer,Paid By (T),Shipping,Paid By (S),Clearance,Paid By (C),Receivable,Stock,To be Collected\n";
    let sumTransfer = 0, sumShipping = 0, sumClearance = 0;
    let sumReceivable = 0, sumStock = 0, sumCollected = 0;

    entries.forEach(e => {
      csv += `${e.date},${e.transfer},${e.transferBy},${e.shipping},${e.shippingBy},${e.clearance},${e.clearanceBy},${e.receivable},${e.stock},${e.collected}\n`;
      sumTransfer += e.transfer;
      sumShipping += e.shipping;
      sumClearance += e.clearance;
      sumReceivable += e.receivable;
      sumStock += e.stock;
      sumCollected += e.collected;
    });

    const totalOut = sumTransfer + sumShipping + sumClearance;
    const totalIn = sumReceivable + sumStock + sumCollected;
    const net = totalIn - totalOut;

    csv += "\n"; // Empty line before summary

    // Totals under their correct columns
    csv += `Grand Total (Outgoing Totals),${sumTransfer},,,${sumShipping},,,${sumClearance}\n`;
    csv += `Grand Total (Incoming Totals),,,,,,,${sumReceivable},${sumStock},${sumCollected}\n`;
    csv += `,,,,,,,\n`;
    csv += `Net Total (Incoming - Outgoing),,,,,,,${net}\n`;

    const blob = new Blob([csv], { type: "text/csv" });
    const link = document.createElement("a");
    link.href = URL.createObjectURL(blob);
    link.download = "business_data.csv";
    link.click();
  }

  if (entries.length) {
    renderTable();
  }
</script>

</body>
</html>
