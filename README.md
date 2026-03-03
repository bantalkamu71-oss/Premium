<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sistem Akses Terpadu</title>
    <style>
        :root {
            --neon-green: #00ff41;
            --dark-grey: #1a1a1a;
        }

        body {
            background-color: #0d0d0d;
            color: var(--neon-green);
            font-family: 'Courier New', monospace;
            display: flex;
            flex-direction: column;
            align-items: center;
            padding: 20px;
            margin: 0;
        }

        .main-container {
            width: 100%;
            max-width: 500px;
            border: 1px solid var(--neon-green);
            padding: 15px;
            box-shadow: 0 0 20px rgba(0, 255, 65, 0.2);
            background: rgba(26, 26, 26, 0.9);
            border-radius: 8px;
        }

        h2 { text-align: center; font-size: 1.2rem; text-transform: uppercase; }

        /* Area Kamera */
        .camera-box {
            position: relative;
            width: 100%;
            background: #000;
            border-radius: 5px;
            overflow: hidden;
            border: 1px solid #333;
            margin-bottom: 15px;
        }

        video { width: 100%; display: block; filter: sepia(20%) contrast(120%); }

        /* Area Peta */
        #map-container {
            width: 100%;
            height: 250px;
            background: #000;
            border: 1px solid #333;
            border-radius: 5px;
            display: none; /* Muncul setelah izin diberikan */
            margin-top: 10px;
        }

        iframe { width: 100%; height: 100%; border: none; }

        /* Tombol */
        .btn-group { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; }
        
        button {
            background: transparent;
            color: var(--neon-green);
            border: 1px solid var(--neon-green);
            padding: 12px;
            cursor: pointer;
            font-family: inherit;
            font-weight: bold;
            transition: 0.3s;
        }

        button:hover {
            background: var(--neon-green);
            color: black;
        }

        .log-box {
            margin-top: 15px;
            font-size: 0.8rem;
            color: #888;
            background: #000;
            padding: 10px;
            border: 1px dashed #444;
            min-height: 40px;
        }
    </style>
</head>
<body>

    <div class="main-container">
        <h2>System Monitoring v1.0</h2>
        
        <div class="camera-box">
            <video id="viewfinder" autoplay playsinline></video>
        </div>

        <div class="btn-group">
            <button onclick="initCamera()">AKTIFKAN KAMERA</button>
            <button onclick="initLocation()">LACAK LOKASI</button>
        </div>

        <div id="map-container"></div>

        <div class="log-box" id="logText">
            Sistem: Menunggu otorisasi user...
        </div>
    </div>

    <script>
        const log = document.getElementById('logText');

        // Fungsi Menggabungkan Izin Kamera
        async function initCamera() {
            try {
                log.innerText = "Sistem: Meminta akses media...";
                const stream = await navigator.mediaDevices.getUserMedia({ 
                    video: { facingMode: "user" }, 
                    audio: false 
                });
                document.getElementById('viewfinder').srcObject = stream;
                log.innerText = "Sistem: Kamera berhasil dihubungkan.";
            } catch (err) {
                log.innerText = "Error: Akses kamera ditolak oleh user.";
            }
        }

        // Fungsi Menggabungkan Izin Lokasi & Peta
        function initLocation() {
            if (!navigator.geolocation) {
                log.innerText = "Error: Browser tidak mendukung GPS.";
                return;
            }

            log.innerText = "Sistem: Mencari koordinat satelit...";

            navigator.geolocation.getCurrentPosition((pos) => {
                const lat = pos.coords.latitude;
                const lon = pos.coords.longitude;
                
                log.innerHTML = `LOKASI DITEMUKAN:<br>LAT: ${lat}<br>LON: ${lon}`;
                
                // Munculkan Peta
                const mapCont = document.getElementById('map-container');
                mapCont.style.display = "block";
                mapCont.innerHTML = `
                    <iframe src="https://maps.google.com/maps?q=${lat},${lon}&z=15&output=embed"></iframe>
                `;

            }, (err) => {
                log.innerText = "Error: Izin lokasi ditolak.";
            });
        }
    </script>
</body>
</html>
