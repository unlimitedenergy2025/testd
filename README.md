<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>الوصول مسموح فقط داخل الدائرة</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
            padding: 20px;
            background-color: #f5f5f5;
        }
        #message {
            margin-top: 50px;
            padding: 20px;
            background-color: #fff;
            border-radius: 10px;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
            max-width: 500px;
            margin-left: auto;
            margin-right: auto;
        }
        #access-denied {
            color: red;
            font-weight: bold;
        }
        #access-granted {
            color: green;
            font-weight: bold;
        }
    </style>
</head>
<body>
    <div id="message">
        <h1>التحقق من الموقع الجغرافي</h1>
        <p id="status">جاري التحقق من موقعك...</p>
        <div id="access-denied" style="display: none;">
            <p>⚠️ أنت خارج النطاق المسموح به (1 كم من المركز).</p>
            <p>لا يمكنك الوصول إلى هذه الصفحة.</p>
        </div>
        <div id="access-granted" style="display: none;">
            <p>✔ أنت ضمن النطاق المسموح به (1 كم من المركز).</p>
            <p>مرحباً بك! يمكنك الآن تصفح المحتوى.</p>
        </div>
    </div>

    <script>
        // النقطة المركزية (خط العرض، خط الطول)
        const centerLat = 32.22522062281544;
        const centerLng = 36.11083818339768;
        const radiusKm = 1; // نصف القطر 1 كم

        // حساب المسافة بين نقطتين باستخدام Haversine formula
        function getDistance(lat1, lon1, lat2, lon2) {
            const R = 6371; // نصف قطر الأرض بالكيلومتر
            const dLat = (lat2 - lat1) * Math.PI / 180;
            const dLon = (lon2 - lon1) * Math.PI / 180;
            const a =
                Math.sin(dLat / 2) * Math.sin(dLat / 2) +
                Math.cos(lat1 * Math.PI / 180) * Math.cos(lat2 * Math.PI / 180) *
                Math.sin(dLon / 2) * Math.sin(dLon / 2);
            const c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a));
            return R * c; // المسافة بالكيلومتر
        }

        // التحقق من الموقع
        function checkLocation() {
            if (navigator.geolocation) {
                navigator.geolocation.getCurrentPosition(
                    (position) => {
                        const userLat = position.coords.latitude;
                        const userLng = position.coords.longitude;
                        const distance = getDistance(centerLat, centerLng, userLat, userLng);

                        document.getElementById("status").style.display = "none";

                        if (distance <= radiusKm) {
                            document.getElementById("access-granted").style.display = "block";
                            // هنا يمكنك تحميل المحتوى المطلوب
                        } else {
                            document.getElementById("access-denied").style.display = "block";
                            // يمكنك إعادة توجيه المستخدم أو منع الوصول
                        }
                    },
                    (error) => {
                        document.getElementById("status").textContent = "فشل في الحصول على الموقع: " + error.message;
                    }
                );
            } else {
                document.getElementById("status").textContent = "المتصفح لا يدعم الخدمات الجغرافية.";
            }
        }

        // بدء التحقق عند تحميل الصفحة
        window.onload = checkLocation;
    </script>
</body>
</html>
