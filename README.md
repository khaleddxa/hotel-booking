<!DOCTYPE html>
<html lang="ar">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>حجز فندق</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.17.4/xlsx.full.min.js"></script>
</head>
<body>
    <h2>نموذج الحجز</h2>

    <label for="name">الاسم:</label>
    <input type="text" id="name">

    <label for="checkin">تاريخ الوصول:</label>
    <input type="date" id="checkin">
    <span id="checkin-day"></span>

    <label for="checkout">تاريخ المغادرة:</label>
    <input type="date" id="checkout">
    <span id="checkout-day"></span>

    <label for="price">السعر لليلة:</label>
    <input type="number" id="price" value="300" readonly> ريال  

    <h3>الإجمالي: <span id="total-price">0</span> ريال</h3>

    <button onclick="saveBooking()">حفظ الحجز</button>
    <button onclick="exportToExcel()">تصدير إلى Excel</button>

    <h3>الحجوزات:</h3>
    <table border="1" id="booking-table">
        <thead>
            <tr>
                <th>الاسم</th>
                <th>تاريخ الوصول</th>
                <th>اليوم</th>
                <th>تاريخ المغادرة</th>
                <th>اليوم</th>
                <th>عدد الليالي</th>
                <th>الإجمالي (ريال)</th>
            </tr>
        </thead>
        <tbody>
        </tbody>
    </table>

    <script>
        let bookings = [];

        function updateTotal() {
            let checkin = new Date(document.getElementById("checkin").value);
            let checkout = new Date(document.getElementById("checkout").value);
            let pricePerNight = parseInt(document.getElementById("price").value);

            if (!isNaN(checkin) && !isNaN(checkout) && checkout > checkin) {
                let nights = (checkout - checkin) / (1000 * 60 * 60 * 24);
                document.getElementById("total-price").innerText = nights * pricePerNight;
            } else {
                document.getElementById("total-price").innerText = "0";
            }
        }

        function updateDay(id, outputId) {
            let date = new Date(document.getElementById(id).value);
            if (!isNaN(date)) {
                let days = ["الأحد", "الإثنين", "الثلاثاء", "الأربعاء", "الخميس", "الجمعة", "السبت"];
                document.getElementById(outputId).innerText = days[date.getDay()] + " - " + date.toLocaleDateString("ar-EG");
            } else {
                document.getElementById(outputId).innerText = "";
            }
        }

        function saveBooking() {
            let name = document.getElementById("name").value;
            let checkin = document.getElementById("checkin").value;
            let checkout = document.getElementById("checkout").value;
            let price = parseInt(document.getElementById("total-price").innerText);

            if (!name || !checkin || !checkout || price === 0) {
                alert("يرجى ملء جميع الحقول بشكل صحيح!");
                return;
            }

            let checkinDate = new Date(checkin);
            let checkoutDate = new Date(checkout);
            let nights = (checkoutDate - checkinDate) / (1000 * 60 * 60 * 24);

            let days = ["الأحد", "الإثنين", "الثلاثاء", "الأربعاء", "الخميس", "الجمعة", "السبت"];
            let checkinDay = days[checkinDate.getDay()];
            let checkoutDay = days[checkoutDate.getDay()];

            let booking = {
                name: name,
                checkin: checkin,
                checkinDay: checkinDay,
                checkout: checkout,
                checkoutDay: checkoutDay,
                nights: nights,
                total: price
            };

            bookings.push(booking);
            updateTable();
        }

        function updateTable() {
            let tableBody = document.getElementById("booking-table").getElementsByTagName("tbody")[0];
            tableBody.innerHTML = "";

            bookings.forEach(booking => {
                let row = tableBody.insertRow();
                row.insertCell(0).innerText = booking.name;
                row.insertCell(1).innerText = booking.checkin;
                row.insertCell(2).innerText = booking.checkinDay;
                row.insertCell(3).innerText = booking.checkout;
                row.insertCell(4).innerText = booking.checkoutDay;
                row.insertCell(5).innerText = booking.nights;
                row.insertCell(6).innerText = booking.total;
            });
        }

        function exportToExcel() {
            if (bookings.length === 0) {
                alert("لا توجد حجوزات لتصديرها!");
                return;
            }

            let ws = XLSX.utils.json_to_sheet(bookings);
            let wb = XLSX.utils.book_new();
            XLSX.utils.book_append_sheet(wb, ws, "الحجوزات");

            XLSX.writeFile(wb, "حجوزات_الفندق.xlsx");
        }

        document.getElementById("checkin").addEventListener("change", function() {
            updateTotal();
            updateDay("checkin", "checkin-day");
        });

        document.getElementById("checkout").addEventListener("change", function() {
            updateTotal();
            updateDay("checkout", "checkout-day");
        });
    </script>
</body>
</html>
