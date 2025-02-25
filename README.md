<!DOCTYPE html>
<html lang="ar">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>حجز الجلسات بالساعة</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
            direction: rtl;
            margin: 20px;
        }
        form {
            max-width: 400px;
            margin: auto;
            background: #f4f4f4;
            padding: 20px;
            border-radius: 10px;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
        }
        input, select, button {
            width: 100%;
            padding: 10px;
            margin: 10px 0;
            border: 1px solid #ccc;
            border-radius: 5px;
        }
        button {
            background: #007bff;
            color: white;
            font-size: 16px;
        }
    </style>
</head>
<body>

    <h2>حجز الجلسات بالساعة</h2>
    
    <form id="bookingForm">
        <label for="date">التاريخ:</label>
        <input type="date" id="date" required>

        <label for="checkIn">وقت الدخول:</label>
        <input type="time" id="checkIn" required>

        <label for="checkOut">وقت الخروج:</label>
        <input type="time" id="checkOut" required>

        <label for="people">عدد الأشخاص:</label>
        <input type="number" id="people" min="1" required>

        <label for="price">السعر:</label>
        <input type="text" id="price" readonly>

        <button type="button" onclick="calculatePrice()">احسب السعر</button>
        <button type="submit">إتمام الحجز</button>
    </form>

    <script>
        function calculatePrice() {
            let checkIn = document.getElementById("checkIn").value;
            let checkOut = document.getElementById("checkOut").value;
            
            if (!checkIn || !checkOut) {
                alert("يرجى اختيار وقت الدخول والخروج.");
                return;
            }

            let inTime = new Date(`1970-01-01T${checkIn}:00`);
            let outTime = new Date(`1970-01-01T${checkOut}:00`);
            let diffHours = (outTime - inTime) / 3600000;

            if (diffHours <= 0) {
                alert("وقت الخروج يجب أن يكون بعد وقت الدخول.");
                return;
            }

            let price = diffHours >= 4 ? 250 : diffHours * 99;
            document.getElementById("price").value = `${price} ريال`;
        }
    </script>

</body>
</html>
