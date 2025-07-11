
<html lang="en">

<head>

  <meta charset="UTF-8">

  <title>Business Tracker (Firebase)</title>

  <style>

    body { font-family: Arial, sans-serif; margin: 20px; }

    input { margin: 5px; padding: 8px; width: 250px; }

    button { padding: 8px 14px; margin: 5px; cursor: pointer; }

    table { border-collapse: collapse; margin-top: 20px; width: 100%; }

    th, td { border: 1px solid #ddd; padding: 8px; text-align: center; }

    #loadingStatus { color: #666; font-style: italic; }

  </style>

</head>

<body>



<!-- Login Section -->

<div id="loginSection">

  <h2>🔐 Login</h2>

  <input type="text" id="username" placeholder="Username" value="e3med"><br>

  <input type="password" id="password" placeholder="Password" value="e3med2025+"><br>

  <button onclick="checkLogin()">Login</button>

  <p id="loginMessage" style="color:red;"></p>

</div>



<!-- Main App -->

<div id="appSection" style="display:none;">

  <h2>📊 Business Tracker</h2>

  <form id="entryForm">

    <input placeholder="Amount Transferred" id="transfer"><br>

    <input placeholder="Paid by (Transfer)" id="transferBy"><br>

    <input placeholder="Shipping Fees" id="shipping"><br>

    <input placeholder="Paid by (Shipping)" id="shippingBy"><br>

    <input placeholder="Clearance Fees" id="clearance"><br>

    <input placeholder="Paid by (Clearance)" id="clearanceBy"><br>

    <input placeholder="Receivable" id="receivable"><br>

    <input placeholder="Stock" id="stock"><br>

    <input placeholder="To be Collected" id="collected"><br>

    <button type="button" onclick="saveEntry()">Save Entry</button>

    <button type="button" onclick="downloadCSV()">Download Excel</button>

  </form>



  <h3>📂 Entries <span id="loadingStatus"></span></h3>

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



<!-- Firebase SDK -->

<script src="https://www.gstatic.com/firebasejs/9.6.0/firebase-app-compat.js"></script>

<script src="https://www.gstatic.com/firebasejs/9.6.0/firebase-database-compat.js"></script>



<script>

 // Import the functions you need from the SDKs you need
import { initializeApp } from "firebase/app";
import { getAnalytics } from "firebase/analytics";
// TODO: Add SDKs for Firebase products that you want to use
// https://firebase.google.com/docs/web/setup#available-libraries

// Your web app's Firebase configuration
// For Firebase JS SDK v7.20.0 and later, measurementId is optional
const firebaseConfig = {
  apiKey: "AIzaSyBjZjNOWQwREWbNiPK5PTUHqDkiWuEim_g",
  authDomain: "e3med-finance.firebaseapp.com",
  databaseURL: "https://e3med-finance-default-rtdb.europe-west1.firebasedatabase.app",
  projectId: "e3med-finance",
  storageBucket: "e3med-finance.firebasestorage.app",
  messagingSenderId: "386561578213",
  appId: "1:386561578213:web:2dffa095390d44c2ef700c",
  measurementId: "G-QFHGC17P36"
};

// Initialize Firebase
const app = initializeApp(firebaseConfig);
const analytics = getAnalytics(app);

  };



  // Initialize Firebase

  const app = firebase.initializeApp(firebaseConfig);

  const db = firebase.database();



  let entries = [];



  // 🔄 Load entries from Firebase

  function loadEntries() {

    document.getElementById('loadingStatus').textContent = "Loading...";

    db.ref('businessEntries').on('value', (snapshot) => {

      entries = snapshot.val() || [];

      renderTable();

      document.getElementById('loadingStatus').textContent = "";

    }, (error) => {

      console.error("Firebase error:", error);

      document.getElementById('loadingStatus').textContent = "Error loading data!";

    });

  }



function checkLogin() {
    const username = document.getElementById('username').value.trim(); // Add .trim()
    const password = document.getElementById('password').value.trim(); // Add .trim()

    console.log("Entered Username (trimmed):", username); // Changed console.log
    console.log("Entered Password (trimmed):", password); // Changed console.log
    console.log("Expected Username:", "e3med");
    console.log("Expected Password:", "e3med2025+");

    if (username === "e3med" && password === "e3med2025+") {
        console.log("Login successful!");
        document.getElementById('loginSection').style.display = 'none';
        document.getElementById('appSection').style.display = 'block';
        loadEntries();
    } else {
        document.getElementById('loginMessage').textContent = "Invalid credentials!";
        console.log("Login failed: Mismatch in credentials.");
    }
}



  function saveEntry() {

    const newEntry = {

      date: new Date().toLocaleDateString(),

      transfer: parseFloat(document.getElementById('transfer').value) || 0,

      transferBy: document.getElementById('transferBy').value,

      shipping: parseFloat(document.getElementById('shipping').value) || 0,

      shippingBy: document.getElementById('shippingBy').value,

      clearance: parseFloat(document.getElementById('clearance').value) || 0,

      clearanceBy: document.getElementById('clearanceBy').value,

      receivable: parseFloat(document.getElementById('receivable').value) || 0,

      stock: parseFloat(document.getElementById('stock').value) || 0,

      collected: parseFloat(document.getElementById('collected').value) || 0

    };



    entries.push(newEntry);

    db.ref('businessEntries').set(entries)

      .then(() => {

        alert('Entry saved successfully!');

        document.getElementById('entryForm').reset();

      })

      .catch(error => {

        alert('Error saving: ' + error.message);

      });

  }



  function deleteEntry(index) {

    if (confirm('Delete this entry?')) {

      entries.splice(index, 1);

      db.ref('businessEntries').set(entries)

        .catch(error => alert('Delete failed: ' + error.message));

    }

  }



  function renderTable() {

    const tbody = document.querySelector('#entryTable tbody');

    tbody.innerHTML = '';

    

    entries.forEach((entry, index) => {

      const row = document.createElement('tr');

      row.innerHTML = `

        <td>${entry.date}</td>

        <td>${entry.transfer}</td><td>${entry.transferBy}</td>

        <td>${entry.shipping}</td><td>${entry.shippingBy}</td>

        <td>${entry.clearance}</td><td>${entry.clearanceBy}</td>

        <td>${entry.receivable}</td><td>${entry.stock}</td>

        <td>${entry.collected}</td>

        <td><button onclick="deleteEntry(${index})">🗑️</button></td>

      `;

      tbody.appendChild(row);

    });

  }



  function downloadCSV() {

    let csv = "Date,Transfer,Paid By,Shipping,Paid By,Clearance,Paid By,Receivable,Stock,To Collect\n";

    

    entries.forEach(entry => {

      csv += `${entry.date},${entry.transfer},"${entry.transferBy}",${entry.shipping},"${entry.shippingBy}",${entry.clearance},"${entry.clearanceBy}",${entry.receivable},${entry.stock},${entry.collected}\n`;

    });



    const blob = new Blob([csv], { type: 'text/csv' });

    const url = URL.createObjectURL(blob);

    const a = document.createElement('a');

    a.href = url;

    a.download = 'business_data.csv';

    a.click();

  }

</script>

</body>

</html>
