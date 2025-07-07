function downloadCSV() {
  let csv = "Date,Transfer,Paid By (T),Shipping,Paid By (S),Clearance,Paid By (C),Receivable,Stock,To be Collected\n";
  let sumTransfer = 0, sumShipping = 0, sumClearance = 0;
  let sumReceivable = 0, sumStock = 0, sumCollected = 0;

  entries.forEach(e => {
    // Split the date string to get just the date part (before the comma)
    const dateParts = e.date.split(',');
    const dateOnly = dateParts[0];
    
    csv += `${dateOnly},${e.transfer},${e.transferBy},${e.shipping},${e.shippingBy},${e.clearance},${e.clearanceBy},${e.receivable},${e.stock},${e.collected}\n`;
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
