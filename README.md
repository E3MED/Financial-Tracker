<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>Business Entry Tracker</title>
  <style>
    body { font-family: Arial, sans-serif; margin: 20px; background: #f8f9fa; }
    input, select { margin: 5px; padding: 8px; width: 250px; }
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
      <div><input placeholder="Amount Transferred" id="transfer" /></div>
      <div><input placeholder="Paid by (Transfer)" id="transferBy" /></div>
      <div><input placeholder="Shipping Fees" id="shipping" /></div>
      <div><input placeholder="Paid by (Shipping)" id="shippingBy" /></div>
      <div><input placeholder="Clearance Fees" id="clearance" /></div>
      <div><input placeholder="Paid by (Clearance)" id="clearanceBy" /></div>
      <div><input placeholder="Receivable" id="receivable" /></div>
      <div><input placeholder="Stock" id="stock" /></div>
      <div><input placeholder="Debts" id="debts" /></div>
      <div><input placeholder="To be Collected" id="collected" /></div>
      <button type="button" onclick="saveEntry()">Save Entry</button>
      <button type="button" onclick="downloadCSV()">Download Excel</button>
    </form>

    <h3>📂 Saved Entries</h3>
    <table border="1" id="entryTable" cellpadding="5">
      <thead>
        <tr>
          <th>Date</th><th>Transfer</th><th>Paid By</th><th>Shipping</th><th>Paid By</th>
          <th>Clearance</th><th>Paid By</th><th>Receivable</th><th>Stock</th><th>Debts</th>
          <th>To Collect</th><th>Delete</th>
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
        transfer: transfer.value,
        transferBy: transferBy.value,
        shipping: shipping.value,
        shippingBy: shippingBy.value,
        clearance: clearance.value,
        clearanceBy: clearanceBy.value,
        receivable: receivable.value,
        stock: stock.value,
        debts: debts.value,
        collected: collected.value,
        date: new Date().toLocaleString()
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
          <td>${e.debts}</td><td>${e.collected}</td>
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
      let csv = "Date,Transfer,Paid By,Shipping,Paid By,Clearance,Paid By,Receivable,Stock,Debts,To be Collected\n";
      entries.forEach(e => {
        csv += `${e.date},${e.transfer},${e.transferBy},${e.shipping},${e.shippingBy},${e.clearance},${e.clearanceBy},${e.receivable},${e.stock},${e.debts},${e.collected}\n`;
      });
      const blob = new Blob([csv], { type: "text/csv" });
      const link = document.createElement("a");
      link.href = URL.createObjectURL(blob);
      link.download = "business_data.csv";
      link.click();
    }

    // Auto-load table if entries exist and user is logged in (optional)
    if (entries.length) {
      renderTable();
    }
  </script>
</body>
</html>
