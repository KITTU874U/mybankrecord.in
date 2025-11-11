 <!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Ashish Mama Ji - Your Problem Solved</title>
<style>
  body {
    font-family: "Poppins", sans-serif;
    background: linear-gradient(135deg, #e8f0ff, #ffffff);
    margin: 0;
    padding: 20px;
  }
  h1 {
    text-align: center;
    color: #2c3e50;
    background-color: #f9d342;
    padding: 15px;
    border-radius: 10px;
    box-shadow: 0 0 10px #aaa;
  }
  .container {
    max-width: 700px;
    margin: 20px auto;
    background: #fff;
    border-radius: 10px;
    padding: 20px;
    box-shadow: 0 4px 15px rgba(0,0,0,0.1);
  }
  input, button {
    width: 100%;
    margin: 6px 0;
    padding: 10px;
    border-radius: 8px;
    border: 1px solid #ccc;
    font-size: 15px;
  }
  button {
    background-color: #3498db;
    color: white;
    font-weight: bold;
    cursor: pointer;
  }
  button:hover {
    background-color: #2c80b4;
  }
  table {
    width: 100%;
    border-collapse: collapse;
    margin-top: 15px;
  }
  th, td {
    border: 1px solid #ddd;
    padding: 8px;
    text-align: center;
  }
  th {
    background-color: #3498db;
    color: white;
  }
  img {
    width: 60px;
    height: 60px;
    border-radius: 8px;
  }
  .btn-small {
    padding: 5px 10px;
    font-size: 13px;
    margin: 2px;
  }
</style>
</head>
<body>

<h1>🌟 Ashish Mama Ji — Your Problem Solved 🌟</h1>

<div class="container">
  <input type="text" id="name" placeholder="Enter Name">
  <input type="text" id="address" placeholder="Enter Address">
  <input type="text" id="phone" placeholder="Enter Phone Number">
  <input type="text" id="account" placeholder="Enter Account Number">
  <input type="text" id="loan" placeholder="Enter Gold Loan Amount">
  <input type="file" id="photo" accept="image/*">

  <button id="addBtn">Add / Update Entry</button>
  <button onclick="clearAll()">🗑 Clear All</button>
  <button onclick="exportCSV()">⬇️ Export CSV</button>
  <button onclick="exportAllPDF()">📄 Export All to PDF</button>

  <table id="dataTable">
    <thead>
      <tr>
        <th>Photo</th>
        <th>Name</th>
        <th>Address</th>
        <th>Phone</th>
        <th>Account No.</th>
        <th>Gold Loan</th>
        <th>Actions</th>
      </tr>
    </thead>
    <tbody></tbody>
  </table>
</div>

<script>
let editIndex = -1;

function loadData() {
  const data = JSON.parse(localStorage.getItem("bankData") || "[]");
  const table = document.querySelector("#dataTable tbody");
  table.innerHTML = "";
  data.forEach((entry, i) => {
    const row = `<tr>
      <td><img src="${entry.photo}" alt="No Photo"></td>
      <td>${entry.name}</td>
      <td>${entry.address}</td>
      <td>${entry.phone}</td>
      <td>${entry.account}</td>
      <td>${entry.loan}</td>
      <td>
        <button class="btn-small" onclick="editEntry(${i})">✏️ Edit</button>
        <button class="btn-small" onclick="deleteEntry(${i})">🗑 Delete</button>
        <button class="btn-small" onclick="exportSinglePDF(${i})">📄 PDF</button>
      </td>
    </tr>`;
    table.innerHTML += row;
  });
}

document.getElementById("addBtn").onclick = function() {
  const name = document.getElementById("name").value.trim();
  const address = document.getElementById("address").value.trim();
  const phone = document.getElementById("phone").value.trim();
  const account = document.getElementById("account").value.trim();
  const loan = document.getElementById("loan").value.trim();
  const photoInput = document.getElementById("photo");

  if (!name || !address || !phone || !account || !loan) {
    alert("Please fill all fields!");
    return;
  }

  const reader = new FileReader();
  reader.onload = function() {
    let data = JSON.parse(localStorage.getItem("bankData") || "[]");
    const photo = photoInput.files[0] ? reader.result : (editIndex >= 0 ? data[editIndex].photo : "");
    const entry = { name, address, phone, account, loan, photo };

    if (editIndex >= 0) {
      data[editIndex] = entry;
      editIndex = -1;
      document.getElementById("addBtn").textContent = "Add / Update Entry";
    } else {
      data.push(entry);
    }

    localStorage.setItem("bankData", JSON.stringify(data));
    clearInputs();
    loadData();
  };

  if (photoInput.files[0]) reader.readAsDataURL(photoInput.files[0]);
  else reader.onload();
};

function clearInputs() {
  document.querySelectorAll("input[type=text]").forEach(i => i.value = "");
  document.getElementById("photo").value = "";
}

function clearAll() {
  if (confirm("Delete all entries?")) {
    localStorage.removeItem("bankData");
    loadData();
  }
}

function deleteEntry(i) {
  let data = JSON.parse(localStorage.getItem("bankData"));
  data.splice(i, 1);
  localStorage.setItem("bankData", JSON.stringify(data));
  loadData();
}

function editEntry(i) {
  let data = JSON.parse(localStorage.getItem("bankData"));
  const e = data[i];
  document.getElementById("name").value = e.name;
  document.getElementById("address").value = e.address;
  document.getElementById("phone").value = e.phone;
  document.getElementById("account").value = e.account;
  document.getElementById("loan").value = e.loan;
  editIndex = i;
  document.getElementById("addBtn").textContent = "Update Entry";
}

function exportCSV() {
  const data = JSON.parse(localStorage.getItem("bankData") || "[]");
  let csv = "Name,Address,Phone,Account No,Gold Loan\n";
  data.forEach(e => csv += `${e.name},${e.address},${e.phone},${e.account},${e.loan}\n`);
  const blob = new Blob([csv], { type: "text/csv" });
  const a = document.createElement("a");
  a.href = URL.createObjectURL(blob);
  a.download = "bank_data.csv";
  a.click();
}

function exportAllPDF() {
  const data = JSON.parse(localStorage.getItem("bankData") || "[]");
  let newWin = window.open("");
  newWin.document.write("<h2>All Customer Records</h2>");
  newWin.document.write("<table border='1' cellspacing='0' cellpadding='8'><tr><th>Photo</th><th>Name</th><th>Address</th><th>Phone</th><th>Account</th><th>Gold Loan</th></tr>");
  data.forEach(e => {
    newWin.document.write(`<tr>
      <td><img src='${e.photo}' width='50'></td>
      <td>${e.name}</td>
      <td>${e.address}</td>
      <td>${e.phone}</td>
      <td>${e.account}</td>
      <td>${e.loan}</td>
    </tr>`);
  });
  newWin.document.write("</table>");
  newWin.print();
}

function exportSinglePDF(i) {
  const data = JSON.parse(localStorage.getItem("bankData"));
  const e = data[i];
  let newWin = window.open("");
  newWin.document.write(`<h2>${e.name} - Customer Info</h2>`);
  newWin.document.write(`<img src='${e.photo}' width='100'><br><br>`);
  newWin.document.write(`<b>Address:</b> ${e.address}<br>`);
  newWin.document.write(`<b>Phone:</b> ${e.phone}<br>`);
  newWin.document.write(`<b>Account:</b> ${e.account}<br>`);
  newWin.document.write(`<b>Gold Loan:</b> ${e.loan}<br>`);
  newWin.print();
}

loadData();
</script>

</body>
</html>


