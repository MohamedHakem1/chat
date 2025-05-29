function doGet(e) {
  const view = e.parameter.view;

  if (view === "manager") {
    return HtmlService.createHtmlOutputFromFile("ManagerForm");
  } else if (view === "viewer") {
    return HtmlService.createHtmlOutputFromFile("EmployeeViewer");
  } else {
    return HtmlService.createHtmlOutput("❌ الرابط غير صحيح.");
  }
}

function recordOvertime(data) {
  const sheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName("Overtime Counter");
  const rows = sheet.getDataRange().getValues();
  const today = new Date().toLocaleDateString("en-GB");

  for (let i = 1; i < rows.length; i++) {
    if (rows[i][0] === data.name) {
      const rowIndex = i + 1;
      const colIndex = getColumnIndex(data.type);

      if (colIndex !== -1) {
        const currentValue = sheet.getRange(rowIndex, colIndex).getValue() || 0;
        sheet.getRange(rowIndex, colIndex).setValue(currentValue + 1);
        sheet.getRange(rowIndex, 5).setValue(data.manager); // Last Recorded By
        sheet.getRange(rowIndex, 6).setValue(today);        // Last Recorded Date
        sheet.getRange(rowIndex, 8).setValue("Yes");        // Email Sent?

        const email = sheet.getRange(rowIndex, 7).getValue();
        const message = `مرحبًا ${data.name}،\n\nتم تسجيل ${data.type} بواسطة ${data.manager} بتاريخ ${today}.\n\nمع تحيات الإدارة.`;

        MailApp.sendEmail({
          to: email,
          subject: "إشعار بأوفر تايم جديد",
          body: message,
          name: "إدارة الموارد البشرية"
        });

        return;
      }
    }
  }

  throw new Error("❌ لم يتم العثور على الموظف.");
}

function getColumnIndex(type) {
  switch (type) {
    case "Workday OT": return 2;
    case "Weekend OT": return 3;
    case "Public Holiday OT": return 4;
    default: return -1;
  }
}

function getOvertimeSummary() {
  const sheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName("Overtime Counter");
  if (!sheet) return [];

  const data = sheet.getDataRange().getValues();
  if (data.length <= 1) return [];

  data.shift(); // remove header

  return data.map(row => [
    row[0], // Employee Name
    row[1] || 0, // Workday OT
    row[2] || 0, // Weekend OT
    row[3] || 0, // Public Holiday OT
    row[4] || "", // Last Recorded By
    row[5] || ""  // Last Recorded Date
  ]);
}

