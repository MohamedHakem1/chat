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

function recordOvertime(formData) {
  const sheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName("Overtime Counter");
  const data = sheet.getDataRange().getValues();
  const today = new Date().toLocaleDateString("en-GB");
  const name = formData.name;
  const type = formData.type;
  const manager = formData.manager;

  const colMap = {
    "Workday OT": 2,
    "Weekend OT": 3,
    "Public Holiday OT": 4
  };

  const colIndex = colMap[type];
  if (!colIndex) throw new Error("نوع الأوفر تايم غير معروف");

  for (let i = 1; i < data.length; i++) {
    if (data[i][0] === name) {
      const row = i + 1;
      const current = sheet.getRange(row, colIndex).getValue() || 0;
      sheet.getRange(row, colIndex).setValue(current + 1);
      sheet.getRange(row, 5).setValue(manager);
      sheet.getRange(row, 6).setValue(today);
      sheet.getRange(row, 8).setValue("Yes");

      const email = sheet.getRange(row, 7).getValue();
      const msg = `مرحبًا ${name}،\nتم تسجيل ${type} بواسطة ${manager} بتاريخ ${today}.\n\nمع تحيات الإدارة.`;

      MailApp.sendEmail(email, "إشعار أوفر تايم", msg);
      return;
    }
  }

  throw new Error("❌ لم يتم العثور على اسم الموظف في الشيت.");
}

function getOvertimeSummary() {
  const sheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName("Overtime Counter");
  const data = sheet.getDataRange().getValues();
  data.shift();

  return data.map(row => [
    row[0], row[1] || 0, row[2] || 0, row[3] || 0, row[4] || "", row[5] || ""
  ]);
}
