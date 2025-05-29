<!DOCTYPE html>
<html>
  <head>
    <base target="_top">
    <style>
      body { font-family: Arial; padding: 20px; max-width: 500px; margin: auto; }
      label, select { display: block; width: 100%; margin-top: 10px; padding: 8px; }
      button { margin-top: 15px; padding: 10px; width: 100%; background: green; color: white; border: none; }
    </style>
  </head>
  <body>
    <h2>تسجيل أوفر تايم</h2>

    <label>اسم الموظف:</label>
    <select id="employeeName">
      <option disabled selected>اختر الموظف</option>
      <option>AHMED BURHAM</option>
      <option>ALI SOLIMAN</option>
      <option>MOHAMED ABDO</option>
    </select>

    <label>نوع الأوفر تايم:</label>
    <select id="overtimeType">
      <option value="Workday OT">بعد يوم العمل</option>
      <option value="Weekend OT">راحة أسبوعية</option>
      <option value="Public Holiday OT">عطلة رسمية</option>
    </select>

    <label>اسم المدير:</label>
    <select id="managerName">
      <option>Ahmed Mostafa</option>
      <option>Sameh Adel</option>
    </select>

    <button onclick="submitForm()">تسجيل</button>
    <div id="status" style="margin-top: 10px;"></div>

    <script>
      function submitForm() {
        const formData = {
          name: document.getElementById("employeeName").value,
          type: document.getElementById("overtimeType").value,
          manager: document.getElementById("managerName").value
        };

        google.script.run
          .withSuccessHandler(() => {
            document.getElementById("status").innerHTML = "✅ تم التسجيل بنجاح.";
          })
          .withFailureHandler(err => {
            document.getElementById("status").innerHTML = "❌ خطأ: " + err.message;
          })
          .recordOvertime(formData);
      }
    </script>
  </body>
</html>
