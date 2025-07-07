<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>Business Entry Tracker</title>
  <style>
    body { font-family: Arial, sans-serif; margin: 20px; background: #f9f9f9; }
    input, select { margin: 5px; padding: 8px; width: 250px; }
    button { margin-top: 10px; padding: 10px 15px; cursor: pointer; }
    h2 { color: #333; }
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
  </div>

  <script>
    let entries = JSON.parse(localStorage.getItem("entries") || "[]");

    function checkLogin() {
      const user = document.getElementById("username").value;
      const pass = document.getElementById("password").value;
      if (user === "e3med" && pass === "e3med2025+") {
        document.getElementById("loginSection").style.display = "none";
        document.getElementById("appSection").style.display = "block";
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
  </script>
</body>
</html>
